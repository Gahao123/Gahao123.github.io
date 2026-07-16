---
title: C++知识
date: 2025-10-15 14:13:56
categories:
  - 信息保存
tags:
  - 报告开发tip
  - C/C++
  - git
  - Linux
  - 命令行
  - DRAM
  - 内存条物理层知识
---

## 生成报告tip(重要)
测试的时候可以将生成的文件名固定,因为这样会覆盖,减少翻文件的耗时
但是实际输出报告啥的时候**最好加上时间戳**,便于溯源和查看历史
RowHammer最后的报告把掉电翻转删掉

<!-- more -->

## cc和cpp文件
都是C++文件，cc在Linux上常用，cpp在Windows上常用

## C++开发使用
Qt项目+数据库Sqlite(这个是纯C数据库)
SSH连开发板:MobaXterm , 直接去官网下 -> 在PC上`ssh root@IP地址`,可以拖文件+开多个终端,相当于Windows直接打开板子命令行,可以Windows上MobaXterm拖文件到板子上,然后通过SSH在板子上编译运行
【注意】MobaXterm左边那个文件夹目录里不能打开终端,想在终端里改结构只能cd

## .h文件头都是会有ifndef的
最结尾有endif，主要是为了宏保护，在编译中防止重复包含

## typedef，uint8_t\uint_16_t\uint32_t\uint64_t
相当于官方制定的按别名重定义的数据类型
把不同机器下被赋予不同的长度以保证，不同机器下int64_t的大小相同，而不会因为机器本身出现字长不同
要用就是
int16_t;int32_t;int64_t;这些是有符号数
uint16_t;uint32_t;uint64_t;这些是无符号数
格式化输出：
```
uint16_t -> %hu
uint32_t -> %u
uint64_t -> %llu
```

## ISA -> 指令集
就是x86,x86-64,ARM这些

## NDA就是保密协议的意思

## 交叉编译
以下这些情况需要交叉编译,注意交叉编译是为了快,小东西也可直接MobaXterm拖文件在板子上编译:
在x86电脑上编大型RISC-V程序 ; 编Linux kernel/driver ; 编大型项目(比如 AI runtime)
具体是x86Linux系统电脑写代码(如果是Windows系统则还需要WSL)+交叉编译,MobaXterm SSH登录+传文件,RISC-V板子运行程序+perf分析
工具链是`riscv64-linux-gnu-g++`
【打开网络共享设置命令】win+r输入`ncpa.cpl`
【PC上看网线连的板子的IP的命令】`arp -a`，找板子对应的物理地址即可
【板子上看板子IP】`ip a`，找插网线的那个接口，如eth0

## WSL,即Windows Subsystem for Linux
是Windows10中的一个功能强大的子系统，它允许用户在Windows环境中直接运行Linux操作系统和应用程序。这意味着，无需设置复杂的虚拟机或进行双系统启动，开发人员和用户就可以在Windows上使用Linux的各种命令行工具、实用程序和应用
WSL支持Windows和Linux文件系统之间的互访，用户可以在Linux环境中访问Windows文件系统，反之亦然。此外，WSL还允许在Windows命令行中执行Linux命令，以及在Linux环境中运行Windows应用程序。
【安装】在PowerShell（管理员）里`wsl --install`,默认装一个Ubuntu,然后重启
WSL挂载命令`sshfs root@192.168.137.000:/root /tmp/test` 【注意WSL的/tmp文件夹每次开机都会完全清空,所以需要去/tmp下重建/test文件夹再挂载】
【WSL关机】在cmd里`wsl --shutdown`,然后去再打开

## buffer -> 缓冲v. ; 缓冲区,缓存,缓冲器,n.

## ION的mmap
指的是映射内存，即把一块物理内存映射到进程的虚拟地址空间，进行读写
调用mmap就是将物理内存映射到进程虚拟空间
Linux中的mmap函数:允许用户将一个文件或其他对象映射到内存中。这样，对这段内存的读写操作就相当于对文件本身的读写，而无需再调用`read()`和`write()`系统调用,原型如下:`void* mmap(void* start, size_t length, int prot, int flags, int fd, off_t offset);`start指向欲映射的内存起始地址，通常设为NULL，代表让系统自动选定地址;prot映射区域的保护方式，可以是`PROT_EXEC`、`PROT_READ`、`PROT_WRITE`、`PROT_NONE`的组合;flags影响映射区域的各种特性，必须指定`MAP_SHARED`或`MAP_PRIVATE`;fd要映射到内存中的文件描述符，通常指定为-1，即用系统默认的分配函数，但是也可以自己指定fd，就可以调用自己的分配函数，比如指定fd为自己弄的内核模块文件啥的;offset文件映射的偏移量，通常设置为0，代表从文件最前方开始对应，只不过没有数据擦除而已(即上一个使用这块的场地没有进行清理而已，不太影响);成功执行时，mmap返回被映射区的内存起始地址；失败则返回`MAP_FAILED(-1)`，错误原因存于errno中
> mmap通过创建一个新的虚拟内存区域，并将其与文件的物理磁盘地址相连，实现文件磁盘地址和进程虚拟地址空间中一段虚拟地址的一一对应关系
> mmap的映射可分为三个阶段:1.进程启动映射过程，并在虚拟地址空间中为映射创建虚拟映射区域;2.调用内核空间的系统调用函数mmap，实现文件物理地址和进程虚拟地址的一一映射关系;3.进程发起对这片映射空间的访问，引发缺页异常，实现文件内容到物理内存（主存）的拷贝
> 映射完直接按数组访问即可访存，比如得到的地址是map，修改就是`mapped[43].integer = 243;`即可
> 创建完之后解除映射:`munmap`

## 物理帧号PFN
Linux内核为每个进程提供`/proc/[pid]/pagemap`文件，用于查看每个虚拟页是否存在和对应的物理帧号(PFN),关系式为`物理地址 = PFN × PAGE_SIZE + (虚拟地址 % PAGE_SIZE)`。内核出于安全考虑默认会禁用普通用户读取物理页号的能力，需要root才能看到真实PFN，否则全为0或拒绝访问(只要看到物理页全0就是拒绝访问了)

## 日常流程
git的流程就是:本地更新主干，基于主干新建一个分支并切换 -> Coding -> 将这个分支推送到远端 -> 打开仓库,进入创建Pull Request界面 -> 选定目标分支,填写Title以及Description,点击提交Pull Request
`git pull <远程仓库名origin> <要拉取的分支名main或master>`;从远程仓库获取最新代码并合并到当前分支
`git add 文件名`(`git add .`是所有文件)添加到暂存区
`git commit -m "xxxxx” -m`后面的文本是本次提交的注释信息
`git checkout -b 分支名称`。若已有分支，则可使用命令 `git switch 分支名称`
查看本地分支:`git branch`;远程分支:`git branch -r`;所有分支:`git branch -a`；其中标*的是当前所在分支
不能直接写main，就得创建一个分支，然后写到分支里面，后发起合并请求，让审核人来合并;分支可以创建一个自己的分支然后每次都在那个里面搞就行，或者每次创建一个也可以
`git push origin 分支名` 推送到远程仓库，基本格式如下:
`git push <远程主机名> <本地分支名>:<远程分支名>`
如果本地分支名和远程分支名相同，可以省略冒号和远程分支名：
`git push <远程主机名> <本地分支名>`
> 命令行下按Tab键可以进行自动代码补全(若有多个补全选项,补的是哪个不确定)

