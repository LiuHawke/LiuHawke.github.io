---
title: "Unity-移动端打包记录（持续更新）"
date: 2023-03-05T20:34:23+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052044934.jpg
---

# Android

## Gradle版本问题

本地打包的大部分错误都是因为这个问题，这是因为笔者接入的SDK自定义了gradle的插件版本，这个再unity本身其实已经定义过了，但是自己是可以通过修改build.gradle进行修改的。查看unity本身gradel的插件版本的路径是：Editor\Data\PlaybackEngines\AndroidPlayer\Tools\GradleTemplates\baseProjectTemplate.gradle,以2019.4.28版本为例，里面代码为：

```java
allprojects {
    buildscript {
        ***
        dependencies {
            ***
            classpath 'com.android.tools.build:gradle:3.4.0'
            **BUILD_SCRIPT_DEPS**
        }
    }
    ***
}
***
```

如上图，可以看到插件版本为3.4.0，如果unity里本身修改了baseProjectTemplate.gradle就按照修改后的来，这个文件夹内所有的gradle和properties都是默认的，如果程序里修改就按照程序里的来。修改的方法在Editor–>ProjectSettings–>Player–>Publishing Settings，如下图所示：



![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052046711.png)



如上图，其实就是对应编辑器文件夹下的gradle文件，如果打勾就会在pluging/Android文件夹下生成对应的文件，就可以直接修改，不再按照unity默认的来，就可以修改配置了。

经过上面的介绍已经知道如何查看并修改unity的gradle插件版本，下面就是修改对应的gradle版本。首先打开Editor–>Preference–>External Tools就可以看到Android的打包环境配置。



![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052046717.png)



2020版本以后的Unity是默认路径下就自己配置好环境，选择默认就可以，但是依旧可能会存在环境不存在或者版本不对，所以可以自己配置，这样修改也方便。这里有需要特别关注的一点也是大部分打包失败的原因，就是gradle的版本和对应的插件版本是有对照关系的，必须对照上才能正常打包。对应关系如下图：



![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052046727.png)

只要配置好对应的关系就行了。

## APK+obb分包无法运行问题

因为Google商店对上传的apk有内存限制，要求是100M以内，这里推荐使用的是APk+OBB进行分包，根据最新的要求是要求使用AAB包，这里先介绍APK+OBB的分包遇到的问题。

分开打包的方法是Editor–>ProjectSettings–>Player–>Publishing Settings里，勾选上最下面的Split Application Binary。



![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052048439.png)

这个是可以代码控制的：

```c#
PlayerSettings.Android.useAPKExpansionFiles = true;
```

分包后如何在手机上运行呢，这里只需要安装分包后的APK，然后在手机上运行，发现第一次运行不成功，这是因为资源都在OBB中，所以无法正常运行，这里只需要吧自己的OBB改好名字放在对应的文件夹就行了。然后再运行就可以了。

文件夹地址：手机目录\Android\obb"APP的包名”
OBB文件的名字: main.安卓内部版本号.APP包名.obb (举例：main.102.com.XXX.XXX.XXX.obb)

## 华为手机出现水滴屏无法适配的问题

笔者的项目要求手机在遇到水滴屏或者刘海屏的时候，上面显示黑条不进行渲染就可以，笔者查了一下unity的设置方法，发现只需要不勾选Editor–>ProjectSettings–>Resolution and Presentation里的Render outside safe area即可。



![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052052992.png)

但是笔者发现APP在某个测试的华为手机上依旧渲染了，最后发现是接入的SDK里设置了华为手机的屏幕渲染。在华为手机Android8.0的适配方案是在AndroidManfiest里面添加下面的话即可，笔者发现接入的SDK设置了这个，于是去掉就没有问题了。

```c#
<meta-data android:name="android.notch_support" android:value="true"/> 
```

这里列举一下小米手机的适配方案是：

```c#
<meta-data
 android:name="notch.config"
 android:value="portrait|landscape"/>
```



