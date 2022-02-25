# 把龙芯电脑变成怀旧游戏PC是什么体验？
在我年初回国之后，我就收到了一件特别的“欢迎礼物”，来自张浩斌的龙梦9S2A一体机，在他的《龙芯能干啥?》[^1]一帖中，展示了龙芯2F利用模拟器运行游戏的情形。

10多年前，我曾经接触过夏普Zaurus系列Linux PDA，是当时为数不多的ARM Linux设备之一，许多爱好者都自行编译移植各路游戏软件到上面，而另外一个启发我的平台则是为丁果A320开发的Dingux，它运行在同样使用MIPS指令集的君正处理器上，按道理来说，许多游戏也同样可以移植到龙芯。

这启发我尝试移植更多的游戏到这台一体机上，龙梦9S2A使用的是800Mhz的龙芯2F，而一代开源游戏神器丁果A320使用的君正JZ4740只有360Mhz，理论上讲，所有Dingux上的游戏都具备移植到龙梦9S2A的潜力。

## Quake
我尝试的第一个游戏是著名的Quake，1996年发布的Quake以及之后的整个系列在90~2000年代一直被视为3D游戏的性能标杆，也是各路媒体必备的“跑分神器”，Quake一代引擎的代码在1999年就以GPL协议发布，成为开源软件，而后续的Quake II和Quake III也陆续开源，开源后的Quake系列被移植到包括Amiga OS、RISC OS这类小众平台在内的许多不同平台，成为比较不同平台游戏性能最常用的工具之一。

因为在网页里的电脑博物馆的建设过程中大量使用了SDL作为移植模拟器到WASM的库，所以我首先尝试了在LibSDL网站上提到的SDL Quake[^2]，但是因为SDL Quake中仍然保留了原始Quake代码中的x86汇编，所以无法在龙芯上编译，通过一番搜索，我找到了《Linux Quake HOWTO》[^3]一文，其中介绍了TyrQuake、QuakeSpasm等多个不同的Quake移植版本。

经过一番尝试，我最终使用TyrQuake源码编译成功，TyrQuake会生成使用OpenGL Render的版本以及使用软件渲染器的版本，因为龙芯9S2A一体机的SM501芯片几乎只有帧缓冲，所以用软件渲染性能更好些。

在thandor.net网站中，我找到了一系列90年代的CPU运行DOS版本Quake的得分[^4]，网站中的测试的标准使用了窗口模式，320x200分辨率，并禁用声音。参照其设置我尝试在龙芯一体机下运行了一组类似的测试，在文件夹中执行，使用320x200窗口模式运行TyrQuake（龙芯一体机的SM501显卡不支持全屏运行在不同分辨率下，会黑屏，如果遇到类似的情况，需要使用Ctrl+Alt+Backspace重启XWindow）

```shell
./tyr-quake -w -width 320,height 200 -nosound

```
得到的分数是62.2fps，超过Celeron 300Mhz，低于Pentium II 266Mhz，不过如果使用较旧的TyrQuake 0.61版代码[^5]进行测试，成绩会超过一代超频神器Celeron 300A，超过68 FPS。

## 模拟城市
我尝试移植的第二个游戏是模拟城市，SimCity的Linux版本的历史可以追溯到1993年发布的X11 TCL/Tk版模拟城市，当时这款游戏主要面对的是Sun、SGI等商业Unix工作站用户。

2008年，模拟城市Unix版开发者Don Hopkins将这款游戏的源代码以GPL v3协议捐赠给OLPC教育笔记本电脑计划，让这款经典的模拟游戏可以运行在各类\*nix平台上，我们直接可以使用Github里的micropolis-activity文件夹[^6]编译运行（需要libxpm-dev和byacc依赖，可以通过apt安装）。

模拟城市的移植在一方面也展示出Linux与商业Unix应用之间的互换性，在某种程度上来讲，使用MIPS指令集的龙芯电脑可以被视为某种“小型机”或“Unix工作站”。

