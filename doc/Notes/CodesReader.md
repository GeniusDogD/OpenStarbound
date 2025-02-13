[CodesReader/OpenStarbound](https://github.com/CodesReader/OpenStarbound)

压缩包密码：He7&3@Ma9



《泰拉瑞亚》是反编译的，读玩法相同的《星界边境》代码；以后捡《泰拉瑞亚》值得读的地方读；

 

# 各模块功能

| Source 文件夹 | 功能                                                         |
| ------------- | ------------------------------------------------------------ |
| Core          | 核心的支持代码，可通用于任何游戏。例如基础的数据结构、算法、Json 读写方法等。 |
| Extern        | Core 模块依赖的一些外部来源的代码。例如 Lua 支持，Hash  算法等。 |
| Base          | 包含相比于 core 模块没那么通用，但仍然很底层的代码。         |
| Platform      | 平台 API。只有头文件，没有具体实现。需要在 application 模块中实现。 |
| Game          | 核心游戏逻辑。Server 和 client 端都使用的代码。              |
| Test          | 使用 Google Test 进行测试                                    |
| Server        | 服务的代码                                                   |
| Utility       | 一些命令行工具。例如解包 assets 的工具。                     |
| Application   | 包含创建窗口、输入和输出处理、2d 绘图的代码。                |
| Windowing     | 控制面板和小组件                                             |
| Rendering     | 绘图代码，但不依赖组件系统（widget  system）                 |
| Fronted       | 前端，或者说交互界面的代码。                                 |
| Mod_uploader  | 官方提供的上传 mod 的程序的代码。                            |
| Client        | 客户端，即游戏入口。我们从这里开始解读                       |

 

游戏的 assets 即 \assets\packed.pak 文件。可以使用 asset_unpacker.exe 解包，它在运行目录 \win32 文件夹中。

Asset_unpacker.exe 的源码时 utility 文件夹中的 asset_unpacker.cpp

 

# 程序入口 UML

StarApplicationClient.cpp

STAR_MAIN_APPLICATION

runMainApplication

SdlPlatform::run() 包含游戏的初始化和循环

Application 抽象类定了模板，由 ClientApplication 类实现；

 

ClientApplication

实现了Application模板，ClientApplication类使⽤不标准的状态模式来管理游戏场景的转换；

ClientApplication类的changeState()⽅法管理游戏场景的转换；

MainAppState::Splash指的是开场动画；

MainAppState::Title指的是启动界⾯；

m_cinematicOverlay

m_cinematicOverlay成员变量⽤于管理过场动画。游戏的过场动画是cinematic⽂件，⾥⾯是Json。想查看动画效果，可以在游戏内的 控制台使⽤cinema命令。《星界边境》打开控制台⽅法为：进⼊游戏后，按下“/”键或“Enter”键，即可输⼊命令。

 

# 资源加载系统与内置 Mode 框架

省流版：

《星界边境》内部实现了RFC6902这样⼀种Json补丁标准，接⼝在StarJsonPatch.hpp中。mod制作者编写patch⽂件给游戏assets内的 Json格式的⽂件打补丁（增删改），以此来实现给游戏打mod

详细版：

UML 类图

各个类所在层及功能

RootLoader 类

RootLoader rootLoader(……) 不是一般的创建对象的语法，栈上创建对象，而不是 new 在堆上，会在代码块结束时调用析构函数销毁；

《星界边界》大量使用这种 C++ 语法，例如 Root::reload() 方法内的互斥锁就是栈上创建对象，创建时 lock()，代码块结束时 unload()，没有显式调用；

RootLoader 类中的代码中有对命令行参数的解析 。。。

RootLoader类加载最基本的配置（sbinit.config），获取资源、存档与⽇志⽂件的路径，还有命令⾏参数解析等功能。

 

# 如何制作 Mode

[Modding:Portal - Starbounder - Starbound Wiki](https://starbounder.org/Modding:Portal)

在Installing Mods⼀段中，可以看到游戏从两个地⽅加载mod，⼀是Starbound\mods\⽂件夹，⼆是 Steam\steamapps\workshop\content\211820⽂件夹。

在How do I mod Starbound⼀段中，可以看到游戏assets包含有Json对象、Lua脚本、⾳频和图像⽂件。 

 

对于一般的游戏，比如《泰拉瑞亚》，给它打 mod 需要反编译出游戏源码，然后编写 patch 文件给游戏源码打补丁。tModLoader 中打补丁的 patch 文件；

《星界边境》内置了这一过程，不需要反编译出源码；只需要给 Jason 格式的文件打补丁即可；

这⾥的“Json格式的⽂件”是指⽂件内容是Json，⽂件后缀不⼀定是“.json”，可能是“.config”、“.cinematic”(过场动画)、 “.behevior”（⾏为树）等。

 

# Asset 类

StarAssets.cpp

Assets::doLoad()⽅法

loadJson()调⽤readJson()，readJson()调⽤jsonPatch()，这便是打补丁的函数；

jsonPatch()函数在StarJsonPatch.cpp中，是⼀个全局函数，它给游戏assets中Json格式的⽂件打补丁并返回结果。

[Modding:Basics - Starbounder - Starbound Wiki](https://starbounder.org/Modding:Basics)

 

回看m_root成员变量

ClientApplication::changeState

直到游戏场景转换到Splash，m_root调⽤fullyLoad()，这时才开始多线程加载全部游戏 assets。虽然之前调⽤m_root->assets()建⽴了整体assets相应的Assets类的实例，但是直到fullyLoad()被调⽤，才把assets内的各个资源实 例化成⼀个个相应的实例。

，ClientApplication::updateMods()加载的是Steam workshop中的mod。 