## 服务器换IP修改地址
- 先`git remote -v`，可能会看到类似`origin  http://旧IP:33333/RISC-V_Security/riscv-security-docs.git (fetch)`和`origin  http://旧IP:33333/RISC-V_Security/riscv-security-docs.git (push)`
- 然后修改为新的地址`git remote set-url origin http://新IP:33333/RISC-V_Security/riscv-security-docs.git`
- 再确认一下`git remote -v`，应该变成`origin  http://新IP:33333/RISC-V_Security/riscv-security-docs.git (fetch)`和`origin  http://新IP:33333/RISC-V_Security/riscv-security-docs.git (push)`
- 最后测试`git fetch`或者`git pull`，如果能正常拉取说明修改成功

## 查看全局用户名和邮箱，适用于所有Git仓库
`git config --global user.name`
`git config --global user.email`

## 查看当前仓库用户名，查看某个特定仓库的用户名
`git config user.name`
`git config user.email`

## 查看所有配置信息，要查看所有 Git 配置（包括用户名和邮箱）
`git config --global --list`
用v2的CLI如果就是超时,需要加上代理:(后面的端口号看v2右下角"本地"那一项的端口号)
`git config --global http.proxy http://127.0.0.1:10808`
`git config --global https.proxy http://127.0.0.1:10808`
【或者直接v2里启用TUN,就是"系统代理"左边那个"启用Tun"按钮】所有流量强制进代理,就不用命令了【但是TUN注意】开TUN可能导致DNS泄露,会有隐私问题,因此少开
取消代理:
`git config --global --unset http.proxy`
`git config --global --unset https.proxy`
设置全局用户名:
`git config --global user.name "Your Name"`
`git config --global user.email "your.email@example.com"`
而只设置当前仓库用户名的话:
`git config user.name "Your Name"`
`git config user.email "your.email@example.com"`
> 空的clone之前记得`git init`在当前路径创git
> 要是仓库改ip了就直接去搜改法

## 远程通过git同步hexo,其实就是迁移hexo
首先在原笔记本上初始化git仓库:
```
cd 你的hexo项目目录
git init
git add .
git commit -m "初始化hexo博客"
```
然后设置远程仓库:
```
git remote add origin 你的仓库地址
git push origin master //(或者main,具体使用git branch检查是哪个)
```
然后这就传上去了,接下来在新设备上:
```
git clone 你的仓库地址 //要直接一步到位分支也选好的话就是git clone -b main https://github.com/Gahao123/Gahao123.github.io.git
cd 项目目录
npm install // 重新安装依赖
```
接着验证环境就行:
```
hexo clean
hexo generate
hexo deploy
```
【注意,这样搞的话不管在哪台机器上写文章】写之前先pull,写完之后commit+push
【`npm install`报错】管理员身份打开PowerShell执行`Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`,下面的询问输入`Y`回车确认,然后就能`npm install`了
【调整vscode代码高亮样式/中文】: 在vscode下`Ctrl+Shift+P`,然后输入`Preferences: Color Theme`,回车,弹出的框里选"深色(Visual Studio) Visual Studio Dark" ; 中文直接在扩展里搜`Chinese`,找到微软的简体中文,安装即可

## 隔离hexo和Gitea本地仓库
本地git大概率用的是http,因此push本地Gitea时,把http代理先取消,push完再加上就行

## 查看当前文件路径命令：pwd；查看文件ls，详细信息ls -l
`gcc -o 这里是输出文件名 要编的文件`，要运行就是`./文件名`
如果是`gcc 要编的文件`，那默认生成`a.out` ，运行就是`./a.out`
C语言就是`gcc`命令,C++的话就是`g++`

## Linux用root强制执行:sudo
`sudo 命令`即可以root身份强制执行
GPT生成的代码如果让去GitHub搞下来内核源码就PASS,能用root态已经够了,再去弄内核源码搞不懂的
切换root权限:`sudo su`

## 目录相关
看当前路径`pwd` ; 切换`cd 要去的目录` ; 列出所有文件`ls`
返回上一级`cd ..` ; 返回家目录`cd ~`
复制粘贴增添删除啥的建议键鼠解决

## 写文件
打开完直接按i开写,写完按ESC退出,输入`:wq`是既保存又退出w存q退

## 地址位：1个单元格是1B，8个bit，0-255(这个是CPU和操作系统看到的抽象层内存,这个视角下是按字节寻址,内存是一个线性字节数组,1个地址对应1B=8bit)
内存条多大(如16G)，地址位数就是转成二进制的指数位(如34位)
要取一个单元格，就必须把那一行弄到buffer里才能读
注意，以0x开头的数每一位是16进制4个bit，也就是一个单元格能塞两个16进制位，如:map[i]=0xAB,这就塞满了
如果是把mmap得到的map转成32位来访问，那就是能塞8个(8位->32位翻4倍，塞0x的2个->8个)
**虚拟地址映射物理地址读`proc/self/pagemap`**:Linux内核提供了一种通过 /proc 文件系统，在运行时访问内核内部数据结构、改变内核设置的机制。/proc是个伪文件系统，它只存在于内存中，不占用磁盘空间。它以文件系统的方式为外部访问内核数据的操作提供接口;/proc 下还有一些以数字命名的目录，它们是进程目录。系统当前运行的每一个进程都在 /proc 有一个相对应的目录，以该进程的 pid 命名，是读取进程信息的接口，而 self 目录是读取进程本身信息的 link。/proc/self中有一个名为 pagemap 的文件，用来记录当前进程的物理页面的信息；通过 pagemap 可以查看当前进程虚拟页面所映射的物理页的信息。pagemap 中的每一项都是一个64位的值,所以只要找到虚拟页在 pagemap 中的对应项，就能得到虚拟地址所映射的物理地址;pagemap就是映射了虚拟内存和物理内存,具体映射方法不用管(已经有映射结果了,就不要管过程了)
**大致流程**：通过虚拟地址计算出该地址相对于起始地址的页号`page index = virtual address / page size`->计算该地址相对于所在页面起始的偏移量`offset = virtual address % page size`->在 pagemap 中通过页号查找对应项，查看该页是否在内存中->若当前页存在，则取 bits 0~54 加上偏移量，可得所对应的物理地址
**要直接搞物理内存的话**:`/dev/mem` 是 Linux 系统中的一个虚拟字符设备，提供对物理内存地址空间的直接访问。需要 root 权限 ，读完直接mmap将物理地址映射到用户空间，以便直接访问内存，然后读取和写入的时候记得`volatile`去掉编译器优化(但是代昊天说不要用这个)
**物理地址到DRAM地址**知道了物理地址后就行了,然后分析出物理地址对应的DRAM地址,把数据操作到特定DRAM地址,后控制相邻行即可
**内存操控**敏感数据一般是不让随便改/移动的,是由内核态保护的,所以问题是如何把移动这些数据;论文方法是把空间占满,然后释放掉某个地方再加载敏感数据,这样数据就到了目标位置,因为只有那里能放,这样做还有一个前提是得知道数据有多长,论文里有提到怎么算长度RAMBleed和Drammer里(是利用规则,让他直接写到我们想要的位置)

