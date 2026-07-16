---
title: 保护硬盘U盘
date: 2025-02-22 17:07:08
categories:
  - 信息保存
tags:
  - 保护清理硬盘
  - 压缩软件7zip
  - 爆0E
  - 扩展存储NAS
---

## 注意扫盘软件
做清理:Dism++,spacesniffer分析磁盘(**这个一定要以管理员身份运行!**)
关闭休眠功能清C盘:管理员权限运行powershell,输入powercfg -h off,重启即可自动删掉C盘里的hiberfil.sys,省下磁盘空间
不用担心,多用睡眠功能,不用休眠功能就好了,删掉没啥影响
NVIDIA Corporation文件夹可以删除，因为它包含的是显卡驱动的解压文件，安装完成后这些文件对系统没有影响。删除这些文件不会对显卡的正常工作产生不良影响，但在删除之前，建议确认显卡驱动已经成功安装并且稳定运行。
NVIDIA APP在更新完驱动之后也可以卸了,能省下大量C盘空间
`用户/AppData`下的Java相关组件都可以全删了,不会影响
pip是python的包管理,不用python了可以删
`Local/NVIDIA/DXCache`是游戏预编译着色器缓存,太大可以删,但是一般建议保留
C盘里和打印机相关的如果不用打印机了可以删,比如HPPrintScanDoctor
`Local\Microsoft\vscode-cpptools`下是vscode的C++缓存可以全删
**WSL也占空间**,往非系统盘(D盘迁移): 1.`wsl -l -v`记录下`NAME`那一列; 2.在确认`wsl --shutdown`的前提下在D盘新建目录并导出`wsl --export Ubuntu D:\wsl\Ubuntu.tar`(后面那个目录自己建),这里需要等导出完; 3.注销原来的WSL,`wsl --unregister Ubuntu`,C盘的文件会删除; 4.导入D盘的那个tar包`wsl --import Ubuntu D:\wsl\Ubuntu D:\wsl\Ubuntu.tar --version 2`; 5.查看是否正常`wsl -l -v`要和原来一样,并且vscode开WSL也正常即可; 6.删除`D:\wsl\Ubuntu.tar`,这个只是导出的备份文件 -> Linux系统可以自己打包备份
【终极大法】备份有用的文件,然后设置 -> Windows更新 -> 高级选项 -> **重置此电脑**
> QQ和WX会在启动时扫盘，从而对硬盘带来负荷！
> 扫盘次数太多会导致固态硬盘坏掉，就是爆0E
> 硬盘检测工具DiskInfo，其中0E栏介质与数据完整性错误计数正常为0，如果不是0就说明已经在坏掉倒计时了 -> 去掉启动声音:`./CdiResource/voice`里的全删了
> 扫盘的大多是杀毒软件和反作弊程序，切记注意。
> 启动项管理:任务管理器左边"启动应用"这里,把不需要开机就自动运行的软件全部右键选择"禁用" -> 建议禁用"手机连接"和"移动设备",避免开机时这俩进行局域网搜索手机进行握手同步产生卡顿

<!-- more -->

## 硬盘删文件后留有碎片,SSD的TRIM会自动处理
固态磁盘SSD会有TRIM机制,不用的块会被逐渐清理,所以删了的文件慢慢就没了

## 删xbox相关，其实就是删系统内置的搜不到的应用
基本是需要管理员运行powershell才能删 -> `Get-AppxPackage *xbox* | Remove-AppxPackage` 拿着这个命令问GPT即可
还有一些能删的: 

|项目名|命令|
|:--:|:-------------:|
|人脉| `Get-AppxPackage *people* \| Remove-AppxPackage` |
| Groove音乐、电影与电视 | `Get-AppxPackage *zune* \| Remove-AppxPackage` |
|3D |`Get-AppxPackage *3d* \| Remove-AppxPackage `|
|新闻订阅 |`Get-AppxPackage *bing* \| Remove-AppxPackage`|
|地图 |`Get-AppxPackage *map* \| Remove-AppxPackage`| 
|OneNote |`Get-AppxPackage *OneNote* \| Remove-AppxPackage`|

【然后再关调用】移除ms-gamingoverlay调用 -> Windows设置 -> 游戏 -> Game Bar全关,摄像全关 
【然后再注册表禁用两条命令】:`reg add HKCU\Software\Microsoft\Windows\CurrentVersion\GameDVR /v AppCaptureEnabled /t REG_DWORD /d 0 /f`
`reg add HKCU\System\GameConfigStore /v GameDVR_Enabled /t REG_DWORD /d 0 /f`

