# 移植jrtplib到安卓平台

 发表于 2018-02-24

## 关于rtp协议

### RTP协议介绍

实时传输协议RTP（Real-time Transport Protocol）是网络传输协议的一种，构建与TCP/IP之上，广泛用于局域网推送视频与音频的推送。RTP协议本身比较复杂，而且各厂商基本不提供基于RTP协议的sdk，大多数是基于RTMP和RTSP，但是后边两者实时性远不如RTP高。为了实现将安卓手机屏幕录屏取得H264流，并将之分片或者组合发送至电脑端播放器，延时低于1s，最后选择采用RTP协议发送。

RTP报文由两部分组成：报头和有效载荷。RTP报头格式如图所示，其中：

1. V：RTP协议的版本号，占2位，当前协议版本号为2。
2. P：填充标志，占1位，如果P=1，则在该报文的尾部填充一个或多个额外的八位组，它们不是有效载荷的一部分。
3. X：扩展标志，占1位，如果X=1，则在RTP报头后跟有一个扩展报头。
4. CC：CSRC计数器，占4位，指示CSRC 标识符的个数。
5. M: 标记，占1位，不同的有效载荷有不同的含义，对于视频，标记一帧的结束；对于音频，标记会话的开始。
6. 同步信源(SSRC)标识符：占32位，用于标识同步信源。该标识符是随机选择的，参加同一视频会议的两个同步信源不能有相同的SSRC。
7. 特约信源(CSRC)标识符：每个CSRC标识符占32位，可以有0～15个。每个CSRC标识了包含在该RTP报文有效载荷中的所有特约信源。
8. PT: 有效载荷类型，占7位，用于说明RTP报文中有效载荷的类型，如GSM音频、JPEM图像等。
9. 序列号：占16位，用于标识发送者所发送的RTP报文的序列号，每发送一个报文，序列号增1。接收者通过序列号来检测报文丢失情况，重新排序报文，恢复数据。
10. 时戳(Timestamp)：占32位，时戳反映了该RTP报文的第一个八位组的采样时刻。接收者使用时戳来计算延迟和延迟抖动，并进行同步控制。

这里的同步信源是指产生媒体流的信源，它通过RTP报头中的一个32位数字SSRC标识符来标识，而不依赖于网络地址，接收者将根据SSRC标识符来区分不同的信源，进行RTP报文的分组。特约信源是指当混合器接收到一个或多个同步信源的RTP报文后，经过混合处理产生一个新的组合RTP报文，并把混合器作为组合RTP报文的SSRC，而将原来所有的SSRC都作为CSRC传送给接收者，使接收者知道组成组合报文的各个SSRC。

### RTP协议的复杂封包

网络传输的MTU最大值一般是1400-1500字节。rtp推荐使UDP作为传输协议，为了保证数据不丢失，我们需要将H264流的NALU单元限制在MTU以内。H264流的SPS和PPS只占很少的字节，并且在画面变化很少时产生的NAL单元也很小，这时候可能需要组包发送，将两个或者多个NAL单元封装在一个包内发送；当产生的NAL单元超过MTU的限制后，假如每个载体还要传送一个NAL，则可能会丢失数据，导致接收端接收的不是完整的一帧数据，这个时候需要分包发送，将一个NAL单元拆分成两个或者更多个包发送。
想想头都大了，因为分包和组合包

#### 单一NAL单元模式

对于 NALU 的长度小于 MTU 大小的包, 一般采用单一 NAL 单元模式.
对于一个原始的 H.264 NALU 单元常由 [Start Code] [NALU Header] [NALU Payload] 三部分组成, 其中 Start Code 用于标示这是一个
NALU 单元的开始, 必须是 “00 00 00 01” 或 “00 00 01”, NALU 头仅一个字节, 其后都是 NALU 单元内容.
打包时去除 “00 00 01” 或 “00 00 00 01” 的开始码, 把其他数据封包的 RTP 包即可.

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|F|NRI|  type   |                                               |
+-+-+-+-+-+-+-+-+                                               |
|                                                               |
|               Bytes 2..n of a Single NAL unit                 |
|                                                               |
|                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                               :...OPTIONAL RTP padding        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

#### 组合封包模式

当 NALU 的长度特别小时, 可以把几个 NALU 单元封在一个 RTP 包中.

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                          RTP Header                           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|STAP-A NAL HDR |         NALU 1 Size           | NALU 1 HDR    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         NALU 1 Data                           |
:                                                               :
+               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|               | NALU 2 Size                   | NALU 2 HDR    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         NALU 2 Data                           |
:                                                               :
|                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                               :...OPTIONAL RTP padding        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

