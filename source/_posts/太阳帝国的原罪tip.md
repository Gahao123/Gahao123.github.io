---
title: 太阳帝国的原罪tip
date: 2025-01-30 17:08:53
categories:
  - 信息保存
tags:
  - Sins of a Solar Empire
  - 游戏配置
---

## 技能许可目标
“战舰”指大船，“轻护舰”指小船
> 许可目标未标明“主力舰”的均不可对主力舰生效
> 如 超度巡洋战船:1眩晕 许可目标为敌对的战舰和轻护舰，则不可对主力舰和泰坦生效

<!-- more -->
## 改无限钱
【**注意自定义地图地图大小一定拉到最小**】要不会有不必要的拖沓
在你的安装目录里找到个Galaxy目录，用记事本打开其中的文件（具体是哪个地图你参考下游戏里选地图时右侧的地图提示，那里有个图示里面写着地图的英文名的，或者你直接用翻译软件查看下这个目录里的文件名也能对应找到你想改的地图）
以双人图中有23颗星球的Storm  Front地图为例，用记事本打开后拖到最后，用搜索找到如下文字:(注意备份文件)
```
player
designName "Player0"
inGameName "Player0"
overrideRaceName ""
teamIndex -1
startingCredits 3000
startingMetal 800
startingCrystal 250
```
其中player0~9分别对应于第1个~第10个玩家，玩家选择是队伍 1时，teamIndex 要改作 0 ；玩家选择是队伍 2时，teamIndex 要改作 1 , 如此类推。
否则电脑会随机分派Player，不过有时候你给其他队伍设成了1或其他也有可能会被随机分配资源。
所以，推荐你把第一个也就是player0的 teamIndex -1改为teamIndex 0。然后其他的都改成teamIndex -1，这样电脑就不会随机分配你改好的金钱了。
startingCredits指的是资源推荐7个9，startingMetal指的是金属startingCrystal指的是晶体这俩推荐6个9，多了会出错。

还有`planetArtifactDensity 0.150000`神器密度，`planetBonusDensity 0.600000`星球奖励密度

## 下的多泰坦MOD 1.96那个原理是黑暗舰队召唤出泰坦
贸易和圣临是战舰工厂有黑暗舰队技能,瓦萨里是不要钱的相位门可以黑暗舰队
主力舰容量实际是可以随意透支的,不会有问题,因此把主力舰容量需求都改掉 -> 把`AbilityDarkCapitalShip.entity`里的`requiredCapitalShipSlots`这一项删掉,并将`useCostType "ResourcesAndMustHaveCapitalCrew"`按照另外两个改成`useCostType "ResourcesAndMustHaveShipSlots"`

## 强制攻击
强制攻击只限制攻击指令而并不会限制移动指令;或者是对电脑无效?
> 即 光辉主战飞船:2仇视 无法打断移动和跳跃指令，无法进行控制

## 圣临的光束防御平台共鸣
是5个造一起吃满,总共1000盾和50%输出加成

## 圣临炸球:星际基地的流星
虽然技能上面的红字写的伤害120,人口15,但是上面还有波数:1级流星4波,2级流星8波(总共960)
效果很不错,比泰坦炸球快得多,还不影响舰队扫荡
不过星际基地造起来很慢,也可炸球主力舰的大(歇斯底里),2级大直接`-1%/s`持续50s

## 护盾缓冲
**护盾缓冲**可以理解为**护盾免伤**，越集火效果越明显。特别是会加强某一方向的护盾，围攻时护盾掉的比只有正面击中快。
简单点说就是免伤，一边挨打免伤百分比也会一边上升，但有减免上限，一般70+%，即使护盾没了也依然有效。
单个船挨打的火力越密集，护盾缓冲越高，上限一般是75%
> 圣临80%+都有可能
> 但是遇上瓦萨里的相位导弹护盾就不行了，相位导弹触发时是**同时忽略护盾和护盾缓冲**，因此效果很好
> 瓦萨里的瘫痪船有护盾干扰，能进一步提高穿盾效果