## 内核源码相关
需要用内核源码改寄存器设置,关闭内存读写合并,这个会影响ht他们获取信息还有时间那些判断,要关了才好操作
具体就是进行SPL修改，怎么改可以去查相关手册
内存操控相当于定位闲置内存然后去做读写操作

## DRAM的刷新是按电容电荷刷新
即刷新的时候，电容电荷>50%就刷成1，小于50%就刷成0，一旦改变就相当于永久改变了，所以只要rowhammer把一个位改变了，再怎么刷新那一位也就变了，不用担心刷新和读取的先后等问题
刷新周期默认是每32ms刷新整个DRAM，可以按倍数调(目前就调成了64ms)，也可以调成非整数倍，但是不知道会不会发生什么意料之外的问题

## CIVA直接用户态缓存清空指令
用户态就能用，荔枝派4A直接能用(C910)，平头哥应该也能用
`dcache.civa`缓存无效化指令(用户态就能用)
荔枝派4A上没有ECC

## 地址映射解析做不了
虚实地址映射解析 -> 地址位探测 -> Bank映射函数解析 -> 交叉位探测
因为是弱内存，RV是乱序核，观察不到跳变，而且解析时间很长，所以没法进行

## CLI就是命令行界面
CLI也能开发程序运行，直接用户敲命令执行程序、运行啥的
有CLI就得有命令的结构设计，就是参数设计
图形用户界面是GUI

## 荔枝派4A
LicheePi 4A ; 核心是平头哥C910 (搜TH1520也可以,这个是C910的集群)
后面Milk-V往上就是C920
需要用户手册,TRM(技术参考手册,基本是商业秘密,很难搞),SPL源码(去uboot仓库里找)

## 目前rowhammer大总结
- 已知不存在ECC
- TRR -> 用同步，且TRR阈值基本上为20w次(阈值相当高)，所以把访问频率弄成15w次可行
- 合并重排，即访存ABAB变AABB为重排，再由AABB变AB为合并 -> 指针追逐，把下一个要访问的地址存到当前地址下，相当于强制按次序访存的最高境界;多线程，这样搞也能进行同步;NOP，即访问ABAB的空隙中间拉长间隔以规避合并重排(已经试过不可行)
- 缓存 -> 缓存驱逐(不太行，时间太长);内核模块指令，问题在于调用内核态指令的话要进行用户态到内核态的来回切换，这个切换比缓存驱逐还慢;非缓存内存，即从内核态划分一块非缓存内存给用户态，用户态即可直接访问内存，这样划分有一个问题是无法读pagemap获取虚拟地址和物理地址的映射关系了，具体操作是先内核态指定非缓存内存(如10G) -> 把这个非缓存内存用`mmap`映射给用户态 -> 内核态将虚拟地址和物理地址的映射关系写入一块特定内存 -> 用户态那边读取内存获取虚实映射关系;也可以CIVA指令用户态直接一条指令清除缓存
- ReadBuffer，DDR里自带的一个小缓冲区，存在但是不太重要

cycle数据相关:就是和CPU频率相关，比如CPU频率什么2.3GHz就是CPU1s内有2.3G个cycle
最后分析出来的刷新cycle数就是多少个cycle刷新一次
读内存控制器的标准手册里的寄存器手册 -> 寄存器里写的不一定用上，但是寄存器里没写的东西一定没有启用，eg如何得到未启用ECC?因为板子上那个ECC相关的寄存器没有启用，对那些ECC相关的寄存器访问也非法
数据总线反转技术DBI:每8个位判断一次,如果1的个数大于等于4就进行一次取反操作再存入内存,1的个数小于4就存原数据 -> 多存0让尽量更多电容不带电,这样就不会掉电,进一步减小掉电影响 -> 全F,然后攻击行全0就不行,因为全F经过DBI后存到内存里就成全0了,这时候再用全0的攻击行去攻击弄不出翻转 -> 改成全A(1010),攻击行全5(0101),这样就算经过DBI,攻击时数据也是交错的,才有可能翻转
关page_close_timer=0,让没有访问指令就立刻行关闭,从而后面能触发更多行激活(触发行激活才能弄RowHammer)

## RowHammer需要知道
RowHammer在学术界还没有能在服务器上做成功的,主要是内存的质量确实很好?

## ThrowHammer,Drammer提权,ECC和TRR监测,RAMBleed信噪比就不要了
RDMA完全是另一套体系了和现有的完全不同，而且ThrowHammer没办法集成在咱们的程序里
和Drammer提权有关的全删了，太难了做不了，Drammer只强调定点修改就行;同样的，利用成功率这种和提权有关的也指标也不要
ECC和TRR的数据统计没法得到，也全不要
RAMBleed信噪比啥的也都不要了，只留信息泄露率、信息恢复精度弄到基础指标里就行
**对具体技术指标的定义**一定要慎重，可以少一些再一步步加，但是开始就写死余地就太窄

## 搞snRV服务器内存32G*4太大了怎么办
把4根内存条拔下来3根，这样可以缩小范围

## 内存条物理层知识
通道数由CPU决定,1通道在家用CPU上可插2条,服务器上插1条;内存条的个数反过来也能大概判断通道数,egRV服务器上4根内存条就是4通道
双面内存条的正反两面分rank(就是看正反都有黑方块的),rank之间是可以并行的,1根内存条2个rank,4根内存条就是8个rank;但是单面内存条基本只有1个rank -> 但是也不绝对,只是可以这么认为
然后每个面上有颗粒(就是黑方块,一般是8个,因为8*8bit=64bit,为系统64位,不过8个也不一定,有位宽为4,8,16的颗粒都存在),颗粒分die(可以是1,2,4等die数,有die堆叠),然后有一个专门算ECC的芯片(这个是DIMM ECC,有额外8bit ECC芯片,对系统可见),on-die ECC是在每个黑方块内部的,对系统不可见(也就是关不掉),然后就数方块数来大概确定die数(注意有一面中间那个稍大点的黑方块是内存条的控制芯片) -> 位宽为8指的是一次可并行输出8位bit
然后die下就是bank-group,再往下是bank(一般每组是4bank),然后是行row,然后是列col,然后是偏移量到块cell -> 注意到die下面这些怎么分配就是厂家决定了,有很多种划分方式
【按矩阵理解】从bank开始就当成是一个三维矩阵，bank数算z轴，然后取一个bank相当于取出一个二维矩阵，然后通过row行位(y轴)和column列位(x轴)取得对应元素(先整行进row buffer,再从buffer里选column,1个bank里只有1个row buffer)，这个元素是8个并行cell(1个cell是1bit)，取得对应元素值(8bit数据)【注意这个是物理层的cell单元格,物理上一个cell是1bit,和操作系统使用的抽象内存单元格1B是不一样的】 -> 然后每次读取到元素也不是就读1个,而是读一个"burst长度"(一般是8),也就是说你读column=100,实际100-107都读出来了(单个x8芯片一次burst读出8B,8个芯片并行后总共得到64B,这64B就是一个cache line)
【offset是什么】CPU给的是"字节地址",但DRAM工作的是"行+列+burst",是64B对齐的 -> 如果访问地址 0x1000,DRAM会读0x1000~0x103F,但如果访问地址0x1003,DRAM读的还是0x1000~0x103F,然后CPU取偏移量为3的那个字节 -> 先找到那个bank,算是确定z轴,取出一个二维矩阵,然后通过行位刷进row buffer,然后通过列位确定那个8bit的并行cell集合;不是就从你确定的这个地址开始读,而是看你这个地址属于哪个burst对齐的部分,把那个对应的部分读出来,CPU再根据偏移量找到最终需要的那个数据【burst_offset和byte_offset,这俩是低6位的东西】burst_offset(次3位)是CPU要取burst对齐的读出数据的哪一个8B(cache line偏移),byte_offset(最低3位)就是你要的是这个8B里面的哪一个字节(因为一共是8个字节)

