---
title: PPT简历汇报Word等相关
date: 2025-03-26 15:07:49
categories:
  - 信息保存
tags:
  - PPT注意
  - 答辩演讲总结
  - 简历相关
  - Word标题
  - Excel
---

## PPT母版-->上面视图栏-->幻灯片母版
可以直接调整生成的所有的PPT页的样式
> - 封面要中规中矩一些，就直接写汇报名称+汇报人就行
> - 封面后的第二页一定要有目录
> - 在母版视图里可以右键新建样式，然后在ppt页里右键选择版式就可以应用，调完版式后在这页下方插入新页就会应用这个版式的东西来新建

右键单张PPT页可以调整背景图片
也可以直接在母版里调，这样就都是一致的了

<!-- more -->

## 取消PPT切换动画、内部动画和自动播放
取消切换动画:全选PPT里所有幻灯片->选到"切换"选项卡->切换到此幻灯片选"无"->右边勾选"换片方式:单击鼠标时",取消"设置自动换片时间"
取消内部动画:选到"动画"选项卡->高级动画里的"动画窗格"->Ctrl+A全选,右键菜单选删除
取消自动播放:选到"幻灯片放映"选项卡->设置幻灯片放映->推进幻灯片选"手动"

## 画图画表想好看
去闲Y上搜专业画图画表，能比论文里还逼真
绘图配色可搜"科研绘图配色" -> RMB配色可参考:100,50,20,10,5,1
**科研和工程的文档表要不一样的**:科研(给软件所那种)要三线表,而工程类(给中兴的)就要全实线的表

## 自我介绍注意
英文自我介绍会慢，需要少写点;中文念PPT会变快，需要稍微多弄点
广告行业铁律:永远不要说客户的产品烂,绝对不能违反

## 汇报注意
开汇报会议的时候每个章节/部分讲完都要停下来问:您看这个部分有没有问题
每个部分要停顿下来问问的!

## presentation注意
开头一定要有自我身份介绍
对于专有名词要有解释(尤其是英文的),可以帮助听众理解+凑时长

## 简历相关
简历【必须】就要一页,多页的模板看都不用看直接删,也不需要封面
基本信息别写太多,不写地址,写年龄别写出生日期
挑有含金量的东西写,比较最能吸引HR的东西,含金量靠后的就可以删掉了(比如院级比赛)
比赛如果有些日子了,可以切换思路:不写时间了,只写获奖比赛和奖项,然后把"获得奖项"这一块写小点
学校层次985一定要写上,写上总是好的
> 反正就是多写直观信息,一次信息,不要再让HR用脑子加工信息了

## Word设置标题级别
选中那一段,右键选段落,常规里有个"大纲级别",这里选几级就是几级标题
插入分页(直接跳到下一页):插入 -> 最左边"页面" -> 分页
插入目录:在"引用"里找到第一个目录,添加即可,弄好那些标题,右键更新域更新整个目录即可
【目录里有不好看的三级标题那种】比如特别大,直接左边选中那一行,直接改字体看看,可能就能直接变了
【修改目录样式】在"开始" -> "样式"右下角小箭头,在列表里找到目录 1/TOC 1,目录 2/TOC 2,目录 3/TOC 3(分别对应目录的一级/二级/三级标题) -> 右键"TOC 1",修改,然后点左下角"格式" -> "段落",在这里改就行【一般就改成段前段后0,然后行距1.5倍即可】
【**导航窗格,就是标题在左边**】快捷键`Ctrl+F` ; 也可以上面"视图" -> 中间"显示"里勾选"导航窗格"
【**Word代码块**】放到表格里,单列表格,甚至还能弄标题

## Excel
【固定表头】上面"视图" -> 冻结窗格 -> 可冻结首行、首列，这样滚动即可保持表头/第一列
【列筛选】选中一列 -> 上面"数据" -> 点"筛选" -> 这一列的表头点三角框,选择条件