#### 分包模式

当NALU的长度超过MTU时,就必须对NALU单元进行分片封包.也称为Fragmentation Units(FUs).

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| FU indicator  |   FU header   |                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               |
|                                                               |
|                         FU payload                            |
|                                                               |
|                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                               :...OPTIONAL RTP padding        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

这几种模式看着就很复杂，假如是非专业人士很难搞定。网上比较有名的就是ffmpeg和jrtplib，他们都对RTP协议做了较好的封装。这里我使用用C++编写的jrtplib工程移植到安卓平台。

## JRTPLIB介绍

jrtplib一个用C ++编写的面向对象库，旨在帮助开发人员使用RFC 3550中描述的实时传输协议（RTP）。

该库使得用户可以使用RTP发送和接收数据，而不用担心SSRC冲突，调度和传输RTCP数据等。用户只需要向库提供要发送的有效载荷数据，并且该库能给用户访问传入的RTP和RTCP数据的权限。

jrtplib支持定义于RFC3550中的RTP协议，它使得发送和接收RTP报文变得异常简单，用户不用担心SSRC冲突，也不用考虑如何传输RTCP数据，因为RTCP功能完全在内部实现，不需用户手动操作。
当发送RTP报文时，用户只需简单的给发送函数提供负载数据；当接收数据时，jrtplib提供了访问传入的RTP和RTCP数据的接口。

目前为止，jrtplib支持以下平台：

```
*GNU/Linux
*MS-Windows（Win32和WinCE）
*Solaris
当然也可以运行于其他类unix环境。
```

jthread封装了pthread，提供了一些特定的接口使用起来更方便一点。
jrtplib可以使用jthread库在后台自动轮询传入的数据，所以推荐安装jthread。当然如果没有安装jthread，jrtplib也能正常工作，但是需要用户自己轮询传入的数据了。3.x.x版本的jrtplib至少需要1.3.0版本的jthread。