## 做一块新开发板需要找的
用户手册 ; TRM(技术参考手册,基本是商业秘密,很难搞) ; SPL源码(就是去GitHub仓库里找那个写一堆寄存器的很长的dram_init类文件)

## 荔枝派3A
默认用户名root,pw bianbu

## Linux的OOM Killer内存不足保护机制
Linux下如果可用内存不足,回收失败且无法满足新的内存请求,那内核就会选择一个"最占内存/最合适"的进程直接kill
RowHammer程序直接申请80%内存容易遭kill

## 改代码
交叉编译u-boot,记得把共享啥的全关了,网也全弄DHCP,要不apt容易连不上网:
先安编译环境:`sudo apt update`
`sudo apt install -y build-essential make gcc g++ bc bison flex libssl-dev libncurses-dev device-tree-compiler gcc-riscv64-linux-gnu g++-riscv64-linux-gnu`
然后去目录下:
`cd "/mnt/c/Users/wangj/Desktop/yjs/代码文件/荔枝派3A上/uboot-2022.10-k1-bl-v2.2.y/uboot-2022.10-k1-bl-v2.2.y"`
`make distclean`
`make CROSS_COMPILE=riscv64-linux-gnu- k1_defconfig`
`make -j$(nproc) CROSS_COMPILE=riscv64-linux-gnu-`
就去chase_chain_thread.cc里把那些printf都给注释掉，因为这些巨慢
复制代码:`cp -r /mnt/c/Users/wangj/Desktop/yjs/代码文件/荔枝派3A上/chain_Licheepi3a_kernel /tmp/test/` -> 进入目录:`cd /tmp/test/chain_Licheepi3a_kernel` -> 交叉编译:`riscv64-linux-gnu-g++ perf.cpp chain_rowhammer.cc evaluation-analysis/EvaluationAnalysis.cpp Modeler/AttackModel.cpp -o c_hammer` -> ssh登板子跑
【修改申请内存百分比】:在`chain_rowhammer.cc`里修改`double mem_ratio = 0.8;`
【修改有翻转也接着跑】:在`chain_rowhammer.cc`里拉到最后`if print else print`的`else`里把那个`break`注释掉
看Linux下有没有在运行,用查看CPU状态命令:top ;可以看到占用和进程啥的东西;按q退出
这是Linux后台长期运行的经典方式:先给执行权限`chmod +x c_hammer`,然后`nohup ./c_hammer > result.txt 2>&1 &`
这个命令的作用:
部分	含义
nohup	忽略终端断开
./c_hammer	运行程序
\> result.txt	标准输出写文件
2>&1	错误输出也写文件
&	放后台运行
【确认运行】`ps aux | grep c_hammer`
【终止运行】`killall c_hammer`
【查看CPU核心和分布情况】`lscpu`或者`cat /proc/cpuinfo` -> 扫描线程和攻击线程是错开的,所以绑定到同一个核心

## RowHammer的SPL修改
不能说改了SPL，不会验证的，就算验证也可以说你板子比较好、时间没达到、温度湿度
不说就不知道，毕竟实验室环境下能成就行
改SPL的验证:就去那个改寄存器的地方 -> 找地方加入printf,输出一串高辨识度字符 -> 正常替换烧系统,系统开机的时候应该串口会有打印,用debugger,先板子断电,然后接法是板子上的金属棍,debugger上的GND接板子GND,debugger上的TX接板子RX,debugger上的RX接板子TX(一定要对应,未伸出的那一面标了都是什么,然后根据背面和正面的物理对应关系找正确的那个接口,别接错),然后用MobaXterm开一个串口连接Serial,Speed那里一定要选115200,给板子上电,MobaXterm上就有输出,找自己print的那一串字符即可验证(应该在最开始的地方)

## 编译内核模块,在内核态进行RowHammer更容易翻转,用户态很难
先找到和当前内核版本匹配的内核模块:`uname -r`可能输出`6.x.x-spacemit`,去搜`bianbu linux k1 6.1`找到后下载【注意】clone Linux内核得在WSL里去clone
下下来之后`cd "/mnt/c/Users/wangj/Desktop/yjs/代码文件/荔枝派3A上/linux-6.1"`,然后板子上执行`zcat /proc/config.gz > k1.config`或`cat /boot/config-6.1.15 > k1.config`,然后`cp /tmp/test/k1.config .config`确认`ls .config`
然后用kernel headers/build system编译.ko,如`make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- oldconfig`,然后`make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- modules_prepare -j$(nproc)`,完成后验证`ls include/generated`要有`autoconf.h`以及`ls scripts`有`mod目录`
然后弄到那两个ko对应的源码,新建两个文件夹对应放置,`./chain01`里是`Makefile dram_chain_thread.c`,`./chain02`里是`Makefile dram_init.c`,在这两个地方分别`make -C "/mnt/c/Users/wangj/Desktop/yjs/代码文件/荔枝派3A上/linux-6.1" ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- M=$(pwd) modules`应该会同目录生成`.ko`文件
然后【insmod加载模块】`insmod dram_init.ko`和`insmod dram_chain_thread.ko`,后用`dmesg`查看输出【这里最容易出问题,即出现和当前内核版本不匹配】但字符有类似`6.1.15+`和`6.1.15`这种微小不匹配可以直接去`/linux-6.1/include/generated/utsrelease.h`里把`#define UTS_RELEASE "6.1.15+"`这里改成`6.1.15`,然后检测命令`cat /proc/modules | grep dram`或`lsmod | grep dram`,得有`dram_init ...`和`dram_chain_thread ...`说明这俩确实活着,然后`dmesg | tail -50`贴给AI
【编译命令】`riscv64-linux-gnu-g++ -march=rv64imafdc_zicbom_zifencei_zihintpause perf.cpp chain_rowhammer.cc -o c_hammer`,然后还是和上面一样运行
验证程序运行状态:`dmesg | tail -n 50`检测ko有没有在用【这里连上Debugger串口也会自动输出信息】;`lsmod | grep dram`确认模块还在;`ls /dev | grep dram`确认设备存在
【改了代码之后卸载模块挂载】`rmmod dram_chain_thread`和`rmmod dram_init`,检查`lsmod | grep dram`和`ls /dev | grep dram`都没输出即可

## RowHammer特别注意!!!
温度对内存稳定性是有直接影响的,记得把风扇(散热)去掉!!
温度高会更不稳定容易翻转,并且直接拔线完全没问题