## 超级武器数量
每**4**个星球增加一门超武，贸易忠诚升级了科技可以每4球增加2门 ; 初始的1个超武也计算，所以占到第8个球才能开第二门超武

## 矿
中立矿井收入巨高，一定要占
没科技的话跳跃一次要花100反物质
在两颗恒星都放了加贸易的星际基地，贸易线就能**跨越星系**
精炼厂只提升影响范围内每个矿井0.08产量(而且每个矿井还有3个精炼器的影响上限)，推荐只在能影响到12+矿井的地方造精炼厂，否则还不如贸易港
中立+叛军能吃到外壳+护盾+装甲研究，能吃到技能研究(前提是同种族)，无法吃到所有武器+伤害研究
圣临反叛泰坦一定要点大招,少点两级反物质 -> 2级大是每下25%血给50%技能CD,10%伤害减免和400%回蓝 -> 靠这个才能硬打双子基地

## 忠诚度
球的忠诚度取决于到首都的距离
|距离	|基础值  |最大值|
|--|---|---|
|0	|  100%	 | 110%|
|1	|  90%	 | 100%|
|2	|  80%	 | 90%|
|3	|  70%	 | 80%|
|4	|  55%	 | 65%|
|5	|  40%	 | 50%|
|6+	|25%	 | 35%|

## 不同类型和难度AI的差别介绍
AI共6个难度，
第一难度资源100%
第二难度资源100%
第三难度资源150%
第四难度资源200%
第五难度资源400%
第六难度资源600%
AI智商没根本变化，但从第四到第5难度是质的飞跃
发展类型：
进攻型优先发展舰队
防御型优先发展星基空雷防御设施
经济型优先发展贸易
研究型优先研究科技

## 官方wiki的教程