## 华为荣耀系列闪退

 ```
 2023-02-22 14:34:08.234 12494-12494 AndroidRuntime          pid-12494                            E  FATAL EXCEPTION: main
                                                                                                     Process: com.xg.legendofliqi, PID: 12494
                                                                                                     java.lang.Error: *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
                                                                                                     Version '2021.3.1f1c1 (0cb3bd085a01)', Build type 'Release', Scripting Backend 'il2cpp', CPU 'arm64-v8a'
                                                                                                     Build fingerprint: 'HONOR/FNE-AN00/HNFNE:12/HONORFNE-AN00/6.1.0.178C00:user/release-keys'
                                                                                                     Revision: '0'
                                                                                                     ABI: 'arm64'
                                                                                                     Timestamp: 2023-02-22 14:34:03+0800
                                                                                                     pid: 12494, tid: 12580, name: Thread-9  >>> com.xg.legendofliqi <<<
                                                                                                     uid: 10052
                                                                                                     signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x100000071
                                                                                                         x0  0000000000000000  x1  0000000000000001  x2  00000075508123b8  x3  0000000000000001
                                                                                                         x4  ffffffffffffffff  x5  00000075b0c07208  x6  0000000000000000  x7  0000000000000000
                                                                                                         x8  0000000000000000  x9  0000000000010cc0  x10 0000000000000000  x11 0000000000000000
                                                                                                         x12 0000000000000a80  x13 0000000000000000  x14 431bde82d7b634db  x15 0000000100000001
                                                                                                         x16 00000076512d3bb8  x17 00000079a4bec168  x18 000000753f646000  x19 0000000000000000
                                                                                                         x20 ffffffffffffffff  x21 000000777bdb99b0  x22 00000075b0bf2400  x23 00000075508123f8
                                                                                                         x24 00000075b0bf2278  x25 0000007550812740  x26 00000075b0be3c10  x27 00000075508128d8
                                                                                                         x28 000000761018e900  x29 0000007550812360
                                                                                                         sp  0000007550812360  lr  0000007650856d54  pc  00000075513c0730
 
                                                                                                     backtrace:
                                                                                                           #00 pc 00000000000a9730  /vendor/lib64/hw/vulkan.adreno.so (qglinternal::vkWaitForFences(VkDevice_T*, unsigned int, VkFence_T* const*, unsigned int, unsigned long)+184) (BuildId: 5839fbcc06912e00a12c12db16914158)
                                                                                                           #01 pc 0000000000781d50  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #02 pc 000000000075f864  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #03 pc 0000000000781260  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #04 pc 0000000000760a08  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #05 pc 000000000076ec38  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #06 pc 0000000000758714  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #07 pc 0000000000750994  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #08 pc 000000000075a9f4  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #09 pc 00000000006a71d4  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #10 pc 00000000007508fc  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #11 pc 00000000006a8f00  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #12 pc 00000000008a5e50  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #13 pc 00000000008a7c18  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
 2023-02-22 14:34:08.234 12494-12494 AndroidRuntime          pid-12494                            E        #14 pc 000000000089f4a8  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #15 pc 000000000033070c  /data/app/~~WnZeyO9JhYbG9GbePcBSVg==/com.xg.legendofliqi-qTxoOJYDPp2KJv3Yr8KuPA==/lib/arm64/libunity.so (BuildId: 73ee1682c58347912d029a798789df982330b7d9)
                                                                                                           #16 pc 00000000000b3ea0  /apex/com.android.runtime/lib64/bionic/libc.so (__pthread_start(void*)+264) (BuildId: 82e5b2ff86b193c94139353a92c4af29)
                                                                                                           #17 pc 0000000000053880  /apex/com.android.runtime/lib64/bionic/libc.so (__start_thread+64) (BuildId: 82e5b2ff86b193c94139353a92c4af29)
 
                                                                                                     	at vulkan.qglinternal::vkWaitForFences(VkDevice_T*, unsigned int, VkFence_T* const*, unsigned int, unsigned long)(vkWaitForFences:184)
                                                                                                     	at libunity.0x781d50(Native Method)
                                                                                                     	at libunity.0x75f864(Native Method)
                                                                                                     	at libunity.0x781260(Native Method)
                                                                                                     	at libunity.0x760a08(Native Method)
                                                                                                     	at libunity.0x76ec38(Native Method)
                                                                                                     	at libunity.0x758714(Native Method)
                                                                                                     	at libunity.0x750994(Native Method)
                                                                                                     	at libunity.0x75a9f4(Native Method)
                                                                                                     	at libunity.0x6a71d4(Native Method)
                                                                                                     	at libunity.0x7508fc(Native Method)
                                                                                                     	at libunity.0x6a8f00(Native Method)
                                                                                                     	at libunity.0x8a5e50(Native Method)
                                                                                                     	at libunity.0x8a7c18(Native Method)
                                                                                                     	at libunity.0x89f4a8(Native Method)
                                                                                                     	at libunity.0x33070c(Native Method)
                                                                                                     	at libc.__pthread_start(void*)(__pthread_start:264)
                                                                                                     	at libc.__start_thread(__start_thread:64)
 ```