## MobaXterm检测不到插上去的Debugger
按`Win + X`打开设备管理器,在"通用串行总线控制器"下找到USB Serial Converter A和USB Serial Converter A,分别右键这俩 -> 属性 -> 高级 -> 选上加载VCP,然后个A/B应该会分别被MobaXterm识别为COM6/COM7,在MobaXterm里选其中一个COM没输出的时候就换另一个COM

## 结果文件里出现大量open pagemap: Too many open files
程序在不停地`open("/proc/self/pagemap", ...)`，但是没有`close()`，最后把进程的文件描述符FD耗尽了，Linux每个进程默认只能打开有限数量的文件（通常1024），超出限制再`open()`就会报错并导致stdout/stderr的输出混杂
因此不能在每次检测bit flip时都重新`open("/proc/self/pagemap")`而不关闭，否则长时间运行后会出现FD泄漏。
最后解决方法是:程序本身有`physical_pages`保存了物理页地址 -> 虚拟页地址,建立反向映射`unordered_map<virtaddr_t, physaddr_t> virtaddr_map;`并初始化`for (auto &page : physical_pages){virtaddr_map[page.second] = page.first;}`,这样在检测bit flip时,根据虚拟地址找到所属page直接查`virtaddr_map`表得到物理页基址再加offset得到物理地址

## TRR目前对于快速累积到阈值的有很好的抓取识别能力
但是对于缓慢往阈值去累计的就很头疼
是随机抓取策略，并且TRR是跨越多个刷新周期刷一次
监控条目数量越多越好但是会产生调度问题，监控条目接近10(估计在十几个到几十个之间)，替换策略可能是低频替换
PRAC是每行都追踪，开销大
RISCV只有指针追逐能保证访问顺序，其他所有方式都不行，产生不了行激活
服务器ECC触发不是简单写个日志，而是直接宕机，后续就无法再利用了

## K1的老版本镜像[下载](https://archive.spacemit.com/image/k1/version/bianbu-linux/)
LicheePi 3A和Banana Pi BPI-F3用的都是这里的v1.0.15/ -> 对应的Linux内核是6.1.15

## DDR4和DDR5不兼容,不能互换
物理插槽就是不兼容的,一块主板只能支持DDR4或DDR5其中一种,不能同时支持,也不能后期切换
CPU也会被绑定,无法兼容

## b站乔红讲[内存原理](https://www.bilibili.com/video/BV1Htr8YhELV/?spm_id_from=333.1387.upload.video_card.click&vd_source=caea238fdc1c97555bce64a9b94b987e)
- 内存条通电存数据,断电则存的数据全没,使用一般是把硬盘数据搬到内存,以便CPU做更快的读写
- 电容中间是可存在绝缘物质的,也会保持性质 ; 然后一个晶体管做开关控制电容与电路导通与否,这就是内存基本结构`1T1C`,T晶体管,C电容,对应一条直接操作这个`1T1C`的导线,就是位线bitline,还有一条线把整个一排的晶体管控制极连起来,叫字线wordline,这条线可以控制整条线上晶体管的通断
- **为什么需要字线wordline控制导通?**:每个`1T1C`都扯一条位线bitline太占地方,于是设计成这一排的电容的位线和上一排并列的电容的位线是连一起的,两排电容共用一组位线,这样的话每一排用来控制通断的字线就可起作用,控制每一排`1T1C`是否接入电路,同时为避免数据乱套,同一时刻只能有一条字线有电压
- DDR5上纵向有65536根字线wordline,横向有8192列(也就是8192根位线bitline),这就是一个bank(一个`65536*8192`的阵列,就是16位行位*13位列位),32个bank就可以组成一个内存颗粒(16个1排,放两排,上下对齐),把多个内存颗粒封装一下焊到一块电路板上,就是一根内存条了 ; 当然不同的内存颗粒里面的结构也不是完全相同,可以开内存颗粒看一看进行印证
- 存储设备一般是读和写两种操作,但是有问题,比如上面的`1T1C`,随着工艺和制程进步,晶体管的尺寸越来越小了,带来一个问题就是这个晶体管会在断开的情况下缓慢漏电(晶体管漏电问题是目前阻碍半导体发展的最大问题,FinFET和GAA都是为了解决这个),这个漏电会导致电容上的电荷被缓慢泄露出去,漏的多了就1变0了,所以内存除了读写,还有刷新操作
- DDR5一个电容充满电的电压是1.1V表示1,没电是0V表示0,如果要对其中一排进行刷新操作,就先把所有位线bitline预充电到0.55V(表示1和0的一半位置),然后激活对应要刷那一行的字线wordline,若电容满电1.1V,此时位线上是0.55V所以电容电压往位线上走,电容上电压降低,位线上的0.55V会升高,比如升到0.82V,然后位线末端有一个神奇的电路叫感应放大电路,这个电路可以感应到位线上电压的细小变化,感应到这个变化后将这个变化放大,比如上面的0.55V变0.82V会被位线末端的感应放大电路感知到,然后感应放大电路将位线电压增大到1.1V,这时电容上不足1.1V,于是变成位线给电容充电 ; 反之若电容0V,则导致位线电压降低,感应放大电路感应到后将位线拉成0V,最后把电容也弄成0V ; 这一整行的弄完后,就完成刷新操作,同时刷新完此时位线和电容里的电压是一致的,可以直接进行读取操作 ; 写操作也是一样要先对目标行做一次刷新操作,位线拉到0.55V,激活字线让电容和位线相连,电容电压扰动位线,感应放大电路放大扰动结果,刷新完后用一个附加电路修改感应放大电路的状态,以此操作位线,从而对电容充电或放电,实现新数据的写入
- **为什么写之前要刷新而不是直接覆写?**:如果只看要写入的那几个电容,那刷新确实是多余的;但字线的控制范围是一整行,不刷的话这一行的其他电容的电荷就顺着位线跑光了