## 在线Overleaf Latex改中文
左下角Settings里,Compiler,改成`XeLatex`,然后再在文档里加入`\usepackage{ctex}`,重新编译即可
推荐使用排版的包:`\usepackage{algorithm}`和`\usepackage{algpseudocode}` ; 再加上包后面取消自动的`Algorithm 1`后面的`1`:`\renewcommand{\thealgorithm}{}`
就用下面这个模板:
```
\begin{algorithm}[H]
\caption{算法3.2.1：指令集解析算法（ISA Parsing Algorithm）}

\textbf{Input} cpuInfoSource：CPU基础信息接口

\vspace{0.3em}

\textbf{Output} isaProfile：ISA能力描述对象

\vspace{0.5em}

\begin{algorithmic}[1]

\State cpuInfo $\gets$ GetCPUInfo(cpuInfoSource)
\Comment{获取处理器基础信息}

\If{cpuInfo = NULL}
    \State \Return FAILURE
    \Comment{CPU信息获取失败，结束探测}
\EndIf

\State isaString $\gets$ ParseISAString(cpuInfo)
\Comment{提取ISA架构字符串}

\State archType $\gets$ IdentifyArchitecture(isaString)
\Comment{识别RV32或RV64架构}

\State extensions $\gets$ ExtractExtensions(isaString)
\Comment{解析扩展指令集能力}

\If{archType = INVALID}
    \State \Return FAILURE
    \Comment{架构信息异常}
\EndIf

\State isaProfile $\gets$
BuildISACapabilityProfile(
archType,
extensions)

\Comment{构建设备ISA能力描述对象}

\State \Return isaProfile
\Comment{返回标准化探测结果}

\end{algorithmic}
\end{algorithm}
```

**这个是代ht给的模版**,没有开头的"Algorithm"了而是"算法"(这个还是麻烦的),注意若出现报错一般是公式符`$$`出错了,就在出错那里调一下`$$`的位置让他完全包住公式就行:
```
\documentclass[UTF8]{ctexart}

\usepackage{amsmath}
\usepackage{amssymb}
\usepackage[a4paper,margin=2cm]{geometry}
\usepackage[ruled,vlined,linesnumbered]{algorithm2e}

\SetAlgoNlRelativeSize{-1}
\DontPrintSemicolon

\SetKwInput{KwInput}{Input}
\SetKwInput{KwOutput}{Output}

\newcommand{\triangleremark}[1]{\hfill $\triangleright$ #1}

% =========================
% 关键修改：算法中文化 + 编号格式
% =========================
\renewcommand{\algorithmcfname}{算法}
\renewcommand{\thealgocf}{3.3.\arabic{algocf}}

\begin{document}

\begin{algorithm}[H]
\caption{Random模式非均匀访存编号序列构造算法}

\KwInput{$params$：HammerParameterSet}
\KwOutput{$hammer\_pattern$：HammerPattern}

\textbf{function} GEN\_RANDOM\_PATTERN$(params)$

\Indp

$accessers\_index \leftarrow [DUMMY] \times params.num\_slot\_total$
\triangleremark{访问槽初始化}

$aggressor\_patterns \leftarrow \emptyset$
\triangleremark{攻击者模式集合初始化}

$periods \leftarrow GET\_AVAILABLE\_MULTIPLICATORS(params)$
\triangleremark{生成周期倍率集合}

\For{$i \leftarrow 0$ \KwTo $params.num\_slot\_total - 1$}{

\If{$accessers\_index[i] \neq DUMMY$}{continue}

$free \leftarrow GET\_FREE\_SLOTS(i, accessers\_index)$
\triangleremark{连续空闲槽长度}

$(n,a,d,mul) \leftarrow GET\_CURR\_PARAMS(params, free)$
\triangleremark{攻击行数/振幅/距离/倍率}

$m \leftarrow GAUSSIAN(periods)$
\triangleremark{高斯随机周期选择}

$p \leftarrow m \times params.base\_period$
\triangleremark{当前访问周期}

$cand \leftarrow GEN\_CANDIDATES(params,n,d,mul,p,aggressor\_pattern)$

\triangleremark{生成候选攻击行集合}

\If{$aggressor\_pattern \neq \emptyset$}{
    $aggressor\_patterns \leftarrow aggressor\_patterns \cup \{aggressor\_pattern\}$
}
\triangleremark{记录当前攻击者模式}

FILL\_SLOTS$(i,p,cand,accessers\_index,mul)$
\triangleremark{周期填充访存序列}

}

\For{$i \leftarrow 0$ \KwTo $params.num\_slot\_total - 1$}{

$p \leftarrow (i-1+params.num\_slot\_total)\bmod params.num\_slot\_total$

\If{$accessers\_index[i]==accessers\_index[p]$}{
$accessers\_index[i] \leftarrow (accessers\_index[i]+1)\bmod params.num\_aggressors$
\triangleremark{消除相邻重复访问}

$n \leftarrow (i+1)\bmod params.num\_slot\_total$

\If{$accessers\_index[i]==accessers\_index[n]$}{
    $accessers\_index[i] \leftarrow (accessers\_index[i]+1)\bmod params.num\_aggressors$
    \triangleremark{二次冲突修正}
}
}
}

$hammer\_pattern \leftarrow BUILD(accessers\_index,\ aggressor\_patterns)$
\triangleremark{基于访问序列与攻击者模式构造最终模板}

\Return{$hammer\_pattern$}

\Indm
\textbf{end function}

\end{algorithm}

\end{document}
```