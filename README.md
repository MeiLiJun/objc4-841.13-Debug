# objc4-841.13



官网地址 : https://opensource.apple.com/releases/

ENV :  Mac OS : 12.3 Xcode : 13.3  

objc4-841.13 : 源码来自 opensource（官网） -> releases -> macOS 12.3

  第二步中所需库大部分也从此版本系统中下载的对应版本 几个没有的在 Libc-825.40.1中



下载后的设置：

1. macosx.internal 错误：

  以下设置为 macOS

  PROJECT -> objc -> BuildSettings -> BaseSDK

  TARGETS -> objc -> BuildSettings -> BaseSDK



  TARGETS -> objc -> Build Phases -> Run Script(markgc)
  脚本内 macosx.internal 改为 macosx


2. 根目录下创建sysInclude文件夹,存放所需库文件（本文从macOS 12.3下下载对应的库版本）,

   配置到工程

  TARGETS -> objc -> BuildSettings -> Header Search Paths

  新增 $(SRCROOT)/sysInclude 

   

参照工程中 sysInclude 目录结构：(共 19 个.h文件) 

| 文件名                                | 查找位置                                                  |
| ------------------------------------- | :-------------------------------------------------------- |
| simple.h                              | libplatform-273.100.5/private/_simple.h                   |
| Block_private.h                       | libdispatch-1325.100.36/src/BlocksRuntime/Block_private.h |
| CrashReporterClient.h                 | Libc-825.40.1（非12.3系统内）                             |
| objc-shared-cache.h                   | dyld-955/include/objc-shared-cache.h                      |
| kern / restartable.h                  | xnu-8020.101.4/osfmk/kern/restartable.h                   |
| mach-o / dyld_priv.h                  | dyld-955/include/mach-o/dyld_priv.osh                     |
| os / bsd.h                            | Libc-1507.100.9/libdarwin/h/bsd.h                         |
| os / api.h                            | Libc-1507.100.9/os/api.h                                  |
| os / variant_private.h                | Libc-1507.100.9/os/variant_private.h                      |
| os / linker_set.h                     | Libc-1507.100.9/os/linker_set.h                           |
| os / lock_private.h                   | libplatform-273.100.5/private/os/lock_private.h           |
| os / tsd.h                            | xnu-8020.101.4/libsyscall/os/tsd.h                        |
| os / base_private.h                   | xnu-8020.101.4/libkern/os/base_private.h                  |
| os / reason_private.h                 | xnu-8020.101.4/libkern/os/reason_private.h                |
| pthread / spinlock_private.h          | libpthread-486.100.11/private/pthread/spinlock_private.h  |
| pthread / tsd_private.h               | libpthread-486.100.11/private/pthread/tsd_private.h       |
| sys / reason.h                        | xnu-8020.101.4/bsd/sys/reason.h                           |
| System / pthread_machdep.h            | Libc-825.40.1（非12.3系统内）                             |
| System / machine / cpu_capabilities.h | xnu-8020.101.4/osfmk/machine/cpu_capabilities.h           |



3. 删除 bridgeos(3.0) / bridgeos(4.0) / bridgeos 



4. 报错 ： 'CrashReporterClient.h' file not found 

  解决 ： TARGETS -> objc -> BuildSettings -> Preprocessor Macros 

​    添加 LIBC_NO_LIBCRASHREPORTERCLIENT

​     

5. 报错 ： library not found for -lCrashReporterClient 或者 -loah    

  TARGET -> objc -> Other Linker Flags -> Any macOS SDK

  删除 -loah 删除 -lCrashReporterClient



6. 注释掉含有 dyld_platform_version_macOS_ *报错的整个if语句*



7. 其他需要注释的报错

```c
//#error mismatch in debug-ness macros
```

```c++
//  if (!os_feature_enabled_simple(objc4, preoptimizedCaches, true)) {
//    DisablePreoptCaches = true;
//  }
```

```c++
//  if (!os_feature_enabled_simple(objc4, classRxSigning, false))
//    DisableClassRXSigningEnforcement = true;
```

```c++
//  if (!os_feature_enabled_simple(objc4, classRoSigningFaults, false))
//    DisableClassROFaults = true;
```

```c++
// 提取出来注释掉
// || sdkIsAtLeast(10_*12, 10*_0, 10_*0, 3*_0, 2_*0)*
  if (DebugPoolAllocation) {
      // OBJC_DEBUG_POOL_ALLOCATION or new SDK. Bad pop is fatal.*
    _objc_fatal
    ("Invalid or prematurely-freed autorelease pool %p.", token);
  }
```

```c++
// 提取出来注释掉
// && dyld_program_sdk_at_least(dyld_fall_2018_os_versions)
  if (!DisableTaggedPointerObfuscation) {...}
```

```c++
//STATIC_ASSERT((~ISA_MASK & MACH_VM_MAX_ADDRESS) == 0 ||
//       ISA_MASK + sizeof(void**) == MACH_VM_MAX_ADDRESS);
```



8. 'os/bsd.h' file not found

​        Use of undeclared identifier 'os_parse_boot_arg_string'

解决：（如步骤2 添加了os/bsd.h 和 os/api.h）




喜欢图文步骤的朋友请参考：
https://juejin.cn/post/7042148781747339272