## 仙剑奇侠传
仙剑奇侠传是在中文世界最有影响力的游戏之一，在2009年开发者weimingzhi开发了跨平台版本的SDLPAL，在龙芯社区里许多爱好者都曾经尝试过编译这一版本[^7]。

目前SDLPAL的代码仍然被爱好者们持续维护[^8]。新的版本使用了C++11和SDL2，这也让新版本的代码没法在龙梦9S2A使用的共创Linux系统（Debian 5）下直接编译，好在CodePlex上的旧版本代码[^9]可以编译通过。

## 金庸群侠传
金庸群侠传，最初是铁血丹心论坛上的开发者游泳的鱼发布的，原始的代码[^10]没有包括Linux所需的makefile，不过已有热心的爱好者加入了makefile[^11]，可以顺利编译通过。

## 德军总部3D
发布于1992年的德军总部3D（Wolfenstein 3D）是最早取得商业成功的第一人称射击游戏之一，也是共享软件（Shareware）发行模式的早期成功案例，在1995年成为开源软件，它的代码移植版本很多，Wolf4SDL[^12]是其中兼容性较好的版本，使用了SDL1.2多媒体开发库，可以直接在龙芯一体机上编译运行，与Quake类似需要注意的是启动的时候要加上 --windowed参数，SM501不支持全屏，一旦全屏就会黑屏，必须ctrl+alt+backspace重启XWindow

## 天旋地转2（Descent II）
天旋地转2是发布于1996年的飞行第一人称射击游戏，也是最早运用PC显卡做3D加速的游戏之一，在1998年成为开源软件，我使用了D2X项目[^13]的移植代码编译到龙芯一体机上。

## Mini Slug（合金弹头）
Mini Slug是Clement CORDE开发的街机游戏合金弹头（Metal Slug）的重实现版本，而且相比街机版本增加了难度更高的新关卡，代码的可移植性非常好，在开发者的主页[^14]上还可以看到其他一些游戏，编译的时候要注意先编译ymlib（使用build_a.sh脚本）音频库，再编译游戏本身。

## ScummVM
ScummVM是一个在Linux上非常流行的冒险游戏引擎，可以直接用apt安装

```shell
sudo apt-get install scummcm
```

参照网上的教程[^15]，就可以运行猴岛的秘密等经典的DOS冒险游戏了。

## J2ME游戏：MicroEmulator
另一个在龙芯上玩更多游戏的办法是使用Java运行J2ME游戏，我尝试了若干种不同的模拟器，兼容性比较好的是MicroEmulator[^16]。为了让MicroEmulator默认加载所有的Library，我们需要编写一个脚本，将所有的jar文件加入classpath：

```shell
java -cp \
microemulator.jar:\
lib/cldcapi10.jar:\
lib/cldcapi11.jar:\
lib/microemu-jsr-82.jar:\
lib/microemu-javase-applet.jar:\
lib/microemu-nokiaui.jar:\
lib/microemu-jsr-120.jar:\
lib/microemu-siemensapi.jar:\
lib/microemu-jsr-135.jar:\
lib/midpapi20.jar:\
microemu-jsr-75.jar \
org.microemu.app.Main \
--appclassloader delegating
```

然后将脚本保存成一个.sh文件执行，这样MicroEmulator就自带所有库文件启动了，试了一下，能运行的游戏并不是特别多，一个可以运行的例子是古墓丽影

## 玩游戏玩累了，试一下Processing吧
Processing不是游戏，而是一个多媒体交互开发环境，由于它使用Java开发，所以跨平台使用比较容易。龙梦9S2A安装的共创Linux自带移植自Sun JVM HotSpot的Java 5虚拟机，但是Processing 1.5.1运行起来会崩溃，我尝试使用龙芯官网提供的OpenJDK 6[^17]替代它。