## `C:\Users\用户`目录清理
`.cache`是各种缓存,`.matplotlib`是Python绘图,`.e4mc_cache`,`Logs`,`.bash_history`命令行历史,`.python_history`Python REPL历史,`.lesshst`Linux工具历史都可删
`.vscode`是vscode用户配置,`.ssh`是SSH的key,`.gitconfig`是Git全局配置,`.eclipse`+`.p2`+`.sts4`是Eclipse用户配置,带`wsl`的都是WSL配置文件
`xwechat_files`是wx的直接删

## 强删软件:Revo Uninstaller
直接去搜索下载即可,要便携版
看见要删的直接删,选模式那里一般用Moderate即可;如果宁可冒错杀风险也要清干净就选Advanced
像比如有多个Java版本这种就别Advanced了,用Moderate(要不删其中一个容易把另一个的也删了)
在列表右边空白处可以右键 -> Export list to -> txt或html导出列表
半手动扫描问题:上面的Tools -> 选对应的使用 ; 点一下全空白的是上方有"Scan"和"Delete"的需要扫描 ; Browsers Cleaner这里最好日常就选Delete temporary internet files即可 ; Evidence Remover这个是已删除文件的全盘覆写防找回(硬盘消耗大) ; Unrecoverable Delete这个是单个文件/目录的删除+覆写防找回
还有*WindowsApps*也可以删不常用的,如Xbox , Xbox Game Bar , Clipchamp , Microsoft Solitaire Collection(纸牌游戏) , Mixed Reality , 3D Viewer , Phone Link(如果不用手机连接,这个是无线连,不影响USB插手机), 反馈中心

## 压缩软件7zip，这个是开源的
直接搜7zip，各个系统的都有，无广告
要7z接管压缩文件:安装的地方管理员打开7-Zip File Manager,Tools(工具),Options(选项),这里可以调文件接管、右键菜单和语言
右键菜单建议只留"解压到"3条和"添加到"3条 ; 然后选上"右键菜单显示图标"
【**使用**】自己存档/备份/发给自己,用`.7z`文件压缩 ; 发给别人/不确定对方能不能装软件/作业提交/群里分享,用`.zip`

## 系统迁移
DiskGenius做磁盘迁移
可以直接热迁移,然后应该会自动弄成从新盘启动(如果没有,那就进BIOS设置一下)

## 扩展存储NAS
是一种通过网络连接的存储设备,允许多个用户和设备访问和共享数据
通常用于文件存储备份、媒体流传输等
搭NAS的话基本上要用机械硬盘,静态放置的话更稳定且便宜;固态贵而且可能有数据丢失问题
> 注意AI大基建导致硬盘在狂涨价

## 清理环境变量
在cmd里直接搜`set`即会输出所有环境变量,贴给AI做分析
然后设置里搜高级系统设置,下面的环境变量,去里面把该删的删掉

## 查看系统信息CPU-Z
直接去搜索下载`cpu-z_*.*.*-cn`,启动就能看相关信息

## AMD RX 7650 GRE显卡驱动损坏/版本冲突/Windows自动覆盖驱动等
导致设备管理器显示显卡黄色感叹号,无法使用 -> 这是AMD驱动安装失败或残留/Windows Update自动替换驱动/Adrenalin与底层驱动版本不匹配/多次安装卸载导致驱动栈混乱

### 修复流程
1. 断网 -> 拔网线/关Wifi , 防止Windows自动安装错误驱动
2. 断网下使用DDU彻底清理驱动(网上下载工具Display Driver Uninstaller(DDU)),选择GPU,AMD,点Clean and restart,可以彻底清除当前显卡的一切,彻底避免版本冲突
3. 保持断网重启
4. 重启后联网,去AMD官网手动选择Graphics -> Radeon RX 7000 Series -> RX 7650 GRE(对应项)并下载AMD Software: Adrenalin Edition WHQL(完整安装包),就是选大的,1.5G那个版本下载,然后安装,然后重启
5. 重启后设备管理器,显示适配器里AMD Radeon RX 7650 GRE(无感叹号),修复成功,启动游戏不再报OpenGL错误