打包版本是Unity 2021.3.1f1c1,有使用最新版Unity打包但还是一样的空包一样会闪退

错误日志是这样的截图找不到了可以看到最关键的错误信息就是``vulkan.qglinternal::vkWaitForFences(VkDevice_T*, unsigned int, VkFence_T* const*, unsigned int, unsigned long)(vkWaitForFences:184)``

### 什么是Vulkan？

是一个跨平台的2D和3D绘图应用程序接口
在2015年游戏开发者大会（GDC）上发表
是OpenGL的下一代版本，旨在提供更低的CPU开销与更直接的GPU控制

Auto Graphics API:自动图形接口（建议选择自动）

勾选：Unity会自动尝试使用对应图形API接口进行图形处理
默认使用Vulkan，如果设备不支持Vulkan，Unity会退到GLES3.2、GLES3.1或GLES3.0

不勾选：可以列表中自己对使用的图形API接口进行排序，设置使用优先级

下方的副选项ES3.1、ES3.1+AEP、ES3.2是三个额外的用于配置最低OpenGL ES 3.X次要版本的
注意：只有当GLES2不在列表中是，Unity才会讲额外的三个选项要求添加到Android应用清单中，在这种情况下，如果你的应用发布到GooglePlay应用商店，它将不会显示在不受支持的设备上

![image-20230305211119554](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052111597.png)

大概得原因就是因为Auto Graphics API勾选后，选了自动优先使用vulkan但是华为荣耀系列为何会报错我就不是太清楚了，有想深入了解的可以自行研究一下跟一下堆栈日志

### 解决方案一

第一种解决方案是将自动Graphics API勾选取消，然后变成下图这用再移除Vulkan，这样就解决了但是这个解决方式并不优雅，因为这样意味着我们所有的APK都使用了OpenGL3，vulkan可以简单理解为是OpenGl的升级版这样相当于是降级处理

![image-20230305211346589](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052113622.png)

### 解决方案二

Android 设备上运行时选择您想要的图形 API。

Unity提供了在UnityPlayerActivity中添加命令行参数的功能。您可以添加命令行参数。例如`-force-gles20, -force-gles30, -force-gles31, -force-gles31aep and -force-vulkan`强制使用选定的图形 API。

您可以导出项目并在Android Studio中打开它，然后修改UnityPlayerActivity.java文件。有一个例子：

```Java
String deviceName = android.os.Build.MODEL;

// Setup activity layout
@Override protected void onCreate(Bundle savedInstanceState)
{
    requestWindowFeature(Window.FEATURE_NO_TITLE);
    super.onCreate(savedInstanceState);

    if(deviceName.equals("FNE-AN00"))
    {
        this.getIntent().putExtra("unity", "-force-gles30");
    }

    mUnityPlayer = new UnityPlayer(this);
    setContentView(mUnityPlayer);
    mUnityPlayer.requestFocus();
}
```

在这里，我使用了我的华为荣耀测试机FNE-AN00为例。并使用 Vulkan 和 OpenGLES 图形 API 构建项目。我想在这个设备上选择比较老的Graphics API，所以我设置命令行参数使用OpenGLES graphics API。我们的游戏将使用 OpenGLES 3.0 在这个 FNE-AN00 设备上运行。另一方面，它在其他 Android 设备上运行时将使用 Vulkan。

当然因为华为荣耀系列的机型很多，我是比较暴力的直接将所有华为机型都使用OpenGLES 3.0，更优雅的方式就是将有问题的机型做针对设置。