文件修改(wiki)[https://wiki.sinsofasolarempire.com/index.php/Convert_Data]
参考这个修改游戏参数文件的格式为TXT,然后才能改游戏文件
直接在./GameInfo下打开`*.entity`开头是`BIN2`的话,说明已经被编译成二进制结构了,这样是没法改的

### Convert Data
Starting to mod SoaSE first requires converting files from BIN format to TXT format. Once converted, the files can be edited with any text editor like notepad, I recommend Notepad++ (free). Using TXT files is fine, BIN file are used to help the game load faster and save a lot of space, basically a form of compression the game can read.

Note: The ConvertData files are version specific. The old ConvertData files included with Forge Tools 3 will not work with current files or the current ConvertData files will not convert older mod files.

Converting files will keep the same extension (e.g. *.entity). To open the converted files you will need to first specify a program to do so (e.g. notepad). The very first line upon opening the file will either read TXT or BIN.

Contents
1	Batch Convert
1.1	Create a BAT File
1.2	Run a BAT File
2	Single File Method
2.1	BIN to TXT
2.2	TXT to BIN
3	See also:

### Batch Convert
Using a simple .bat file along with the ConvertData_Rebellion.exe, converting multiple files becomes very easy.

 

#### Create a BAT File
Save a new .bat file.
Create a new text document on your desktop. Double click the file – it should be blank inside. Now, go to file>save as, and in the “Save As” window, input a name for your BAT file and then add a “.bat” on the end (without the quotes).

Once the .bat is created, double click to run. To view or modify, right-click --> Edit

By default, the ConvertData exes will convert to BIN so the bin switch at the end is not required. The next 2 examples are identical.

`for %%a in (*.type) do ConvertData_Rebellion type %%a %%a bin`
`for %%a in (*.type) do ConvertData_Rebellion type %%a %%a`

Convert File Types
GameInfo\*.entity to Text    	`for %%a in (*.entity) do ConvertData_Rebellion entity %%a %%a txt`
GameInfo\*.entity to Bin	`for %%a in (*.entity) do ConvertData_Rebellion entity %%a %%a`
Mesh\*.mesh to Text	`for %%a in (*.mesh) do ConvertData_Rebellion mesh %%a %%a txt`
Mesh\*.mesh to Bin	`for %%a in (*.mesh) do ConvertData_Rebellion mesh %%a %%a`
Particle\*.particle to Text	`for %%a in (*.particle) do ConvertData_Rebellion particle %%a %%a txt`
Particle\*.particle to Bin	`for %%a in (*.particle) do ConvertData_Rebellion particle %%a %%a`
Window\*.brushes to Text	`for %%a in (*.brushes) do ConvertData_Rebellion brushes %%a %%a txt`
Window\*.brushes to Bin	`for %%a in (*.brushes) do ConvertData_Rebellion brushes %%a %%a`
[modDB] Download .bat collection

#### Run a BAT File
Locate ConvertData_Rebellion.exe fron the Sins of a Solar Empire Rebellion install directory (e.g. C:\Program Files (x86)\Steam\steamapps\common\Sins of a Solar Empire Rebellion).

ConvertData BAT.png
Copy ConvertData_Rebellion.exe to the location which will be used to convert the files. The new mod folder is a good choice. Keep the ConvertData_Rebellion.exe and any new .bat files together for these codes to work.

Example Convert Entity folder inside a mods GameInfo folder.

C:\Documents\My Games\Ironclad Games\Sins of a Solar Empire Rebellion\Mods-Rebellion v1.85 Dev\Reference Files\GameInfo\Convert_Entity

Move a single entity file, or all of the ones required to convert into the same directory as the .bat files and the .exe. Double click the .bat file and wait for the batch to complete.

### Single File Method
To keep all the modding files together, create a Developer folder in the C drive (e.g. C:\Developer). Inside the new folder, create a folder called ConvertData.

Navigate to where SoaSE is installed 
(e.g. C:\Program Files (x86)\Steam\steamapps\common\Sins of a Solar Empire Rebellion) 
and Copy [Ctrl + C]

ConvertData_Rebellion.exe

Paste [Ctrl + V] the files in the ConvertData folder (e.g. C:\Developer\ConvertData). Note the short address location. This address will need to be typed at the DOS prompt so keep it short to avoid spelling errors. You can also use paste at the DOS prompt.

#### BIN to TXT
Navigate to the folder containing the BIN files, where SoaSE is installed or the mod folder e.g.

C:\Program Files (x86)\Stardock Games\Sins of a Solar Empire - Trinity\GameInfo

and COPY [Ctrl + C] the files which you choose to mod and paste them to the ConvertData folder.

The 3 ConvertData exes are DOS programs and will need the Command Prompt window to use them.

XP
Navigate to Start --> Programs --> Accessories --> Command Prompt
or type cmd `[Enter]`in the Start --> Run box.

Vista and Win 7
Navigate to Start --> All Programs --> Accessories --> Command Prompt
or type cmd `[Enter]` in the Start --> Start Search box.

Navigate to the ConvertData folder in DOS using the cd "Change Directory" command and address, this is not case sensitive. e.g.

CD C:\Developer\ConvertData

CMD CD.png
There are 4 different types of files to convert.

brushes
entity
mesh
particle

To do each file separately from the selected expansion, use...
 

Rebellion

ConvertData_Rebellion type FileName.type FileName.type txt `[Enter]`
 

Trinity Versions

ConvertData_OriginalSins type FileName.type FileName.type txt `[Enter]`
ConvertData_Entrenchment type FileName.type FileName.type txt `[Enter]`
ConvertData_Diplomacy type FileName.type FileName.type txt `[Enter]`

Here is an example using AbilityEMPBlast.entity from the GameInfo directory.

ConvertData_Rebellion entity AbilityEMPBlast.entity AbilityEMPBlast.entity txt

If there was no error the file will be converted to TXT and the DOS prompt will return to the convert directory.

#### TXT to BIN
Once the mod is tested and everything is good you may want to help the game load quicker by converting the TXT files back to BIN format. This is the same as the process above accept for the TXT switch at the end of the command line.

By default, the ConvertData exes will convert to BIN so the bin switch at the end is not required. The next 2 examples are identical.

ConvertData_Rebellion type FileName.type FileName.type bin `[Enter]`

ConvertData_Rebellion type FileName.type FileName.type `[Enter]`

 