## 读DDR5设计规范JESD79-5C
- 在`Table5-16Gb Addressing Table`里对应`2Gb*8`那部分(因为是8个颗粒),BG Address是BG0-BG2,Bank Address in a BG是BA0-BA1,# BG/# Banks per BG/# Banks是8/4/32,Row Address是R0~R15,Column Address是C0-C9,Page size是1KB,Chip IDs / Maximum Stack Height是CID0-3/16H
- 第三章MR4是温控刷新频率,温度在85度以上,刷新率翻倍,tREFI减半 ; MR58是RFM保护的触发配置,即某一行被连续激活多少次会被硬件主动插入一次额外刷新来保护相邻受害行 ; MR59是动态RFM配置,抗pattern的(*注*,MR58和MR59要读内存寄存器才能确定是否启用)
- 第四章,REF命令就是刷新命令,tREFI就是刷新间隔,每隔tREFI时间就要发一次REF命令 ; REF命令发出后，有一段"锁定期"（tRFC），在这段时间里DRAM完全不能做别的事情（不能被ACTIVATE、不能被读写），必须等这段锁定期结束 ; 4.13.5的Table69是tREFI的温度和刷新模式变化表,Table70是tRFC（死区时长）表,用这些计算真正能用的"hammer窗口"，以最常见的场景（Normal模式, 16Gb, ≤85°C）为例：`可用hammer窗口 = tREFI1 - tRFC1(min) = 3900ns - 295ns = 3605ns ≈ 3.6微秒`，如果用Same Bank Refresh模式（DDR5新特性，只锁定单个bank，其他bank同时可用），则死区更短，只有130ns，窗口相对更宽裕，这也解释了为什么rhoHammer代码里、以及你日志里反复出现"sync_ref"、"Same Bank"相关的处理——Same Bank Refresh模式下的死区（130ns）比Normal模式（295ns）短一倍以上，意味着攻击者如果能精确对齐Same Bank Refresh的时序，能拿到更多的有效hammer窗口
- 4.42是RFM/DRFM完整的工作机制流程、具体数值(不过这俩开没开要看颗粒)，**RFM**是内存控制器给每个bank维护一个计数器RAA，每对某个bank发一次ACTIVATE命令，这个bank的RAA就+1，当RAA达到RAAIMT阈值（这是MR58里那个32~80的档位），DRAM就会标记"需要额外刷新管理"，然后控制器会插入一次RFM命令，执行后RAA计数器减少RAAIMT（相当于"清空"这次累积的攻击痕迹），如果RFM命令没有及时插入、RAA持续累积到RAAMMT（3x~6x RAAIMT）这个更高的上限——DRAM会直接拒绝接受该bank的任何新ACTIVATE命令，必须先发REF或RFM把计数降下来才能继续访问，也就是说，一旦你的hammer强度触发了RAAMMT这个硬上限，DRAM会直接硬性拒绝你继续攻击目标行，这是比"插入一次刷新"更强力的保护，相当于给你的hammer代码强制加了个"熔断器" ; **DRFM**是控制器可以主动指定一个具体的行地址，然后发一个DRFM命令，DRAM就会精确刷新这个被采样行"物理相邻"的邻居行，这里的Table 177是BRC保护半径档位表
- PRAC是JESD79-5C版本引入的比RFM更新、更精细的RowHammer防护机制，*但是*它和RFM(上一条那个)是【**互斥**】的，一旦PRAC被启用，RFM和DRFM这两套机制会被DRAM内部自动禁用，二者不会同时生效，是互斥替代关系，不是叠加关系 ; 并且PRAC是可选特性，不是所有16Gb颗粒都支持，并且默认是禁用的，需要先通过MR70:OP[1]=1显式启用，而且启用前必须先做ACI（Activation Counter Initialization，激活计数器初始化），这是个相对复杂的初始化流程，不是"开机自动生效"(即需要主机（BIOS/内存控制器）主动去启用它，普通消费级/工作站平台的BIOS大概率不会去做这个初始化流程，因为这个流程相当复杂，涉及全阵列刷新和状态机切换)
- 4.36说明on-die ECC是*强制要求*的，合规的DDR5一定会有，128个数据位 -> 生成8个ECC校验位(SEC，单比特纠错)，并且DRAM芯片在把数据返回给内存控制器之前，会先在芯片内部自动纠正任何单比特错误，所以只要是"单比特错误"，on-die ECC会在数据被读出、返回给你的检测程序之前，就已经自动纠正掉了，但是是读的时候纠正，但没有写回，也就是说，纠正只发生在"读出瞬间"，芯片存储阵列里的原始错误比特依然留在那里，没被修复，并且不存在任何统计 ; *唯一的例外是*:当同一个128位数据块里，同时出现2个或以上的错误（超出了SEC单比特纠错的能力范围）时，才会变成"检测到但没法纠正"的状态 ; 还有4.37ECS(这个不是强制要求,要看厂家弄不弄)，这个是建立在on-die ECC基础设施之上的一个"主动巡检/统计"外壳，这个是按特定频率主动读出,纠正,写回阵列,这是ECS特有的动作,会真正修复存储阵列里的错误,并且有统计,EC/EpRC这两个计数器来记录"哪里错了、错了几次"(不过操作系统也很难读到)

## C/C++做准备(标准库全不看,记不住的,而且不会问)

