# 项目最外层 CMake

CMake 最小版本



Find SCCache 或者 Ccache

​	https://en.wikipedia.org/wiki/Ccache

​	Cache 是一种软件开发工具，用于缓存编译，大大加快重新编译时间；

​	https://xuanwo.io/reports/2023-04/

 	[Sccache](https://github.com/mozilla/sccache) 是由 mozilla 团队发起的类 [ccache](https://ccache.dev/) 项目

根据是否开启 Cache 设置变量

​	STAR_CCACHE

​	STAR_PRECOMPILED_HEADERS



Project 声明

设置 CMAKE_MODULE_PATH，提供路径给 include() 或者 find_package()



设置 CMAKE_CONFIGURATION_TYPES，声明给生成器 multi-config generators

设置 CMAKE_EXPORT_COMPILE_COMMANDS 打开，输出 json 格式文件；

设置 CMAKE_EXE_LINKER_FLAGS_RELWITHASSERTS ？？？



STAR_SOURCE_IDENTIFIER

​	include .cmake 文件执行 get_git_head_revision

STAR_ARCHITECTURE

​	include .cmake 文件执行 target_architecture

STAR_LITTLE_ENDIAN

​	内置模块，查询字节序

STAR_SYSTEM

​	具体操作系统类型

STAR_SYSTEM_FAMILY

​	系统类型 windows 还是 unix

STAR_COMPILER

​	编译器类型



根据不同的系统和环境，设置 Option

​	根据 STAR_COMPILER 区分 gnu 和 msvc

​	

STAR_BUILD_GUI 默认打开

​	关闭 STAR_BUILD_QT_TOOLS，STAR_ENABLE_STEAM_INTEGRATION，STAR_ENABLE_DISCORD_INTEGRATION



STAR_LUA_APICHECK 关闭

STAR_USE_JEMALLOC 关闭

STAR_USE_MIMALLOC 关闭

STAR_USE_RPMALLOC 关闭



打印报告发现的系统环境设置和所有 Option



SET_FLAG 函数

​	设置变量值 True

​	add_definitions 作为编译命令行参数，代码里面通过 #ifdef 进行判断；

STAR_ARCHITECTURE

​	set_flag(STAR_ARCHITECTURE_I386)

​	set_flag(STAR_ARCHITECTURE_X86_64)

STAR_SYSTEM

​	set_flag(STAR_SYSTEM_WINDOWS)

​	set_flag(STAR_SYSTEM_MACOS)

​	set_flag(STAR_SYSTEM_LINUX)

​	set_flag(STAR_SYSTEM_FREEBSD)

​	set_flag(STAR_SYSTEM_NETBSD)

STAR_SYSTEM_FAMILY

​	set_flag(STAR_SYSTEM_FAMILY_WINDOWS)

​	set_flag(STAR_SYSTEM_FAMILY_UNIX)

STAR_COMPILER

​	set_flag(STAR_COMPILER_GNU)

​	set_flag(STAR_COMPILER_CLANG)

​	set_flag(STAR_COMPILER_MSVC)



STAR_LUA_APICHECK

​	add_definitions(-DLUA_USE_APICHECK)

STAR_SYSTEM_

​	LUA_USE_WINDOWS 默认开了

​	add_definitions(-DLUA_USE_MACOSX)

​	add_definitions(-DLUA_USE_LINUX)

​	add_definitions(-DLUA_USE_POSIX)

add_definitions 。。。。



设置 C++ 编译器 Flag，根据不同的编译器设置对应的参数

​	CMAKE_C_FLAGS

​	CMAKE_CXX_FLAGS

​	CMAKE_C_FLAGS_RELWITHASSERTS

​	CMAKE_CXX_FLAGS_RELWITHASSERTS

​	CMAKE_C_FLAGS_RELWITHDEBINFO

​	CMAKE_CXX_FLAGS_RELWITHDEBINFO

​	CMAKE_C_FLAGS_RELEASE

​	CMAKE_CXX_FLAGS_RELEASE

​	CMAKE_SKIP_BUILD_RPATH 打开，不用系统默认路径



设置其它构建设置

​	CMAKE_MODULE_LINKER_FLAGS

​	CMAKE_RC_COMPILER_INIT

​	enable_language(RC)，CMAKE_RC_COMPILE_OBJECT

​	CMAKE_EXE_LINKER_FLAGS

​	CMAKE_EXE_LINKER_FLAGS_RELWITHASSERTS

​	CMAKE_C_STANDARD_LIBRARIES

​	CMAKE_CXX_STANDARD_LIBRARIES



STAR_USE_JEMALLOC

​	find_package，include_directories，设置 STAR_EXT_LIBS

STAR_USE_MIMALLOC 同理



find_package [find_package — CMake 4.0.0-rc4 Documentation](https://cmake.org/cmake/help/latest/command/find_package.html)

​	Module 模式，Config 模式，FetchContent redirection 模式

​	Module 模式先找 .cmake，再根据 Find Modules 找 CMake 下载；

find_package(ZLIB REQUIRED)

find_package(PNG REQUIRED)

find_package(Freetype REQUIRED)

find_package(Opus CONFIG REQUIRED)

find_package(OggVorbis REQUIRED)

find_package(zstd CONFIG REQUIRED)



include_directories(SYSTEM 一些 include 路径



set(STAR_EXT_LIBS ${STAR_EXT_LIBS} 新增



STAR_BUILD_GUI

​	find_package(SDL2 CONFIG REQUIRED)

​	 set(STAR_EXT_GUI_LIBS

​	find_package(OpenGL REQUIRED)

​	find_package(GLEW REQUIRED)

​	include_directories(SYSTEM 一些 include 路径



set(BUILD_SHARED_LIBS false)



设置输出目录

​	set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/../dist)

​	根据不同的 config 选项，设置输出目录



设置额外的源文件

​	set(STAR_EXTERN_INCLUDES ${PROJECT_SOURCE_DIR}/extern)

​	add_subdirectory(extern)

设置核心代码

​	set(STAR_CORE_INCLUDES

​	add_subdirectory(core)

设置 Base 通用代码

​	set(STAR_BASE_INCLUDES

​	add_subdirectory(base)

平台 API

​	set(STAR_PLATFORM_INCLUDES

​	add_subdirectory(platform)

核心游戏逻辑

​	set(STAR_GAME_INCLUDES

​	add_subdirectory(game)

谷歌测试默认没开

单机服务器

add_subdirectory(server)	

命令行工具

add_subdirectory(utility)

STAR_BUILD_GUI

​	add_subdirectory(application)

​	add_subdirectory(rendering)

​	add_subdirectory(windowing)

​	add_subdirectory(frontend)

​	add_subdirectory(client)

STAR_BUILD_QT_TOOLS

​	add_subdirectory(json_tool)

​	STAR_ENABLE_STEAM_INTEGRATION

​		add_subdirectory(mod_uploader)



# game 子目录