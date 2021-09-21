---
title: 编译Android ARM汇编源文件的两种姿势
date: 2020-09-29 16:45:17
tags: arm android ndk arm汇编编译
---
## 一 概要
在Android ndk开发中，特别是开发与安全相关功能的时候，经常需要使用汇编和c/c++的混合编程，本文介绍两种将arm汇编引入Android ndk编程的编译配置方式，供大家参考和学习。这两种方式包括：
1 AndroidStudio编译arm汇编源文件
2 android-ndk编译汇编源文件

<!--more-->

## 二 AndroidStudio3.x集成环境中编译arm汇编源文件（以arm64为例）

### 1 build.gradle中设置目标二进制文件运行架构
defaultConfig {
        ndk {
            abiFilters 'arm64-v8a'
        }
    }

### 2 CMakeLists.txt中的配置

#### 2.1 使能汇编编译
set(can_use_assembler TRUE)
enable_language(ASM)

add_definitions(-DANDROID -DOC_ARM_ASM)

#### 2.2 以静态库的方式单独编译汇编源文件
add_library( assembly-lib STATIC
             src/main/assembly/syscall.s )


#### 2.3 将静态库链接到目标共享库文件
target_link_libraries( # Specifies the target library.
                       native-lib
                       assembly-lib
                       # Links the target library to the log library
                       # included in the NDK.
                        )

## 三 通过android-ndk工具链手动编译汇编源文件（以arm32为例）

### 1 Application.mk中设置目标二进制文件运行架构
APP_ABI := armeabi-v7a

### 2 配置Android.mk
LOCAL_SRC_FILES := syscall.s

命令行下执行ndk-build即可将汇编源文件编译到目标共享库。