### C , 是面向过程式的,编译后直接生成高效率机器代码,可直接操作内存和硬件
- 【`gcc`编译不加`-o`】生成的是`a.exe`,执行是`./a.exe`
- 【避免滥用`goto`】以保持代码可读性
- 【C语言的函数不支持重载】每个函数名在同一作用域中必须唯一
- 【标识符(变量起名)】不能以数字开头,且标识符中允许的特殊字符只有下划线"`_`"
- 【建议用`const`定义常量取代`#define`】因为带有类型检查,更安全(同时不能再改变)
- 【*运算符特殊*】`^`按位异或,`~`按位取反,`&`取地址,`*`解引用,`->`指针访问成员,`.`直接访问成员,`?:`三元运算符
- 【sizeof】可用`sizeof`获取数组长度:`int length=sizeof(array)/sizeof(array[0]);`,`char`1字节,`short`2字节,`int`和`float`4字节,`double`和`long long`8字节 -> 【*记字节能表示的数*】`2的字节位数-1次方`,正数到不了,负数能到
- 【动态数组】`malloc`和`calloc`动态数组,运行时大小可变,用`realloc`可重新分配内存并改变数组大小,用完后需用`free`手动释放内存,避免内存泄漏和访问无效的内存配置
```
//malloc示例
int size=5;//数组长度,后面改这个size变量就可该数组大小,scanf都行
int *array=malloc(size * sizeof(int));
//使用数组
free(array);//释放内存
```
判断`malloc`是否成功:上面`array`==NULL即失败
- 【enum】C语言中枚举是被当做int处理的,所以无法遍历枚举类型
- 【回调函数】通过函数传参把"调用哪个函数"的决定权交给使用者 -> 函数指针是C的方案,而多态是C++的方案,且函数指针相比多态更轻量级 -> 不过C程序员想把某个行为小给用户决定只能用函数指针 -> 底层思想还是if-else参数决定行为的进化
- 【**字符串操作函数**】`strcpy(s1,s2);`复制s2到s1,`strcat(s1,s2);`把s2加到s1末尾,`strlen(s1);`返回s1长度不包括末尾`\0`,`strcmp(s1,s2);`s1和s2相同返回0,`s1<s2`返回小于0,`s1>s2`返回大于0,`strchr(s1,ch);`返回一个指针指向s1中ch第一次出现的位置,`strstr(s1,s2);`返回一个指针指向s1中s2第一次出现的位置,输出字符串直接`printf`里`%s`
- 【`struct`不完整声明】两个结构体互相包含,A里有B的指针,B里又有A的指针,那在A的定义之前加`struct B;`进行不完整声明
- 【`struct`访问成员变量】正常就是`book1.title`;若是指针`ptr=&book1`,那就`ptr->title`
- 【`union`】和`struct`同一个定义方式,不过多个成员共享同一块内存,同一时刻只有一个成员有效,`sizeof`返回的是成员`sizeof`的最大值(`struct`返回的是和) -> 节省内存,同一串bit不同方式解释,硬件寄存器映射,协议解析
- 【位域】按位定义`struct`成员变量,用于压缩内存,eg有int存的只要`0/1`的开关变量,可以在定义时指定位数`struct{ int a:1; }`这个`:1`表示只用1位,这个`struct`占用4个字节但只有1位用来存值,一直到定义32个占1位的变量这个`struct`都只占4字节,但到定义33个占1位的变量,那这个`struct`就会变成占8个字节(第一个int填满了)【位域的数据类型**必须是int相关**,且位域的宽度不能超过其数据类型大小,使用位域变量是`.`或`->`不变】
- 【输入输出】C语言把所有设备都当作文件,所以设备(如显示器)处理方式与文件相同 ; 程序执行会自动打开以下3个:标准输入`stdin`来自键盘,标准输出`stdout`和标准错误`stderr`都来自屏幕 ; I/O通常用`scanf()`和`printf()`
- 【文件读写】`FILE *fopen(字符串文件名,访问模式);`访问模式"r"读"w"写"a"追加模式(后面这俩文件不存在会创建新文件),成功返回指向FILE对象的指针,失败时返回NULL ; 关闭文件`int fclose(FILE *fp);`成功关闭返回0失败返回EOF ; 读写函数用`fgets()`和`fputs()`
- 【**C预处理器**】是编译过程中的独立阶段,在实际编译前对源代码文本处理(其实就是个文本替换工具) ; 预处理器命令都以`#`开头,`#define`定义宏,`#ifndef`若宏未定义则编译后续代码常用于头文件保护,搭配`#endif`结束条件编译快:(因为若一个头文件被引用两次,编译器将出错)
```
#ifndef HEADER_H //ifdef这个是若宏已定义则处理
#define HEADER_H
/* 内容 */
#endif
```
跨平台开发:
```
#if defined(_WIN32) //这个defined是确定是否被#define过
//Windows特定代码
#elif defined(__linux__)
//Linux特定代码
#elif defined(__APPLE__)
//macOS特定代码
#endif
```
- 【预处理器宏延续】就是一行写不下:`\`,然后写到下一行
- 【**头文件`.h`**】包含函数声明和函数定义,C和C++建议将所有常量、宏、系统全局变量、函数原型写在头文件`.h`中,需要时引用头文件,系统头`#include <file>`检索系统目录的标准列表,用户头`#include "file"`检索当前文件目录
- 【强制类型转换】显式就是加`(类型)`,隐式就是编译器自动转
- 【C语言**没有**对错误处理的直接支持】仅能以返回值的形式访问底层数据,即判断函数返回值,还有处理错误代码errno
比如打开了一个不存在的文件,这时全局变量`int errno`就会变成一个数字,比如:
```
fprintf(stderr,"错误号:%d\n",errno); //输出为:"错误号:2"
perror("perror输出错误"); //输出为"perror输出错误:No such file or directory",就是errno自动变成说明的字符串
//若是自己弄了个int
int errnum=errno;
fprintf(stderr,"打开文件错误:%s\n",strerror(errnum)); //这里后面的是根据errnum那个变量得到对应文本,输出为:"打开文件错误:No such file or directory"
```
main里返回错误`exit(-1);`,正常`exit(0);`
- 【C可变参数】函数参数末尾加上`...`,用`va_list`访问可变参数列表,按顺序访问,没有类型信息(就是手动从内存一个个往下读参数),*缺点是*类型不安全+容易越界+依赖约定 -> 一般用C++的模版,这个编译期类型安全 -> 用C可变参数的典型`printf();`后面跟几个都得输出
- 【**C内存管理**】`void *calloc(int num,int size);`分配num个长度为size字节的连续空间,并全初始化为0 ; `void free(void *address);`释放分配的动态内存空间 ; `void *malloc(int num);`也是分配num字节大小的内存空间,但不初始化 ; `void *realloc(void *address,int newsize);`重新分配内存并扩展字节到newsize ; `memcpy(目标内存指针,源内存指针,要复制的字节数);`从源内存区复制数据到目标内存区域 ; `memmove();`类似`memcpy();`但它可处理重叠的内存区域,参数同上
- `void *`类型表示未确定类型的指针,C和C++中`void *`可类型强制转换为任何其他类型的指针
- 【C未定义行为Undefined behavior】会产生不可预测的结果,本质是"内存解释方式是否合法",如用`float`去解释一个原本不是`float`的就是UB,而不是初始化和兼容问题 : 数组越界访问 ; 解引用空指针,即访问NULL指针的内容 ; 使用未初始化的局部变量 ; 除0操作 ; 数值溢出/`<<`或`>>`位数太大 ; 不安全的类型转换,如将`malloc`出的`int *ptr`转成`float *` ; 内存越界,向已`free`或未分配的内存写数据 ; 未定义的浮点数行为,如比较两个`NaN`是否相等 ; 还有许多 -> 应尽力避免未定义行为
- 【命令行参数】`int main(int argc,char *argv[])`,argc参数数量,包括程序名本身,所以argc至少为1,`argv[0]`是程序名称,剩下的是命令行参数 -> 参数通常是字符串,若参数中间有空格,用`""`包裹
- 【C安全函数】主要是字符串处理的`_s`版本,是在函数参数里加入"目标缓冲区大小",防止越界,防止缓冲区溢出 ; 但是这些一般不用
- 【C排序】 冒泡 ; 选择 ; 插入 ; 希尔,不稳定,先将整个待排序列分成若干子序列(由相隔某个`gap`的元素组成)分别进行直接插入排序,然后依次缩减增量,最后全体再插排一次 ; 归并 ; 快排
- 【***C的工程结构***】`./src/`源代码目录,放`.c`文件 ; `./include/`头文件目录,放`.h`文件 ; `./tests/`测试代码目录 ; `./lib/`第三方库或静态库目录 ; `./build/`输出目录,放`.o`文件、`.exe`文件,可自动生成 ; `./doc/`文档目录 【***注***】`main.c`只`#include .h`文件,然后由`.h`对应的同名`.c`实现`.h`文件 
- 【现代C编译流程】1.预处理,展开`#include`和`#define`; 2.编译,将C分析生成语法书AST,并转化为IR(中间表示)进行优化,经多轮IR优化后生成可重定位的目标机器码文件.o,现代C已不需要汇编这个中间产物; 3.链接,将多个.o文件和库拼接,解析外部符号和地址重定位,生成可执行文件
- 【静态库和动态库的区别】静态库`.a`在链接时复制到可执行文件中,程序独立但体积大 ; 动态库`.so/.dylib`在运行时加载,多个程序可共享,但需确认存在