在Processing官网processing.org下载Processing 1.5.1的X86版，再将龙芯JDK解压出来的j2sdk-image目录更名为java替代processing自带的java目录就算安装好了，虽然Processing在运行的时候会提示不能完全和OpenJDK兼容，但还是能运行不少Processing的sketch的。我自己就成功地运行了新媒体艺术家Golan Levin的Floccus[^18]和Meshy[^19]。

## 运行一些Linux demo
最后我还尝试了一下兰威举发布在Ubuntu中文论坛里的几个Demo[^20]，都可以很顺利的运行。在我看来，这台龙梦9S2A一体机虽然已经是10年之前的产品，但仍然是一款相当有趣而且强大，可玩性颇高的平台，而让它如此好玩的背后，确是无数出于爱好不求回报的爱好者们的贡献：Linux PDA社区、开源掌机社区、Demoscene社区、数字艺术社区、怀旧游戏社区等等。

龙芯处理器及其平台是中国爱好者们的独特财富，就像欧洲以德国为中心的Amiga及Morph OS社区、以英国为中心，依托于ARM架构的RISC OS社区，以及美国使用Power处理器的Raptor Talos平台社区一样，这些不同于IBM PC兼容机的电脑架构往往是充满挑战的“练习场”和“试验田”。

当然相比之下龙芯社群还显得有些稚嫩，无论是社群规模、影响和声誉还是跨界合作都较为有限，我也决定为龙芯社区的建设出一份力，让更多的大众有机会体验龙芯电脑。我把这台龙梦9S2A一体机捐赠给中关村创业大街的极致时空创业博物馆展出，这应该是目前国内唯一可以公开体验龙芯电脑及其游戏的场合，每个参观者都可以亲自体验用龙芯电脑玩经典游戏的乐趣。

## 参考资料
[^1]: 龙芯能干啥? https://zhuanlan.zhihu.com/p/60074380
[^2]: SDLQuake https://www.libsdl.org/projects/quake/
[^3]: Linux Quake HOWTO https://tldp.org/HOWTO/Quake-HOWTO-3.html
[^4]: thandor.net - benchmark - Quake https://thandor.net/benchmark/33
[^5]: TyrQuake 0.61源代码 https://disenchant.net/files/engine/tyrquake-0.61.tar.gz
[^6]: SimHacker: micropolis https://github.com/SimHacker/micropolis
[^7]: 龙芯2F笔记本逸珑8089D两款游戏 https://www.linzhihao.cn/archives/1882/
[^8]: SDLPAL https://sdlpal.github.io/sdlpal/
[^9]: CodePlex Archive: sdlpal https://web.archive.org/web/20180424003631/https://archive.codeplex.com/?p=sdlpal
[^10]: 【09-01-13】【复刻】金庸群侠传Windows Lua复刻版 【游泳的鱼】 https://tiexuedanxin.net/thread-413121-1-1.html
[^11]: ZhanruiLiang/jinyong-legend https://github.com/ZhanruiLiang/jinyong-legend
[^12]: Wolf4SDL https://github.com/11001011101001011/Wolf4SDL mozzwald/wolf4sdl https://github.com/mozzwald/wolf4sdl
[^13]: D2X Project https://icculus.org/d2x/
[^14]: Projets https://oldschoolprg.x10.mx/projets.php
[^15]: 如何使用ScummVM播放复古点对点冒险 https://mos86.com/82319.html
[^16]: MicroEmulator https://sourceforge.net/projects/microemulator/
[^17]: JDK6-MIPS32位 http://www.loongnix.cn/index.php/JDK6-MIPS32
[^18]: Floccus http://www.flong.com/archive/projects/floccus/index.html
[^19]: Meshy http://www.flong.com/archive/projects/meshy/index.html
[^20]: 自制 DEMOSCENE (就是传说中的64K动画) https://forum.ubuntu.org.cn/viewtopic.php?f=88&t=302327 又写了一个 demoscene (64K动画) https://forum.ubuntu.org.cn/viewtopic.php?f=88&t=320271