[jrtplib文档地址](https://jrtplib.readthedocs.io/en/stable/)

[jrtplib-github地址](https://github.com/j0r1/JRTPLIB)

[jthread-github地址](https://github.com/j0r1/JThread)

两个库全都使用cmake构建，并且系统内提供了对主要平台的支持测试，保证在各个平台正常使用。现在我们需要借助ndk交叉编译为安卓平台的架构。

## 编译jthread

官方的jthread是一个基于pthead的封装库，用来解决unix平台多线程编程。封装置后调用相对简单，使用jthread可以轮询查询是否接收到rtp包并且取出。

文件结构如下：

```
└── JThread
    ├── CMakeLists.txt
    ├── ChangeLog
    ├── LICENSE.MIT
    ├── README.md
    ├── TODO
    ├── builddist.sh
    ├── cmake
    │   └── JThreadConfig.cmake.in
    ├── doc
    │   └── manual.tex
    ├── pkgconfig
    │   ├── CMakeLists.txt
    │   └── jthread.pc.in
    ├── sphinxdoc
    │   ├── Makefile
    │   ├── README.md
    │   └── source
    │       ├── _static
    │       ├── _templates
    │       └── conf.py
    └── src
        ├── CMakeLists.txt
        ├── jmutex.h
        ├── jmutexautolock.h
        ├── jthread.h
        ├── jthreadconfig.h.in
        ├── pthread
        │   ├── jmutex.cpp
        │   └── jthread.cpp
        └── win32
            ├── jmutex.cpp
            └── jthread.cpp
```

文件很少，主要的源文件在src文件夹下，这里的文件是实现jthread的主要代码，我们不用管，重点关注该文件夹下的`CMakeLists.txt`文件。doc、pkgconfi和spinxdoc三个文件夹是unix平台安装的文件，也可以不用管。主要的是CMakeLists.txt文件和cmake文件夹下的`JThreadConfig.cmake.in`。下面一起分析一下上面提到的三个需要关注的文件。
关于cmake的详细文档请参[考官方文档](https://cmake.org/cmake/help/v3.10/index.html)或者[这个系列文章](https://juejin.im/post/5a8ebe006fb9a0635a6574de)。

### 根目录下的CMakeList.txt文件

该文件是整个工程构建系统的入口。

```
cmake_minimum_required(VERSION 3.0)

project(jthread)
set(VERSION 1.3.3)
```

来看看这三个蛋疼的玩意，指定了使用cmake的最小版本，构建的工程的名称以及该库的版本。

```
include(CheckCXXSourceCompiles)
```

这个就牛逼了，是用来测试c源码是否包含某个功能，稍后在src文件夹下CMakeLists.txt文件介绍会用到。

```
set (_DEFAULT_LIBRARY_INSTALL_DIR lib)
if (EXISTS "${CMAKE_INSTALL_PREFIX}/lib32/" AND CMAKE_SIZEOF_VOID_P EQUAL 4)
	set (_DEFAULT_LIBRARY_INSTALL_DIR lib32)
elseif (EXISTS "${CMAKE_INSTALL_PREFIX}/lib64/" AND CMAKE_SIZEOF_VOID_P EQUAL 8)
	set (_DEFAULT_LIBRARY_INSTALL_DIR lib64)
endif ()

set(LIBRARY_INSTALL_DIR "${_DEFAULT_LIBRARY_INSTALL_DIR}" CACHE PATH "Library installation directory")
if(NOT IS_ABSOLUTE "${LIBRARY_INSTALL_DIR}")
	set(LIBRARY_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/${LIBRARY_INSTALL_DIR}")
endif()
```

这几个是关于库的安装路径设置，暂时忽略，因为我们在交叉编译的时候会手动指定安装目录。

```
find_package(Threads)
if (NOT CMAKE_USE_WIN32_THREADS_INIT)
	if (NOT CMAKE_USE_PTHREADS_INIT)
		message(FATAL_ERROR "Can find neither pthread support nor Win32 thread support")
	endif (NOT CMAKE_USE_PTHREADS_INIT)
endif (NOT CMAKE_USE_WIN32_THREADS_INIT)
```

find_package可以用来查询系统是否包含某个库，包含会返回变量成功，不包含变量返回NOTFOUND。
这个是用来寻找threads库，假如找到会生成以下变量:

```
CMAKE_THREAD_LIBS_INIT     - 库名称
CMAKE_USE_SPROC_INIT       - 使用sproc?
CMAKE_USE_WIN32_THREADS_INIT - 使用WIN32 threads?
CMAKE_USE_PTHREADS_INIT    - 使用pthreads
CMAKE_HP_PTHREADS_INIT     - 使用pthreads
```

稍后会用到其中的一些变量。
最后一行：

```
add_subdirectory(src)
```

这个是提供执行构建src文件夹下的CMakeLists.txt文件的一个入口，添加这句后src中的cmake文件就可以引用这个cmake文件中的一些变量和环境设置。

### src文件夹下的CMakeLists.txt文件

这个文件的内比较多，挑一些重要的讲一讲：

```
if (NOT MSVC OR JTHREAD_COMPILE_STATIC)
	set(JTHREAD_INSTALLTARGETS jthread-static)
	add_library(jthread-static STATIC ${SOURCES} ${HEADERS})
	set_target_properties(jthread-static PROPERTIES OUTPUT_NAME jthread)
	set_target_properties(jthread-static PROPERTIES CLEAN_DIRECT_OUTPUT 1)
	target_link_libraries(jthread-static ${CMAKE_THREAD_LIBS_INIT})
endif()

if ((NOT MSVC AND NOT JTHREAD_COMPILE_STATIC_ONLY) OR (MSVC AND NOT JTHREAD_COMPILE_STATIC))
	add_library(jthread-shared SHARED ${SOURCES} ${HEADERS})
	set_target_properties(jthread-shared PROPERTIES VERSION ${VERSION})
	set_target_properties(jthread-shared PROPERTIES OUTPUT_NAME jthread)
	set_target_properties(jthread-shared PROPERTIES CLEAN_DIRECT_OUTPUT 1)
	set(JTHREAD_INSTALLTARGETS ${JTHREAD_INSTALLTARGETS} jthread-shared)
	target_link_libraries(jthread-shared ${CMAKE_THREAD_LIBS_INIT})
endif ()
```

这段代码的作用是在非windows平台下构建动态库和静态库，假如不需要全部构建只需要构建动态库或者静态库，注释掉其中的一部分即可(上边构建静态库.a文件，下边构建动态库.so文件)。

```
install(FILES ${HEADERS} DESTINATION include/jthread)
install(TARGETS ${JTHREAD_INSTALLTARGETS} DESTINATION ${LIBRARY_INSTALL_DIR})
```

这两句是用来安装文件，cmake系统默认的安装路径是/usr/local/*，假如设置了`CMAKE_INSTALL_PREFIX`,则会改变默认的安装路径到该变量指向的路径。这两句话的作用是将变量`HEADERS`包含的文件-主要是头文件-安装到`CMAKE_INSTALL_PREFIX`指向路径的`include/jthread`文件夹下,同理，动态库和静态库会安装到指向路径的`lib`文件夹下。

关于上边提到的测试，`include(CheckCXXSourceCompiles)`：

```
# Test pthread_cancel (doesn't exits on Android)
set(CMAKE_REQUIRED_LIBRARIES ${CMAKE_THREAD_LIBS_INIT})
check_cxx_source_compiles("#include <pthread.h>\nint main(void) { pthread_cancel((pthread_t)0); return 0; }" JTHREAD_HAVE_PTHREADCANCEL)
if (NOT JTHREAD_HAVE_PTHREADCANCEL)
    #message("Enabling JTHREAD_SKIP_PTHREAD_CANCEL")
	add_definitions(-DJTHREAD_SKIP_PTHREAD_CANCEL)
else ()
	#message("pthread_cancel appears to exist")
endif (NOT JTHREAD_HAVE_PTHREADCANCEL)
```



这个功能是测试pthread是否有cancel函数,并将结构存储在`JTHREAD_HAVE_PTHREADCANCEL`变量中，程序执行成功则会返回true，执行失败则会返回false，然后执行if中的语句，来添加跳过执行cancel函数的变量，这样在编译后程序就不会执行cancel函数了。

来看一下另一端代码：

```
configure_file("${PROJECT_SOURCE_DIR}/cmake/JThreadConfig.cmake.in" 
	       "${PROJECT_BINARY_DIR}/cmake/JThreadConfig.cmake")
```

configure_file的作用是将第一个参数指向的文件复制到第二个参数指向的路径，也会重命名该文件，并且在生成的文件中替换源文件中的变量。看一下源文件，指定了三个变量，后两个变量会随着你的参数指定而变化。

```
set(JTHREAD_FOUND 1)

set(JTHREAD_INCLUDE_DIRS "${CMAKE_INSTALL_PREFIX}/include")

set(JTHREAD_LIBRARIES ${JTHREAD_LIBS_CMAKECONFIG})
```

这个文件本身并没有太大的作用，主要目的是让jrtplib寻找到jthread库。实际在jrtplib中定义的find宏中提供了两种方式，不是必须采用这种方式。

### 编译全abi的jthread库

用ndk-build来构建时比较简单的，但是要自己编写.mk文件。我闲的蛋疼写了一个bash脚本来生成全abi的动态库和静态库。安卓支持的abi版本共有7种，armeabi arm64-v8a armeabi-v7a mips mips64 x86 x86_64，我们需要分别生成这些版本的动态库.so文件与静态库.o文件。

```
#!/bin/bash

#ndk的路径，替换为自己的路径
export NDK_PATH=/Users/rangaofei/Library/Android/sdk/ndk-bundle

#将要构建的架构
TARGETS=(armeabi arm64-v8a armeabi-v7a mips mips64 x86 x86_64)

#清除build文件夹下的内容
function clean_build() {
	if ([ -d build ]); then
		echo "prepare to clean cache"
		(rm -rf ./build/*)
		echo "complete"
	else
		echo "build is not a directory"
		exit 0
	fi
}

function prepare_build() {
	# 检测是否有Build文件夹，有的话删除文件夹，没有的话创建文件夹
	if ([ -e build ]); then
		echo "you already have build dir"
		clean_build
	else
		echo "prepare to create dir build"
		mkdir build
	fi
	(
		cd build
		for dir in ${TARGETS[@]}; do
			mkdir $dir
		done
	)
}

function prepare_target() {
	#检测是否有所有的target文件夹，有则删除，没有则创建
	if ([ -e target ] && [ -d target ]); then
		echo "prepare to clean target"
		rm -rf ./target/*
		echo "clean target complete"
	else
		echo "you not have target_dir,we will create it"
		mkdir target
	fi
}

function create_child_dir() {
	if ([ -e target ]); then
		(
			cd target
			mkdir $1
		)
	else
		echo "target is not a dir"
	fi
}

function move_to_target() {
	pwd
	if ([ -e ./build/$1/src/libjthread.a ]); then
		echo "prepare move target to ./target/$1"
		cp ./build/$1/src/libjthread.a ./target/$1
		cp ./build/$1/src/libjthread.so ./target/$1
		echo "move to ./target/$1 finished"
	else
		echo "move error $1"
	fi
}

function build_lib() {
	cd build/$1
	cmake ../.. \
		-DCMAKE_SYSTEM_NAME=Android \
		-DCMAKE_SYSTEM_VERSION=21 \
		-DCMAKE_ANDROID_ARCH_ABI=$1 \
		-DCMAKE_ANDROID_NDK=$NDK_PATH \
		-DCMAKE_ANDROID_STL_TYPE=gnustl_static \
		-DCMAKE_INSTALL_PREFIX=$(pwd)
}

function create_all_child_dir() {
	for dir in ${TARGETS[@]}; do
		create_child_dir $dir
		echo "$dir created"
	done
}

function create_all_target() {
	prepare_build
	prepare_target
	create_all_child_dir
	for target in ${TARGETS[@]}; do
		(
			build_lib $target
			make
			make install
		)
		move_to_target $target
	done
}

function sbuild() {
	echo "-------$1"
	case $1 in
	"all")
		create_all_target
		;;
	"*") ;;

	esac
}

sbuild_list=("all")
function _sbuild() {
	local cur
	COMPREPLY=()
	cur="${COMP_WORDS[COMP_CWORD]}"
	COMPREPLY=($(compgen -W "${sbuild_list[*]}" -- ${cur}))
	return 0
}
complete -o filenames -F _sbuild sbuild
```

关于shell脚本有兴趣的话可以参考[系列文章](https://rangaofei.github.io/tags/Shell/).

关于cmake构建的指令是

```
cmake ../.. \
		-DCMAKE_SYSTEM_NAME=Android \
		-DCMAKE_SYSTEM_VERSION=21 \
		-DCMAKE_ANDROID_ARCH_ABI=$1 \
		-DCMAKE_ANDROID_NDK=$NDK_PATH \
		-DCMAKE_ANDROID_STL_TYPE=gnustl_static \
		-DCMAKE_INSTALL_PREFIX=$(pwd)
```

这个命令是执行交叉编译的命令，`-DCMAKE_SYSTEM_NAME=Android`指定了编译平台是安卓平台；`-DCMAKE_SYSTEM_VERSION=21`指定了api版本是21；`-DCMAKE_ANDROID_ARCH_ABI=$1`指定了构建的abi为该函数接收的参数，因为会便利TARGETS数组，所以会执行七次；-DCMAKE_INSTALL_PREFIX=$(pwd)指定了我们上边提到的安装路径为当前目录，则会在当前目录下创建include/thread文件夹来存放头文件，lib文件夹存放库文件。

上边的脚本共干了以下几件事

1. 创建buil文件夹，并在build文件夹下创建七种abi文件夹库，用来执行cmake的外部构建，存放所有生成的文件。
2. 创建target文件夹，并在target文件下创建七种abi文件夹库，将生成的库文件拷贝到这个文件夹下
3. 遍历所有的abi，进入build对应的文件夹下，执行外部构建，并且执行make和make install完成构建
4. 拷贝所有的lib文件夹下的库到对应的target文件夹下

我们在命令行执行下边两个命令

```
source build.sh
sbuild all
```

> 温馨提示：最好在bash中执行上述命令，zsh会发生未知的错误，并且不支持我编写的自动补全。

构建会自动执行并输出日志。构建完成后来看一下目录结构

```
.
├── build
│   ├── arm64-v8a
│   ├── armeabi
│   ├── armeabi-v7a
│   ├── mips
│   ├── mips64
│   ├── x86
│   └── x86_64
├── cmake
├── doc
├── pkgconfig
├── sphinxdoc
│   └── source
├── src
│   ├── pthread
│   └── win32
└── target
    ├── arm64-v8a
    ├── armeabi
    ├── armeabi-v7a
    ├── mips
    ├── mips64
    ├── x86
    └── x86_64
```

可以看到build文件夹和target文件夹已经按我们预期好的形式创建了，并且都有对应的abi文件夹下已经生成了所有的静态库和动态库。这样我们的jthrea库就构建好了，看一下cmake文件夹下的JThradConfig.cmake文件，这个文件位于`build/$abi/cmake`文件夹下，我选的是arm64-v8a：

```
set(JTHREAD_FOUND 1)

set(JTHREAD_INCLUDE_DIRS "/Users/rangaofei/Documents/program/JThread/build/arm64-v8a/include")

set(JTHREAD_LIBRARIES  "-L/Users/rangaofei/Documents/program/JThread/build/arm64-v8a/lib" "-ljthread")
```

这里已经替换好了我当前的目录。

## 编译jrtplib

从github上下载好后，目录结构与jthread基本相似，根目录下的CMakeLists.txt同样是构建的入口，src下是源文件，此处注意一个example文件夹，这个是用来测试jtrplib的，由于我们是安卓平台不需要这个文件夹的例子，所以找到200行左右的代码

```
add_subdirectory(examples)
```

将它注释掉，系统就不会构建所有的examples了。

cmake文件夹下有一些文件，其中有三个模块供构建时使用。，重点介绍一下这个findjthread模块，其他的两个比较简单。

### findjthread.cmake模块

这个模块用来寻找jthread库。上边提到了寻找jthread库可以用JThreadConfig.cmake文件来查询

```
find_package(JThread QUIET NO_MODULE)

if (NOT JTHREAD_FOUND) # Config file could not be found
	find_path(JTHREAD_INCLUDE_DIR jthread/jthread.h)
	
	set(JTHREAD_INCLUDE_DIRS ${JTHREAD_INCLUDE_DIR})

	if (UNIX)
		find_library(JTHREAD_LIBRARY jthread)
		if (JTHREAD_LIBRARY)
			set(JTHREAD_LIBRARIES ${JTHREAD_LIBRARY})
			find_library(JTHREAD_PTHREAD_LIB pthread)
			if (JTHREAD_PTHREAD_LIB)
				set(JTHREAD_LIBRARIES ${JTHREAD_LIBRARY} ${JTHREAD_PTHREAD_LIB})
			endif(JTHREAD_PTHREAD_LIB)
		endif (JTHREAD_LIBRARY)
	else (UNIX)
		find_library(JTHREAD_LIB_RELEASE jthread)
		find_library(JTHREAD_LIB_DEBUG jthread_d)

		if (JTHREAD_LIB_RELEASE OR JTHREAD_LIB_DEBUG)
			set(JTHREAD_LIBRARIES "")
			if (JTHREAD_LIB_RELEASE)
				set(JTHREAD_LIBRARIES ${JTHREAD_LIBRARIES} optimized ${JTHREAD_LIB_RELEASE})
			endif (JTHREAD_LIB_RELEASE)
			if (JTHREAD_LIB_DEBUG)
				set(JTHREAD_LIBRARIES ${JTHREAD_LIBRARIES} debug ${JTHREAD_LIB_DEBUG})
			endif (JTHREAD_LIB_DEBUG)
		endif (JTHREAD_LIB_RELEASE OR JTHREAD_LIB_DEBUG)
	endif(UNIX)
endif (NOT JTHREAD_FOUND)

include(FindPackageHandleStandardArgs)

find_package_handle_standard_args(JThread DEFAULT_MSG JTHREAD_INCLUDE_DIRS JTHREAD_LIBRARIES)
```

这个模块中使用了find_package和find_path,那我们需要在命令行中指定一个参数：`CMAKE_FIND_ROOT_PATH`,当使用交叉编译时这个命令是用来提供find_package和find_path的寻找路径，稍后编写的shell脚本中会设置这个变量。

### 找不到ifaddrs

在交叉编译的过程中，需要使用到库ifaddrs，在unix系统中这个属于必须实现的库，但是很可惜，安卓中没有，在编译的时候报错，提示找不到关于ifaddrs的函数。所以要使用这个库必须手动导入，这里为了方便我就直接拷贝过来了这两个文件，github地址是https://github.com/morristech/android-ifaddrs。然后我们需要在src文件夹下的CMakeLists.txt中找到两个变量`HEADERS`和`SOURCES`，这是两个数组，前者定义了所有的头文件，后者定义了所有的源文件，我们将为ifaddrs的添加的两个文件ifaddrs.h添加到HEADERS数组，ifaddrs.c添加到SOURCES数组。

到这里我们的准备工作基本结束了，下一步要编写shell脚本生成全abi。
基本步骤与编译jthread库相似：

1. 创建buil文件夹，并在build文件夹下创建七种abi文件夹库，用来执行cmake的外部构建，存放所有生成的文件。
2. 创建target文件夹，并在target文件下创建七种abi文件夹库，将生成的库文件拷贝到这个文件夹下
3. 遍历所有的abi，进入build对应的文件夹下，执行外部构建，并且执行make和make install完成构建
4. 拷贝所有的lib文件夹下的库到对应的target文件夹下

命令相似度很高，我就不写了，真不要看一下cmake的构建指令:

```
cmake ../.. \
		-DCMAKE_SYSTEM_NAME=Android \
		-DCMAKE_SYSTEM_VERSION=21 \
		-DCMAKE_ANDROID_ARCH_ABI=$1 \
		-DCMAKE_ANDROID_NDK=$NDK_PATH \
		-DCMAKE_ANDROID_STL_TYPE=gnustl_static \
		-DCMAKE_INSTALL_PREFIX=$(pwd) \
        -DCMAKE_FIND_ROOT_PATH=/Users/rangaofei/Documents/program/JThread/build/$1
```



除了最后一行，其他与jthread的构建基本一致。
最后指定了一个变量`-DCMAKE_FIND_ROOT_PATH`,这变量就是我们前边提到的寻找jthread的文件夹路径。

执行命令

```
surce build.sh
sbuild all
```

> 温馨提示：最好在bash中执行上述命令，zsh会发生未知的错误，并且不支持我编写的自动补全。

这个库文件较多，构建时间会很长：

```
.
├── build
│   ├── arm64-v8a
│   ├── armeabi
│   ├── armeabi-v7a
│   ├── mips
│   ├── mips64
│   ├── x86
│   └── x86_64
├── cmake
├── doc
├── examples
├── pkgconfig
├── sphinxdoc
│   └── source
├── src
│   ├── extratransmitters
│   └── ifaddrs
├── target
│   ├── arm64-v8a
│   ├── armeabi
│   ├── armeabi-v7a
│   ├── mips
│   ├── mips64
│   ├── x86
│   └── x86_64
├── tests
└── tools
```

构建完成后同样在build和target文件夹下生成了这么多库。

## JNI调用

终于到最后一步了,我们编写了这么蛋疼的东西只为这一下。此处以静态库.a文件作为导入库，修改CMakeLists.txt文件如下：

```
cmake_minimum_required(VERSION 3.4.1)

add_library(native-lib
            SHARED
            src/main/cpp/native-lib.cpp )
add_library( jrtp
             STATIC
             IMPORTED )
set_target_properties(jrtp PROPERTIES IMPORTED_LOCATION
${CMAKE_SOURCE_DIR}/src/main/jniLibs/${ANDROID_ABI}/libjrtp.a)


add_library(jthread STATIC IMPORTED)
set_target_properties(jthread PROPERTIES IMPORTED_LOCATION
${CMAKE_SOURCE_DIR}/src/main/jniLibs/${ANDROID_ABI}/libjthread.a)


include_directories(
             src/main/cpp/include/jrtplib3
             src/main/cpp/include
            )


find_library( # Sets the name of the path variable.
              log-lib

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log )


target_link_libraries( # Specifies the target library.
                       native-lib

                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib}
                      )

target_link_libraries(native-lib jrtp jthread)
```

native-lib是我们的目标库，jrtp和jthread是我们自己构建好的库导入进来的，所以在导入库的时候指定了IMPORT属性，并且指定了properties中库的路径，否则系统会提示连接失败。

我们工程中src/main中的主要文件夹如下:

```
-- cpp
|   `-- include
|       |-- jrtplib3
|       `-- jthread
|-- java
|   `-- com
|       `-- saka
|-- jniLibs
|   |-- arm64-v8a
|   |-- armeabi
|   |-- armeabi-v7a
|   |-- mips
|   |-- mips64
|   |-- x86
|   `-- x86_64
`-- res
    |-- drawable
    |-- drawable-v24
    |-- layout
    |-- mipmap-anydpi-v26
    |-- mipmap-hdpi
    |-- mipmap-mdpi
    |-- mipmap-xhdpi
    |-- mipmap-xxhdpi
    |-- mipmap-xxxhdpi
    `-- values
```

cpp文件夹下有native-lib.cpp文件和include文件夹，include文件夹下有所有的jrpt和jthread头文件。

jniLibs文件夹中存放着所有的abi文件夹，也就是所有的库文件：

```
|-- arm64-v8a
|   |-- libjrtp.a
|   |-- libjrtp.so
|   `-- libjthread.a
|-- armeabi
|   |-- libjrtp.a
|   |-- libjrtp.so
|   `-- libjthread.a
|-- armeabi-v7a
|   |-- libjrtp.a
|   |-- libjrtp.so
|   `-- libjthread.a
|-- mips
|   |-- libjrtp.a
|   |-- libjrtp.so
|   `-- libjthread.a
|-- mips64
|   |-- libjrtp.a
|   |-- libjrtp.so
|   `-- libjthread.a
|-- x86
|   |-- libjrtp.a
|   |-- libjrtp.so
|   `-- libjthread.a
`-- x86_64
    |-- libjrtp.a
    |-- libjrtp.so
    `-- libjthread.a
```

这里我把libjrtp.so文件也复制进来了，为了方便下边讲解如何用动态库构建项目。

依赖动态库构建相对简单一些，因为不需要传递依赖我们native-lib库依赖了jtrp,而jrtp依赖了jthread，在使用静态库的时候需要指定链接所有的依赖库，而使用动态库可以不用，值需要依赖jrtp：

```
cmake_minimum_required(VERSION 3.4.1)

add_library(native-lib
            SHARED
            src/main/cpp/native-lib.cpp )
add_library( jrtp
               SHARED
             # STATIC
             IMPORTED )
set_target_properties(jrtp PROPERTIES IMPORTED_LOCATION
${CMAKE_SOURCE_DIR}/src/main/jniLibs/${ANDROID_ABI}/libjrtp.so)


# add_library(jthread STATIC IMPORTED)
# set_target_properties(jthread PROPERTIES IMPORTED_LOCATION
# ${CMAKE_SOURCE_DIR}/src/main/jniLibs/${ANDROID_ABI}/libjthread.a)


include_directories(
             src/main/cpp/include/jrtplib3
             src/main/cpp/include
            )


find_library( # Sets the name of the path variable.
              log-lib

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log )


target_link_libraries( # Specifies the target library.
                       native-lib

                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib}
                      )

# target_link_libraries(native-lib jrtp jthread)
target_link_libraries(native-lib jrtp)
```

去除了jthread依赖，指定了jrtp为SHARED动态库，修改了路径指向了libjrtp.so文件，同时删除了最后的依赖jthread。

native-lib.cpp中的主要代码如下：

```
destport = 8006;

num = 10;

RTPUDPv4TransmissionParams transparams;
RTPSessionParams sessparams;
sessparams.SetOwnTimestampUnit(1.0 / 10.0);

sessparams.SetAcceptOwnPackets(true);
transparams.SetPortbase(portbase);
status = sess.Create(sessparams, &transparams);
checkerror(status);
uint8_t localip[] = {192, 168, 31, 122};
RTPIPv4Address addr(localip, destport);
status = sess.AddDestination(addr);
checkerror(status);
for (i = 1; i <= num; i++) {
    LOGD("Sending packet");
    status = sess.SendPacket((void *) "1234567890", 10, 0, false, 10);
    checkerror(status);

    sess.BeginDataAccess();

    sess.EndDataAccess();

    RTPTime::Wait(RTPTime(1, 0));
}
```

这个是引用的example1
中的例子，向主机发送10次以rtp协议包好的”1234567890”字符串，每发送一次会打印一次日志到控制台。端口最好设置为偶数。
我们运行一下程序，控制台日志如下：

```
02-24 20:15:16.760 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:17.760 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:18.770 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:19.760 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:20.770 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:21.770 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:22.770 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:23.770 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
02-24 20:15:24.770 10059-10114/com.saka.myapplication E/System.out.c: Sending packet
```

可以看到数据发送成功了，每隔一秒钟数据发送一次，共十次。

由于我在mac上调试，小程序用不到wireshark这种大型软件，推荐一个移植mac平台的socket调试小工具-[sokit](https://juejin.im/post/5a77cb456fb9a0634e6c6c14)，体积小，使用简便.

启动程序后监听到的数据如下:



![image-20211213103219686](C:\Users\intel\AppData\Roaming\Typora\typora-user-images\image-20211213103219686.png)

此处没有解析rtp协议，因为是测试性质的。假如需要解析rtp数据，wireshark内置解析器。

## 关于大小端问题

记得原来使用netty传送数据到电脑时碰到过这个问题，因为大小端不一致导致的电脑端接收数据错误。
首先网络传输数据采用的是大端模式，这个符合人们的阅读习惯。而c#默认是的使用小端，会将高位与低位倒序，读取时发生了错误。

大小端这个问题是真的坑，因为在java中始终是大端模式(Big-endian)，也就是从左到右排列，符合人们的阅读习惯。
但是在jni编程中，是小端模式(little-endoan)，与古代人从右到左的阅读方式类似。关于大小端问题这里不做详细讨论，有很多文章写过这些内容，我主要讲一下如何修改修改构建的库为小端：

首先找到106行左右的代码：

```
option (JRTPLIB_USE_BIGENDIAN "Target platform is big endian" ON)
```

这段代码就是用来控制是否是大端的，我们只需要改为OFF即可：

```
option (JRTPLIB_USE_BIGENDIAN "Target platform is big endian" OFF)
```

[# Android](https://rangaofei.github.io/tags/Android/)

[shell脚本生成安卓全abi动态库与静态库](https://rangaofei.github.io/2018/02/22/shell脚本生成安卓全abi动态库与静态库/)



[安卓解码器MediaCodec解析
  ](https://rangaofei.github.io/2018/03/09/安卓解码器MediaCodec解析/)