### C++ , 是面向对象的,算C的扩展,因此所有C文件都算合法C++文件
- 【`using namespace std;`是C++的】对应`cout<<""<<endl;`也是C++的
- 【*C++支持面向对象四大特性*】封装,对外隐藏实现细节 ; 继承,已有类派生新类 ; 多态,同一操作对不同对象有不同实现 ; 抽象,即抽象类和接口
- 【数据类型】`char16_t`,16位Unicode字符类型,占2字节(16位) ; `char32_t`,32位Unicode字符类型,占4字节(32位) ; `long long`是占8字节
- 【**动态类型转换**,用于继承】,eg:`class Derived : public Base{}`,前面是派生类,后面基类需有虚函数 -> `main`里调用时先`Base* ptr_Base=new Derived;`基类指针指向派生类对象,然后动态将基类指针转为派生类指针:`Derived* ptr_derived=dynamic_cast<Derived*>(ptr_Base);` ; 然后下面判`ptr_derived`若非`NULL`,即转换成功可用`ptr_derived->show();`调用派生类方法;若为空即可进行转换失败处理 -> 前提是基类必须有虚函数`virtual ~Base()=default;` ,这个和多态不同,多态是从基类统一调用派生类行为,而`dynamic_cast`是从基类回头再确认你是谁,且很安全(类似`instanceof`+转换+安全保证)
- 【静态转换】`float f=static_cast<float>(i);`这个是按规则转换,安全 ; `float f=reinterpret_cast<float&>(i);`这已经是`float`解释`int`了,未定义行为,危险,但这个是为了维持底层控制能力必须保留 ; C的括号转化,这个是*编译器帮你猜你想干啥*,具体行为不可控,是C遗留在C++里的,且没有语义约束
- 【`extern`】在A文件里用B文件的变量,那`b.cpp`先定义`int aaa=0;`,然后`b.h`写声明`extern int aaa;`,然后`a.cpp`里`#include "b.h"`就能用`aaa`了 【*注*】`entern int aaa;`这是声明的正确写法,是不分配内存的,如果带了初始化,如`extern int aaa=1;`那么`extern`会被编译器忽略,这就变成定义了(初始化就意味着内存分配,会导致`extern`失效) 【*再注*】现代C++少用`extern`会高耦合埋雷,一般用一个类封装数据+只暴露接口来变成显式依赖(比`extern`的隐式好)
- 【若局部变量和全局变量同名】则局部变量遮蔽全局变量;全局变量定义时会自动初始化,而局部变量不会 【所以局部变量定义时需手动初始化】 -> 推广:每层`{}`都是一个新的作用域,内层作用域可定义与外层同名变量,这样合法,且内层遮蔽外层 -> 但是注意,不是覆盖修改而是屏蔽访问,外层变量仍存在,只是在当前作用域不可见 -> 作用域查找顺序:当前,外层,全局,找到第一个就停止
- 【`volatile`】指该变量的值可能会被程序以外的因素改变,如硬件或其他线程
- 【`static`】`static`局部变量会导致用起来像全局变量,生命周期变长,但作用域不变 ; 全局变量默认`extern`,使用`static`全局变量可限制该变量作用域为该`cpp`文件,即外部不可见 ; `static`类即类唯一+按类的生命周期来管,而不是对象了
- 【`thread_local`】是每个线程一个变量副本,从使用来说,被`thread_local`的变量会在不同线程使用这个变量的时候从这个变量的初始化开始使用完全互不干扰的副本
- 【`const`和`mutable`】`const`函数只在类的成员函数下意义大，是该函数约定不修改成员变量+只调用const函数(用于查询函数,状态判断函数,打印/序列化/日志函数)，但是如果成员变量被`mutable`修饰的话，则是希望在保持上述性质的前提下让这个成员变量在`const`成员函数里变得可修改(开例外)
- 【运算符优先级大致3档】: 1.括号与一元(`!`与`->`与`++`这类) 2.数学运算`+-*/` 3.移位,判断(`==`与`!=`),与或 ; 【易错】:`*ptr++`为`*(ptr++)` ; `*++ptr`为`*(++ptr)` ; `++*ptr`为`++(*ptr)` ; `*arr+1`为`(*arr)+1` ; `a&b==0`为`a&(b==0)` ; `x&y==z`为`x&(y==z)` ; `x+y<<z`为`(x+y)<<z` 【注】见到`i=i++;`或`i++ + ++i;`或`a[i++]=i;`这种同一变量在一个表达式里多次修改/读取,直接判UB不分析
- 【函数参数几种形式】: 1.传值调用(默认),修改参数不影响原值 ; 2.指针调用,把参数地址给形式参数,修改参数影响原值 ; 3.引用调用,把参数引用给形式参数,修改参数影响原值 -> 指针调用和引用调用性能更高,一般对于只读的大对象参数使用`const T&`,既避免复制,又保证不可修改 -> 实际常用引用调用,即避免调用时出错,又保证了速度 ; *引用*虽然底层用指针实现但不是指针,只是别名,不能为`null`,不用解引用,也不能改变指向(指针是地址,这些都可以)
- 【*`*`和`&`注*】`*`和`&`在C++里通常和变量名绑定,而不是类型,比如`const int& x;`实际上是`&x`,还有`int* p;`实际上是按`int (*p);`来理解 -> 【***重要***】在定义东西的时候,`*`表示指针,`&`表示引用 ; 而在进行运算的时候,`*`表示地址解引用,`&`表示取地址(*在定义东西和运算时的含义不同*) -> 【***进阶***】`int** p;`一个指向`int*`的指针(二维数组) ; `int*& p;`这是引用一个`int*` ; `int&* p;`这相当于指针一个`int&`,而引用被禁止指向,所以非法 ; `int&& p;`右值引用,假如要`string s=超复杂string;`,还想加快速度,原本是不行的,因为右边的`超复杂string`马上就没了,至少也得复制一次进行中转,但现在`string&& s=超复杂string;`,相当于直接把数据偷过来,速度快还不需要一个持久对象 -> 【*再注*】引用一旦绑定到一个变量,就不能再绑定到其他变量 ; 且引用不支持多级间接访问,即不能有引用的引用(所以编译器会有**引用折叠**,避免代码自动产生的`& &`这种引用的引用)
- 【**Lambda函数**】就是匿名函数,比如用于那些临时占位的函数,不用起名字+外部维护了(比如`sort`的`cmp`就可以用这个占位),写法是`[capture](parameters)->return-type{body}`,例如`[](int x, int y){ return x<y; }` ; 相当于用前面那个`[]`来匿名指代一个函数,然后这个`[]`里可以控制这个匿名函数能访问的外部变量 -> 如果是`[]`则不能访问当前作用域下的任何变量(不过后面的函数参数还是能正常用的) ; 是`[x,&y]`表示能访问当前位置的x和y,x以传值方式传入(默认),y以引用方式传入 ; `[&]`表示当前任何变量都以引用方式可访问,`[=]`表示当前任何变量都以传值方式可访问 ; `[&,x]`表示x以传值方式传入,其余变量以引用方式可访问,`[=,&z]`表示z以引用方式传入,其余变量以传值方式可访问 ; 【*注*】对于`[=]`或`[&]`的形式,lambda表达式可以直接使用`this`指针;但是对于`[]`的形式,如果要使用`this`指针,必须显式传入:`[this](){this->someFunc();}` -> 后面的参数表`()`和函数体`{}`和正常函数一样
- 【数组初始化赋值】`int a[5]={1,2,3,4,5};`
- 【**C++的string类**】(可修改):`string str="google";` , 赋值`str1=str;` , 连接`str3=str1+str2;` , 求长度`int len=s.size();`和`s.length()`都行 , 判空`s.empty()` , C++的string可修改,访问`s[i]`(不检查越界)/`s.at(i)`(检查越界) , 插入`s.insert(插入位置,插入字符串);` , 删除`s.erase(pos,len);`删除位置和长度 , 替换`s.replace(pos,len,str);` , 查找`auto pos=s.find("bc");` , 子串`s.substr(pos,len);` , 比较可以直接比
