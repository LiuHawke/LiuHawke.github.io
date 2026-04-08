---
title: "Unity-移动平台相关Android"
date: 2022-09-01T15:06:06+08:00

tags:
  - Unity
  - 移动平台
  - Android

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242151718.jpg
---

## Android发布流程

### Unity2019及以上 

发布Android准备工作

通过UnityHub安装Android平台相关工具

1.Android构建支持

2.Android SDK（安卓软件开发工具包）、NDKTools（本地开发工具包）

3.OpenJDK（Java开发工具包）

注意：Unity支持Android 5.1及以上版本

### Unity2019以下版本

1.Android构建支持工具安装（Unity提供下载,安装时需要关闭Unity）

2.JDK（Java开发工具包）（网络下载） JDK 必须是版本 8

https://www.oracle.com/java/technologies/downloads/#jdk18-windows

3.NDK (本地开发工具包)（网络下载）NDK 版本必须是 Unity2018——r16b, Unity2017——r13d

注意：NDK是可选的工具，如果之后要发il2cpp的应用程序，那么NDK是必须的，如果是Mono可以不使用它

https://blog.csdn.net/momo0853/article/details/73898066

https://developer.android.google.cn/ndk/downloads/#lts-downloads

4.Android SDK（安卓软件开发工具包）（网络下载或通过AndroidStudio下载）

建议通过AndroidStudio下载

https://developer.android.com/studio

安装AndroidStudio后 通过它来安装AndroidSDK

5.Java环境变量配置

我的电脑——>系统属性——>查找——>系统环境变量——>环境变量

5-1:JAVA_HOME: 变量值为JDK在你电脑上的安装路径

安装好后可以利用%JAVA_HOME%作为JDK安装目录的统一引用路径

5-2:Path: 编辑Path属性，在原变量后追加 %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin

5-3:CLASSPATH: 变量值为 .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

### 发布前必修改的设置

在File —— Build Setting —— Player Setting中修改

1.公司名

2.游戏项目名

3.版本号

4.默认图标

5.Other Settings —— Package Name（必须修改设置）

6.Other Settings —— Minimum APILevel

## Unity发布参数

### Build Settings 参数

![image-20220901221824489](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242133874.png)

#### **Texture Compression**

**纹理压缩**
**设置发布后的纹理压缩格式**

![image-20220901221934763](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242133057.png)

**Use Player Settings: 使用在播放器设置中设置的纹理压缩格式
ETC：使用ETC格式
ETC2：使用ETC2格式
ASTC：使用ASTC格式
DXT：使用DXT格式
PVRTC：使用PVRTC格式

了解格式详细信息：https://docs.unity.cn/cn/2021.3/Manual/class-TextureImporterOverride.html#android

注意：对于安卓ETC2是常用的压缩格式，支持透明通道。但是对于老设备不支持ETC2，可以使用ETC。
我们可以统一设置为ETC2，在下方的ETC2 fallback中设置，如果不支持ETC2，使用哪种压缩方式**

#### **ETC2 fallback**

**ETC2回退**
**当Unity用于不支持ETC2的安卓设备时的RGBA纹理格式**

![image-20220901222221490](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242133278.png)

32-bit：32位
以全分辨率存储RGBA纹理，使用32位存储每个像素，质量最高，内存占用最多

16-bit：16位
以全分辨率存储RGBA纹理，使用16位存储每个像素，质量较低，纹理可能会丢失有价值的颜色信息，内存占用相对较少

32-bit, half resolution：32位，半分辨率
以半分辨率存储RGBA纹理，使用32位存储每个像素，由于是半分辨率，纹理会变得模糊，内存占用较小

注意：如果使用的不是ETC2压缩，大多数压缩格式，使用32-bit备用
取决于GPU支持什么

#### **Export Project**

**导出项目**
**勾选后，不会发布成apk，而是会将Unity项目导出为可以导入Android Studio的Gradle项目**

#### Symlink Sources

符号链接源
当勾选Exprot Project时，才能勾选此选项。
决定是否在Unity和导出的Gradle项目之间共享Java和Kotlin源文件
启用后，对于测试和迭代Java和Kotlin代码很有用，如果你重新导出Unity项目，你在导出的项目对之前的Java和Kotlin源文件的任何更改都会保留

如果你想导出后进行二次开发，建议勾选此选项

#### Build App Bundle (Google Play)

构建应用捆绑包
是否将应用程序作为Android应用捆绑包（AAB）
启用，发布出去是一个AAB（可以配合安卓进行二次开发）
禁用，发布出去是一个APK（直接就是一个可安装的应用程序）

#### **Create symbols.zip**

**创建 符号压缩文件**
**符号文件可以帮助我们调试应用程序**
**Unity会生成一个包含本机Unity库的符号文件的包**
**它包含一个表，将活动内存地址转换为我们可以使用的信息，比如方法名，我们可以利用符号包来检测调试应用程序的信息****

![image-20220901222330411](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242133225.png)

**Disabled：不生成**
**Public：Unity生成一个公共符号为程序打包**
**公共符号文件包含将函数地址解析为人类可读字符串的信息，包小于调试符号的包**
**Debugging：Unity生成一个调试符号为程序打包**
**包含完整的调试信息和符号表，可以用来解析堆栈、将本机调试器附加到应用程序调试代码**

#### **Run Device**

**运行的设备**
**指定在哪个附加设备上测试生成**
**如果你连接了新设备或在列表中没有看到连接的设备**
**点击刷新Refresh**

#### **Build to Device**

**构建到设备**
**不创建完整的构建，而是将发生修改的单个文件直接部署到设备上**
**相对来说更加快速，方便调试**

#### **Development Build**

**开发模式构建**
**是否包含脚本调试符号和性能分析器到你的项目中，如果你想要调试应用程序，请选择该选项**

#### **Autoconnect Profiler**

**自动连接分析器**
**是否自动将分析器连接到生成的应用程序**

#### **Deep Profiling**

**深度剖析**
**是否启用深度剖析在分析器中，可以让分析器能够检测应用程序中每个函数调用，从而返回更加详细的分析数据**
**注意：该选项会降低脚本的执行速度**

#### **Script Debugging**

**是否允许调试应用程序的脚本文件**

![image-20220901223243427](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242133607.png)

**Wait For Managed Debugger**
**是否等待托管调试器**
**程序在运行脚本代码之前是否等待调试器连接**

#### Compression Method

压缩方法
Unity在构建时压缩项目中数据的方式

![image-20220901223442983](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134820.png)

Default：默认使用ZIP，压缩效果略好于LZ4和LZ4HC，但是数据解压速度较慢
LZ4：使用LZ4，这时一种快速压缩格式，相对ZIP可以显著提高Unity应用程序的加载时间
LZ4HC：LZ4的高压缩版本，构建速度较慢，但是对于发布版本会产生更好的结果，相对ZIP可以显著提高应用程序加载时间

#### Max Texture Size

最大的纹理大小
用于本地开发时导入纹理的最大尺寸
减小最大纹理大小可以加快资源导入和平台切换

#### Texture Compression

纹理压缩
选择下面两个选项，可以加快资源导入和平台切换

No Override：使用默认设置
Force Fast Compressor：强制快速压缩
Force Uncompressed：强制解压缩

### **Player Settings相关**

#### Player **基础设置**

![image-20220901224319232](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134900.png)

#### **Icon 图标设置**

**设置在设备上显示的应用图标**

**Adaptive：运行在Android8.0及以上版本上的设备显示的应用图标**
**Round：运行在Android7.1及以上版本上的设备显示的应用图标**
**Legacy icons：运行在Android7.1以下版本上的设备显示的应用图标**



#### **Resolution and Presentation 分辨率和演示设置**

##### Fullscreen Mode

全屏模式

Fullscreen Window：
全屏窗口（导航栏始终处于隐藏状态）
会缩放应用程序内容，为了匹配纵横比，可能会有黑边

Windowed：
窗口模式，窗口大小取决于应用程序分辨率



##### Resizable Window

课调整大小的窗口

设置用户是否可以调整应用程序窗口的大小

![image-20220901225112530](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134540.png)

此设置可以安卓和平板电脑上的应用程序中启用多窗口功能
详情：https://developer.android.com/guide/topics/ui/multi-window#java



##### **Hide Navigation Bar**

**是否隐藏顶部显示的导航栏**



##### **Render outside safe area**

**是否应使用所有可用的屏幕控件进行渲染**
**包括留海区域**



##### Optimized frame Pacing

是否应均匀分布帧以减小帧速率的方差，启用后体验更流程



#### Resolution Scaling分辨率缩放

##### Resolution Scaling Mode

分辨率缩放模式

Disabled：禁用
不应用分辨率缩放，应用程序呈现为设备的本机屏幕分辨率

Fixed DPI：使用API应用分辨率缩放，使用此选项可优化性能和电池寿命

Target DPI：如果设备的本机屏幕DPI高于设置的值，Unity将降低应用程序的分辨率以匹配此设置
计算方式：
Min（Target DPI * Factor / Screen DPI，1）
其中Factor是在Quality质量设置中设置的 Resolution Scaling Fixed DPI Factor参数



##### Reset resolution on window resize:

当本机窗口大小更改时，是否将屏幕分辨率设置为新的本机窗口大小
如果分辨率缩放模式是Fixed DPI，则将重新计算分辨率



##### Blit Type：

光高类型
是否使用Blit将最终图像呈现在屏幕上

Always：总是让Unity渲染到屏幕外的缓冲区，然后使用Blit将缓冲区的内容复制到设备的帧缓冲区（这和大部分设备兼容，但比不用Blit慢）

Nerver：Unity将渲染到设备操作系统提供的帧缓冲区，如果失败，会有警告信息打印到日志当中。通常比使用Blit块，但是不兼容所有设备

Auto：Unity会渲染到设备操作系统提供的帧缓冲区，如果失败，会打印到日志中，并使用Blit将最终图像呈现到屏幕上



#### Supported Aspect Ratio：支持的纵横比

Orientation：屏幕取向



##### Aspect Ratio Mode：

指定应用程序支持的最大纵横比。

如果设备的纵横比大于此纵横比，Unity会将此纵横比用于应用程序，并添加黑条，使应用程序不会拉伸

Legacy Wide Screen(1.86):支持android的传统宽屏宽高比

Native Aspect Ratio:支持Android本机宽高比的宽高比

Custom:自定义纵横比

选择自定义纵横比时可以自己设置

##### **Default Orientation：**

**默认取向**

**指定应用程序使用的屏幕方向（横屏、竖屏设置）**

**Portrait：竖屏（应用程序窗口底部和屏幕底部对齐）**
**Portrait Upside Down：竖屏倒置（应用程序窗口底部和屏幕顶部对齐）**
**Landscape Right：右横屏（应用程序窗口右侧和屏幕底部对齐）**
**Landscape Left：左横屏（应用程序窗口左侧和屏幕底部对齐）**
**Auto Rotation：自动旋转，可以旋转到你允许的任意方向，在下方勾选即可**



##### Use 32-bit Display Buffer：

显示缓冲区是否保存32位颜色值而不是16位颜色值。

如果你在后期处理效果中看到了条带效果或者需要Alpha值，请启用此设置，某些后期处理效果需要开其它，因为他们以显示缓冲区相同的格式创建渲染纹理



##### Disable Depth and Stencil：

是否禁用深度和模板缓冲区



##### Render Over Native UI：

在本机UI上呈现
设置是否在Android或者IOS的本机UI上进行渲染
如果要该设置生效，需要把相机的清楚标志设置为使用Alpha值低于1的纯色



##### Show Loading Indicator：

显示加载指示器
指定加载指示器是否显示以及如何显示

Don't Show:不显示加载指示条
Large:显示大的加载指示条
Inversed Large:显示大的加载指示条，并带有翻转颜色
Small:显示小的加载指示条
Inversed Small:显示小的加载指示条，并带有翻转颜色

#### Splash Image:

启动图像设置

##### Virtual Reality Splash Image:

虚拟现实的初始屏幕设置

##### Show Splash Screen：

是否显示启动图像（收费版本才能修改，免费版本不能改）

##### Preview：

预览

##### Splash Style：

启动风格

Dark on Light: 暗到亮
Light on Dark: 亮到暗

##### Animation:

启动图像动画效果

Static：静态不动的
Dolly：由小变大
Custom：自定义

**Draw Mode：绘制模式**

**Unity Logo Below：Unity Logo显示在前**
**All Sequential：所有按顺序**

**Logos：可以自己添加Logo图片按顺序显示**

##### BackGround：背景相关

Overlay Opacity:
覆盖不透明度

Background Color:
背景颜色

Blur Background Image:
模糊背景图像

Background Image:
背景图像

Alternate Portrait Image:
备选图像

Static Splash Image:
静态启动图像

Scaling:
缩放比例

Center：
居中（仅缩小）：除非图像太大，否则以原始大小绘制图像，Unity会缩小图像

Scale to fit：
缩放以适应（填充），缩放图像，使较长尺寸完全适合屏幕大小，以黑色填充两侧空白区域

Scale to fit：
缩放以适应（裁剪），缩放图像，使较短的尺寸完全适合屏幕大小，将图像裁剪为较长的维度

### OtherSetting相关

#### Rendering渲染设置

##### **Unity程序员必了解的图形程序接口**

###### **OpenGL（Open Graphics Library）**

中文翻译过来是**开放图形库**，它定义了一个**跨平台、跨语言**的编程接口规格的专业图形程序接口，可以用于3D、2D图形渲染，是一个功能强大、调用方便的底层图形库。由于它跨平台、跨语言、出现时间早，因此它的应用极其广泛！



###### **OpenGL ES（OpenGL for Embedded Systems）**

中文翻译过来是**用于嵌入式系统的开放图形库**，它是**OpenGL的子级**，**主要针对手机、游戏主机**等嵌入式设备而设计，免授权费、跨平台、功能完善。

GLES2.0、GLES3.0 指的就是OpenGL ES这套标准，他们也是Android和IOS手机上常用的图形处理标准。

**Unity在移动平台进行图形渲染处理时，就包含了OpenGL ES方案**



###### **Vulkan**

“下一代”开放的图形显示API，是与DX12能够匹敌的GPU API标准。它有一套最新的图形加速API接口，目标是提供更灵活和丰富的底层操作接口，以替代OpenGL 和 OpenGL ES接口，**可****以把Vulkan看做是OpenGL的升级版**，目前**新版本的Unity支持使用Vulkan方案**。



###### **Directx（Direct eXtension）**

中文翻译过来是直接拓展，简称DX。它是由**微软**公司创建的多媒体编程接口。它**不跨平台**，只针对微软的相关产品，被广泛使用于Windows操作系统、xBox游戏主机的图形应用程序开发中。

其中的D3D算是DX一部分，是对标OpenGL的图形程序接口



###### **WebGL（Web Graphics Library）**

中文翻译过来是网页图形库，它是**针对Web端（\**网页\**）的3D绘图协议**，这个标准允许把JavaScript和OpenGL ES 2.0结合在一起，网页开发人员可以借助系统显卡在浏览器里流畅的展示3D场景和模型，可以在网页里进行3D图形开发。

 

###### **Metal**

中文翻译过来是金属，它是**苹果公司**为游戏开发者提供的图形技术，该技术能够为3D图像提高10倍渲染性能，但是它不支持跨平台，主要针对IOS、macOS苹果自家的操作系统，只有苹果手机、电脑能够使用。

**Windows电脑：DX、OpenGL、Vulkan**

**苹果电脑：Metal、OpenGL、Vulkan**

**安卓手机：OpenGL ES、Vulkan**

**苹果手机：OpenGL ES、Vulkan、Metal**

**网站网页：Web GL**

如果不通过游戏引擎，我们想要在这些平台上开发游戏，那么就必须要针对不同平台学习对应的图形程序接口相关的知识。你会发现**OpenGL**的身影在各主流平台中都占有一席之地，由于它**出现早、跨平台、跨语言**，所以也是为什么在学习计算机图形学时，OpenGL是必学的内容。

我们现在使用游戏引擎开发游戏，在绝大多数情况下，不需要直接和图形程序接口打交道，但是**我们在Unity当中发布应用程序时**，**经常会看到和他们相关的一些设置**，必须要了解了他们的基本概念，才能清楚我们在设置什么。

这些**图形程序接口**还有不同的版本，比如DX10、DX11、DX12，比如OpenGL ES 2.0、OpenGL ES 3.0。**不同的设备、不同的操作系统他们支持的版本也是不同的**。比如我们进行手机游戏开发时，我们**可以去查询主流移动设备支持的图形程序接口的版本，来决定在发布时，对于图形程序接口版本的兼容选择****。这样才能保证我们发布的应用程序能够支持更多的移动设备。



##### **Color Space：色彩空间**

**可以在此切换Unity中用于渲染的色彩空间**
**Gamman（伽马）和 Linear（线性）**

**区别：**
**纹理倾向于保存在伽马颜色空间中，而着色器期望使用线性颜色空间**
**因此，在着色器中对纹理进行采样时，基于伽马的值会导致不准确的结果**
**我们可以选择线性色彩空间，然后通过纹理中的 sRGB选项决定是用伽马还是线性，勾选sRGB是伽马，取消勾选是线性**

**如何选择：**
**线性渲染的效果相对较好一些**
**但并不是所有平台都支持线性渲染**
**支持的平台为：**
**Android、IOS、WebGL、Windows、Mac OS X 、Linux**
**当设备不支持线性渲染时，不会退回到伽马控件，而是自动退出**

**在 Android 上，线性渲染至少需要 OpenGL ES 3.0 图形 API 和 Android 4.3**
**在 iOS 上，线性渲染需要 Metal 图形 API**
**在 WebGL 上，线性渲染至少需要 WebGL 2.0 图形 API**

**确定你的应用程序运行的主流设备是否支持，如果支持则选择线性，不支持选择伽马**

![image-20220901231129340](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134818.png)

https://docs.unity.cn/cn/2021.3/Manual/LinearLighting.html



##### **什么是Vulkan？**

**是一个跨平台的2D和3D绘图应用程序接口**
**在2015年游戏开发者大会（GDC）上发表**
**是OpenGL的下一代版本，旨在提供更低的CPU开销与更直接的GPU控制**



##### **Auto Graphics API:自动图形接口（建议选择自动）**

**勾选：Unity会自动尝试使用对应图形API接口进行图形处理**
**默认使用Vulkan，如果设备不支持Vulkan，Unity会退到GLES3.2、GLES3.1或GLES3.0**

**不勾选：可以列表中自己对使用的图形API接口进行排序，设置使用优先级**

**下方的副选项ES3.1、ES3.1+AEP、ES3.2是三个额外的用于配置最低OpenGL ES 3.X次要版本的**
**注意：只有当GLES2不在列表中是，Unity才会讲额外的三个选项要求添加到Android应用清单中，在这种情况下，如果你的应用发布到GooglePlay应用商店，它将不会显示在不受支持的设备上**



##### Color Gamut: 色域

可以添加或删除 Android 平台用于渲染的色域
色域定义了可用于给定设备（如监视器或屏幕）的可能颜色范围。sRGB 色域是默认（也是必需的）色域。当定位具有宽色域显示的设备时，请使用 DisplayP3 来利用完整的显示功能。

一般的手机使用sRGB即可，如果是PC主机游戏，可以考虑添加DisplayP3



##### **Multithreaded Rendering：多线程渲染**

**启用此选项可将图形 API 调用从 Unity 的主线程移动到单独的工作线程。这有助于提高主线程上 CPU 使用率较高的应用程序的性能。**



##### **Static Batching：静态批处理**

**会将静态物体合并处理DC，提升性能**



##### **Dynamic Batching：动态批处理**

**可以动态的合并DC，提高性能**
**当可编程渲染管线（SPR）处于活动状态时，也就是启用了URP或者HDRP时，动态批处理不起作用**

**在使用FairyGUI时，就需要开启它，FairyGUI的DC将得到优化**



##### **Compute Skinning：计算蒙皮**

**启用此选项可启用 DX11/ES3 GPU 计算蒙皮，从而释放 CPU 资源**

**图形接口的版本要求较高，PC上DX11、移动设备ES3**



##### Graphics Jobs(Experimental)：图形作业

##### 启用后，Unity将渲染循环的图形任务移到其它CPU内核上运行的工作线程，可以减少在主线程上花费的时间



##### **Texture compression format：纹理压缩格式**

**可以在ASTC、ETC2、ETC之间进行选择**



##### **Normal Map Encoding：法线地图编码**

**选择 XYZ 或 DXT5nm 样式以设置法线映射编码。这会影响用于法线贴图的编码方案和压缩格式。DXT5nm 样式的法线贴图质量更高，但在着色器中解码成本更高**



##### **Lightmap Encoding：光照贴图编码**

**选择“正常质量”或“高质量”以设置光照贴图编码。此设置会影响光照贴图的编码方案和压缩格式**



##### **Lightmap Streaming：光照贴图流**

**是否对光照贴图使用 Mipmap 流式处理**



##### Frame Timing Stats：帧时序统计

启用此属性可收集 CPU 和 GPU 帧时间统计信息

需要和摄像机上的动态分辨率设置结合使用（Camera组件下的Allow Dynamic Resolution选项），以确定应用程序是受 CPU 还是 GPU 限制。



##### OpenGL:

Profiler GPU Recorders：是否启用OpenGL的探查GPU记录器

在OpenGL上，探查GPU记录器 会 禁用GPU探查器



##### Virtual Texturing：虚拟纹理

可在场景中具有很多高分辨率纹理时减少 GPU 内存使用量和纹理加载时间。它将纹理拆分为瓦片，然后在需要时将这些瓦片逐步上传到 GPU 内存中。

注意：虚拟纹理和安卓设备不兼容



##### Shader Precision model：着色器精度模型

控制着色器中使用的采样器的默认精度



##### 360 Stereo Capture：360 度立体捕捉

Unity 是否可以捕获立体 360 度全景图像和视频

注意：360 度立体捕捉与 Android 不兼容。



#### Vulkan Setting

身份证明设置

注意：安卓平台时，不要修改这些选项



##### SRGB Write Mode: SRGB 写入模式

允许呈现器在运行时切换 sRGB 写入模式，如果要暂时关闭线性到 sRGB 写入颜色转换，可以启用该选项

注意：建议不要为移动设备启用该选项
它会增加移动设备GPU的负担，产生负面影响



##### Number of swapchain buffers: 交换链缓冲区数量

设为2位双缓冲，设为3为三重缓冲可以和Vulkan渲染器一起使用
该设置可以帮助解决移动平台上的延迟问题

注意：一般情况下我们不要修改此选项，保持为3，不要在安卓设备上使用双缓冲，会产生负面影响



##### Acquire swapchain image late as possible：尽可能晚的获取交换链图像

启用后，Vulkan会延迟获取后缓冲器，直到它将帧渲染为屏幕外图像。Vulkan 使用暂存映像来实现此目的。启用此设置会导致在显示反向缓冲器时产生额外的光圈。此设置与双缓冲相结合，可以提高性能。但是，它也可能导致性能问题，因为额外的 blit 会占用带宽。

注意：安卓设备上我们不要修改该选项



##### Recycle command buffers：回收命令缓冲区

Unity 执行命令缓冲区后是回收还是释放命令缓冲区



##### Apply display rotation during rendering：

在渲染期间应用显示旋转启用此选项可在显示的本机方向上执行所有渲染。这在许多设备上具有性能优势，虽然如此，但是使用它会带来一些限制，所以还是建议大家不要修改此选项



#### Identification：身份证明

注意：首次发布时，一定要确保包名的正确性



##### Override Default Package Name：是否覆盖默认包名称

如果你的公司名和游戏名 不是英文，请勾选它自行设置包名



##### Package Name：软件包名

相当于应用程序的ID，用于在设备和安卓应用商店中的唯一标识。
格式：com.公司名.产品名
只能包含字母数字和下划线，每段都必须以字母开头



##### Version：版本号

显示给用户看的
用于标识应用程序包的迭代
格式：数字.数字.数字。。。
比如：1.0、4.3.2、1.2.1等



##### Bundle Version Code：内部版本号

确定一个版本是否比另一个版本新，数字越大表示版本越新，不会显示给用户看
该值为整数，比如每次发布新版本时可以让数字加一



##### Minimum API Level：最低API级别

决定应用程序运行需要的最低的API级别，如果用户手机的SDK版本低于该设置，Android系统会组织用户安装该应用程序



##### Target API Level：目标API级别

用于指定应用的目标API级别
主要用于通知Android系统，我们已经针对目标版本进行了测试，并且系统不应该通过启用任何兼容性行为，以保持你的应用与目标版本的向前兼容性。应用程序一样可以在较低版本上运行（取决于Minimum API Level）



#### Configuration：配置

![image-20220901232738331](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134547.png)

##### **Scripting Backend：脚本后端**

**决定Unity如何在项目中编译和执行C#代码**

**Mono：将C#编译为.Net公共中间语言CIL，并使用公共语言运行时执行中间语言**

**IL2CPP：将C#编译为CIL，再将CIL转换为C++，再将C++编译为本机代码，**

**Mono和IL2CPP的具体区别在 Unity进阶之C#知识补充当中有讲解**



##### **Api Compatibility Level：API兼容性级别**

**决定可以在项目中使用的.Net API。该设置会影响代码和第三方库的兼容性**

**.Net Standard 2.1：和.Net Standard 2.1兼容，生成较小的版本，具有完整的跨平台支持**

**.Net Framework：和.Net Framework兼容（包含.Net Standard 2.1中的所有的内容），包含更多的API支持，但是会生成更大的包。**

**如果在使用C#的一些高级功能时遇到报错或无法使用，可以尝试切换到.Net Framework以便支持这些高级功能的使用，如果有替代方案，建议使用替代方案，尽量使用.Net Standard 2.1可以让包体更小**



##### **C++ Compiler Configuration：C++编译器配置**

**只有将Scripting Backend设置为IL2CPP，才能使用该选项**

**Debug（调试模式）: 将关闭所有优化，让代码生成速度更快，但运行速度较慢，可以在这种模式下进行调试**

**Release（发布模式）：会对代码进行优化，编译后的代码运行会更快，二进制文件大小更小，但编译时间更长**

**Master（大师模式）：可实现所有可能的优化，从而压缩每一点可能的优化，发布时间比发布模式还要长，如果接受较长的发布时间，建议在发布最终版本时使用Master模式**



##### **Use incremental GC：使用增量GC**

**启用后可以使用增量垃圾回收器，它会将GC分散到多个帧上执行，可以降低在一帧中进行GC造成的卡顿。**



##### Assembly Version Validation：程序集版本验证

Mono是否验证强名称程序集中的类型

强名称程序集，是对程序集进行强命名，为程序集创建唯一标识，可以防止程序集冲突



##### **Mute Other Audio Sources：将其它音频源静音**

**如果希望在运行Unity应用程序时停止在后台运行的应用程序中的音频，可以启用该选项，否则后台音频将和Unity中的音频一起播放**



##### Target Architectures：目标体系结构

允许应用程序运行的CPU
64位环境中运行安卓应用具有性能优势
64位应用可以处理超过4GB的内存空间

Mono模式只能选择ARMv7
IL2CPP模式可以选择更多的模式

ARM就是一种微处理器硬件
Chrome OS是一款Google开发的基于PC的操作系统

ARMv7 32位的ARM

ARM64 64位的ARM

x86(Chrome OS) 32位

x86-64(Chrome OS) 64位



##### Split APKs by target architecture：按目标架构拆分APK（实验性功能）

启用后，可以为目标体系结构中选择的每个CPU架构创建单独的APK，这样在Google Play中为用户提供下载时，它会根据设备的情况下载对应版本的APK，可以让apk更小，用户下载更小的包。
但是主要是针对Google Play，因此面向国内的产品几乎不会使用。



##### Target Devices：目标设备

执行允许运行APK的目标设备

All Devices（所有设备）：允许apk在所有Android和Chrome OS设备上运行
Phones，Tablets，and TV Devices Only（手机、平板、电视设备）：允许apk在Android手机和平板电脑、电视上运行，但是不能在Chrome OS设备上运行
Chrome OS Devices Only（Chrome OS设备）：允许apk在Chrome OS设备上运行，但是不能在Android手机、平板、电视上运行



##### **Install Location：安装位置**

**指定设备上的应用程序安装位置**

**Automatic：自动让操作系统决定，用户可以自己移动安装位置**

**Prefer External：首选外部安装，如果可以，将应用程序安装到外部存储中（SD卡），如果不能，应用程序安装到手机存储空间中**

**Force Internal：强制内部，强制将应用程序安装到手机存储空间中，用户无法将应用程序移动到外部存储中安装**



##### **Internet Access：互联网接入**

**选择是否始终将网络权限添加到Android清单（即使你没有使用任何网路API）**

**Auto：仅当使用了网络API时才会添加互联网访问权限**
**Require：使用添加互联网访问权限**



##### **Write Permission：写入权限**

**是否启用对外部存储（SD卡）的写入访问权限，并向Android应用清单添加相应的权限**

**Internal（内部）：仅授予对内部存储的写入权限**
**External（外置SD卡）：启用对外部存储的写入权限**



##### Filter Touches When Obscured：遮挡时过滤触摸

启用后可以丢弃在另一个可见窗口覆盖Unity应用程序时收到的触摸（触屏事件），可以放置窃听劫持



##### Sustained Performance Mode：持续性能模式

启用此选项可在较长的时间段内设置可预测且一致的设备性能级别，而无需进行热限制。启用此设置时，整体性能可能会降低



##### Low Accuracy Location：低精度定位

启用后可改为低精度值与Android位置API配合使用



##### Chrome OS Input Emulation：

Chrome OS 操作系统的默认行为是将鼠标和触摸板输入事件转为触屏输入事件。
取消可禁用这个默认行为



##### Android TV Compatibility：安卓电视兼容性

启用后应用程序标记为兼容Android TV

启用后出现
Android Game：启用后可将输出的apk标记为游戏而不是常规应用
Android Gamepad Support Level：安卓游戏输入板支持等级
可以选择玩游戏时支持的输入方式



##### Warn about App Bundle size：警告应用程序包的大小

只有在Build Settings中勾选了Build App Bundle（Google Play）才有用
当应用程序包大小超过这个阈值时会收到警告



##### **Active Input Handling：活动输入处理**

**选择要如何处理来自用户的输入**

**Input Manager：老的输入系统，Input相关**
**Input System Package：新输入系统**
**Both：同时使用**



#### Script Compilation：脚本编译

##### Scripting Define Symbols：脚本定义符号

可以在此设置自定义编译标志。

再使用一些第三方内容时有时会在此处添加脚本符号
比如：Lua热更相关、FairyGUI相关

###### Unity有内置的脚本符号

这些符号能够帮助我们有选择的在编译中包含或排除部分代码

这些脚本符号可以配合多路测试 #if 进行使用
\#if 脚本符号
	代码逻辑
\#elif 脚本符号
	代码逻辑
\#else
	代码逻辑
\#endif

###### 平台脚本符号

![image-20220901233443206](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134540.png)

###### 编辑器版本脚本符号

![image-20220901233531611](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134858.png)

###### 其它脚本符号

![image-20220901233603453](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242134723.png)



##### Additional Compiler Arguments：其它编译器参数

向此列表添加条目以将其他参数传递给 Roslyn 编译器。对每个附加参数使用一个新条目。
要创建新条目，请按“+”按钮。要删除条目，请按“-”按钮。
添加完所有所需参数后，单击“应用”按钮以在将来的编译中包括其他参数。“还原”按钮将此列表重置为最近应用的状态。



##### Suppress Common Warnings：禁止显示常见警告

禁用此设置可显示C#警告 
CS0169：从不使用私有字段，声明了私有变量，但是从没有使用
CS0649：编译器检测到从未分配值的未初始化的私有或内部字段声明



##### **Allow 'unsafe' Code：允许使用“不安全”代码**

**启用对在预定义程序集中编译“unsafe”C# 代码的支持**

**unsafe 关键词的基本概念**

默认情况下，C#是不支持 指针 的，unsafe 关键词用于在C#表示**不安全的上下文**，如果**想要在C#中进行任何和指针相关的操作，就必须配合unsafe关键词使用。**

在公共语言运行时（CLR）中，不安全代码是指无法验证的代码。

C# 中的不安全代码不一定是危险的，只是 CLR 无法验证该代码的安全性。因此，CLR 将仅执行完全信任的程序集中的不安全代码。

**如果你使用不安全代码，你****应该负责确保代码不会引发安全风险或指针错误**

**unsafe 关键词的基本使用**

如果想要在Unity中正常使用C#中的unsafe关键词进行程序编写，我们必须在Unity的 Player Settings 中的 Other Settings 中将 Allow unsafe Code 选项勾选上

勾选该选项后，我们就可以在Unity中使用unsafe关键词了。

**注意：unsafe 的使用虽然能让我们在C#中使用指针，但是会存在安全风险和稳定性风险，如果没有特殊需求，请尽量避免在C#中使用 unsafe。**



##### Use Deterministic Compilation：使用确定性编译

启用此设置后，编译的程序集在每次编译时都是完全相同的。
禁用此设置可防止使用 -确定性 C# 标志进行编译

确定性编译可用于确定二进制文件是否从受信任的源编译



###### Enable Roslyn Analyzers：启用罗斯林分析器

禁用此设置可编译用户编写的脚本，而无需项目中可能存在的 Roslyn 分析器 DLL（ Roslyn 就是微软的.Net开源编译器，编译器支持 C# 编译，并提供丰富的代码分析 API。）



#### Optimization：优化

![image-20220901234125528](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242135478.png)

##### Prebake Collision Meshes：预烘焙碰撞到网格

启用该选项可以在构建时将碰撞数据添加到网格



##### Keep Loaded Shaders Alive：保持加载的着色器的活动状态

启动后，不能卸载着色器

着色器加载时会造成性能开销，可能会出现卡顿现象，不允许卸载着色器可以避免卸载后重复加载



#### **Preloaded Assets：预装资源**

**设置启动时加载的资源数组**
**将想要预加载的内容拖入框中**



##### **Strip Engine Code：剥离引擎代码**

**选择IL2CPP模式才会出现的字段**
**如果你希望在IL2CPP模式，能够删除Unity引擎功能中没有使用的代码，可以启用该选项，可以有效的减小包体大小**



##### **Managed Stripping Level：管理剥离水平**

**选择Unity如何剥离未使用的C#代码**
**在发布应用程序时，Unity会自动去除掉没有使用的代码，剥离代码可以使生成的执行文件减小，但是有可能会意外删除需要使用的代码**

**在Unity进阶之C#知识补充中讲解IL2CPP相关内容时讲解过**

**Disabled：不剥离，只有在Mono模式下才能选择**

**Minimal：最小剥离，Unity只会搜索Unity引擎未使用的.Net类库，不会删除任何用户编写的代码，该设置基本不会出现意外剥离，在使用IL2CPP模式后，该模式是默认模式**

**Low：低级剥离，处理Unity相关，玩家自己编写的代码也会被剥离，会尽量减小意外剥离发生**

**Medium：中级剥离，比起Low更多一些剥离**

**High：高级剥离，优先考虑打包大小，会最大限度剥离代码。可以采配合link.xml来手动拒绝剥离，或使用[Preserve]特性（在不希望被剥离的函数前加该特性）**



##### Enable Internal Profiler：启用内部探查器，已经弃用的功能

用此选项以从Android SDK的设备中获取profiler数据adblogcat测试项目时输出。这仅在开发版本中可用。



#### **Vertex Compression：顶点压缩**

**选择要设置的通道，以便在顶点压缩方法下压缩网格**
**通常，顶点压缩用于减少内存中网格数据的大小，减小文件大小，提高CPU性能。**



##### **Optimize Mesh Data：优化网格数据**

**启用后，构建时会从使用的网格中剥离未使用的顶点属性**
**它可以减少网格中的数量，也可以减小包体大小，可以减小加载时间和运行时的内存使用**
**但是，如果启用了该设置，运行时就不要更改材质或着色器相关设置**

**也就是说，如果你游戏会在运行时转换一些对象的着色器，就不能开启该选项。**

**比如游戏中某一个对象，如果网格使用简单漫射着色器，那么 Unity 将删除 切线矢量，因为并不需要它们。如果您想要在运行时切换到网格上的凹凸贴图着色器 ，那么您将不会得到正确的切线数据，因为此数据已被删除**



##### **Texture MipMap Stripping：贴图纹理剥离**

**启用后会启用纹理贴图剥离，在构建时会从纹理中剥离没有使用的纹理贴图。** 
**会根据你发布平台的质量设置进行比较来确定哪些贴图用不到。**
**mipmap生成的多分辨率图也会被剥离**



##### **Stack Trace：堆栈跟踪**

**选择在特定的上下文中允许的日志记录类型**
**可以选择日志记录的方式**
**None：不记录**
**ScriptOnly：只在运行脚本时记录信息**
**Full：一直记录**

Error：错误信息
Assert：断言（用于检测非法情况的）信息
Warning：警告信息
Log：打印日志信息
Exception：异常信息



##### Legacy：以前的内容

Clamp BlendShapes（Deprecated）：启用 骨骼蒙皮动画中钳制混合形状权重的范围



### Publishing Settings

#### **前言**



##### **Android 中的签名是什么？**

Android要求所有已安装的应用程序都使用数字证书做数字签名，数字证书的私钥由应用开发者持有，
Android使用证书作为标示应用程序作者的一种方式，并在应用程序之间建立信任的关系。 证书并不用来控制用户能否安装哪个应用。证书不需要由证书认证中心签名；完全可以使用自制签名证书。

没有正确签名的应用，Android系统不会安装或运行。此规则适用于在任何地方运行的Android系统，不管是在模拟器还是真实设备上。因为这个原因。在真机或模拟器上运行或者调试应用前，必须为其设置好签名。

**为什么要使用签名？**

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242135676.png)

上图是我们在Unity中发布Android应用程序时，设置的包名

开发Android的人这么多，完全有可能把包名命名成同样的名字，这个时候该如何区分？

这时就需要签名来区分了，由于开发商可能通过使用相同的Package Name（包名）来混淆替换已经安装的程序，签名可以保证相同名字，但是签名不同的包不被替换。

举例：

比如应用程序A 和 应用程序B 的包名是相同的，如果没有签名的存在，在同一Android设备上重复安装A和B，就会导致他们之间的彼此覆盖，不能同时拥有，为了避免这种情况出现，就有了签名机制。

**安卓密钥库和密钥，就是用来进行Android签名的。**



##### **Android 应用程序清单是什么？**

Android应用程序工程中，有一个叫做 AndroidManifest.xml 的xml文件，该文件在之后的开发中我们会经常使用和编辑，该配置文件主要用于声明应用程序的组件，并且还有以下的一些重要作用：

1. 确定应用程序要求的用户权限，比如：网络访问、通讯录访问、信息读取等权限

2. 声明应用程序要求的最低API Level

3. 声明应用程序将要使用的或要求的硬件和软件特性，比如：摄像头访问、蓝牙服务、多点触碰等
4. 等等

也就是说，如果我们想要在安卓游戏当中使用一些系统功能或硬件访问权限，都需要在该文件当中进行设置。

**Unity中如果引入Android第三方SDK，或者需要和Android进行交互，经常会对该文件进行编辑。如果我们没有这些需求，可以忽略它。**



##### **Gradle 是什么？**

Gradle 是一个自动化构建开源工具，主要面向Java应用为主，也支持其它语言，比如C++、Kotlin、Swift，未来还会支持更多的语言。

它是一个基于JVM（Java虚拟机）的构建工具，是一款通用灵活的构建工具，也可以用于Android 项目的构建工作，它可以让安卓项目变得更加简洁。

**在Unity中简单理解Gradle，它就是用于帮助我们打包出安卓应用程序 .apk 的一个工具，在Android Studio中也使用Gradle进行应用程序打包。**



##### **ProGuard 和 R8 是什么？**

**代码混淆**

代码混淆(Obfuscated code)，是将计算机程序的代码，转换成一种功能上等价，但是难于阅读和理解的形式的行为。代码混淆主要用于程序源代码，也可以用于程序编译而成的中间代码。执行代码混淆的程序被称作代码混淆器。已经存在许多种功能各异的代码混淆器。

将代码中的各种元素，如变量，函数，类的名字改写成无意义的名字。比如改写成单个字母，或是简短的无意义字母组合，甚至改写成“__”这样的符号，使得阅读的人无法根据名字猜测其用途。重写代码中的部分逻辑，将其变成功能上等价，但是更难理解的形式。比如将for循环改写成while循环，将循环改写成递归，精简中间变量，等等。打乱代码的格式。比如删除空格，将多行代码挤到一行中，或者将一行代码断成多行等等。

**代码混淆的主要目的是提升源代码的安全性**，别人反编译你的应用程序代码后，增加他们阅读分析逻辑的难度。

**ProGuard 和 R8 就是两种不同的代码混淆器。**



**ProGuard**

ProGuard是一个压缩、优化和混淆[J](https://baike.baidu.com/item/Java字节码/13025120)ava字节码（Java源代码通常被编译为字节码）文件的免费的工具，它可以删除无用的类、字段、方法和属性。可以删除没用的注释，最大限度地优化字节码文件。它还可以使用简短的无意义的名称来重命名已经存在的类、字段、方法和属性。常常在Android开发用于混淆最终的项目，增加项目被反编译的难度。

Unity发布安卓应用程序时，默认使用的就是ProGuard来进行处理最终的代码。



**R8**

R8是相对ProGuard较新的Android混淆编译器，它可以尽可能的减小应用的大小，早期的Android Studio版本中，混淆编译器使用的是ProGuard执行编译时的代码优化，如果我们使用Android Gradle 3.4.0或更高版本构建项目时，不再使用ProGuard进行代码优化，而是采用R8编译器协同工作。

Android Sutdio 3.3版本开始，就使用R8作为代码压缩器来对代码进行混淆、压缩、优化了。

R8 相对 ProGuard来说，它可以更快地缩减代码，同时改善输出的大小。

Unity发布安卓应用程序时，我们可以选择使用R8混淆编译器进行发布处理。



大概了解了这些基本概念后，才能知道我们是在设置什么内容。

#### **Keystore Manager：密钥管理器**

![image-20220903120410725](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242135085.png)

**可以通过密钥管理器创建、配置和加载密钥和密钥库**
**我们可以在Keystore Manager窗口**
**或**
**Publishing Settings 发布设置中**
**加载现有密钥库和密钥**

![image-20220903120739100](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242135418.png)

Create New：创建新的密钥库

Anywhere：任何地方，会打开项目文件夹根目录下的文件资源管理器，这是Unity存储密钥库的默认位置

In Dedicated Location：专用位置，将密钥库文件保存到计算机中的另一个目录中

注意：为了协同开发方便，建议大家就把密钥库保存在项目文件夹下，避免其它同事无法访问密钥库

Select Existing：选择现有密钥库
可以指定加载现有密钥库



Password：密钥库的密码
Confirm password：创建密钥库时 需要重复一次密码

Existing Keys：现有密钥
界面的这一部分包含当前密钥库包含的密钥的只读列表

![image-20220903120936402](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242135636.png)

#### **Project Keystore：项目密钥库**

**用于保存签名密钥来实现应用程序安全性的容器**
**如果想要加载和使用现有密钥库，我们需要：**

1. **启动 Custom Keystore**
2. **在 Select 下拉列表中选择Browse，从文件系统中选择密钥库**
3. **输入密钥库密码**

Custom Keystore：启用后，可以指定加载和使用现有密钥库

Select：启用Custom Keystore后，该按钮可以用来选择要使用的密钥库

Path：你选择的密钥库所在路径

Password：输入密钥库密码以加载所选的密钥库



#### **Project Key：项目密钥**

**加载密钥库后，Unity会加载该密钥库中的所有密钥**
**我们可以在项目密钥Project Key中设置从该密钥库中选择哪一个密钥用来作为该项目的活动密钥**

Alias：选择用于打开的项目的密钥

Password：密钥密码



#### **Build：构建相关**

**默认情况下，Unity使用Unity安装提供的清单文件、Gradle模板和文件构建应用程序**
**如果想要自定义这些内容，你需要：**

1. **启用Build下方的这些复选框，文件会出现到复选框的下方**
2. **打开新文件进行更改**
3. **保存修改的文件，Unity下次构建应用程序时会自动使用的文件**



##### **Custom Main Manifest：自定义主清单文件**

**该清单文件是一个xml文件**
**我们可以在此决定一些权限设置（比如：网络、定位、拍照等权限配置），还可以设置是否启用一些安卓功能等等**



##### Custom Launcher Manifest：自定义启动器清单

该清单文件是一个xml文件
我们可以在此决定一些应用程序启动之前的外观和行为。（比如：图标、名称、安装位置等等）



##### Custom Main Gradle Template：自定义主Gradle构建模板

Gradle 是一个构建系统，可自动执行许多构建过程并防止许多常见的构建错误。Unity将Gradle用于所有Android版本。您可以在Unity中构建输出包（.apk，.aab），也可以从Unity导出Gradle项目，然后使用Android Studio等外部工具构建它

是一个gradle文件，包含有关如何将Android应用程序构建为库的信息



##### Custom Launcher Gradle Template：自定义启动器Gradle构建模板

是一个gradle文件，包含有关如何构建Android应用程序的说明



##### Custom Base Gradle Template：自定义基础Gradle构建模板

是一个gradle文件，包含在所有其它模板和Gradle项目之间的共享配置



##### Custom Gradle Properties Template：自定义属性Gradle构建模板

属性文件，包含Gradle生成环境的配置设置
比如 
JVM（Java虚拟机）内存配置
允许Gradle使用多个JVM构建的属性
用于选择进行缩小的工具的属性
构建应用程序包时不压缩本机库的属性等等



##### Custom Proguard File：自定义Proguard文件

是一个txt文件，包含缩小过程的配置设置
如果缩小删除了一些应该保留的Java代码，你可以添加一条规则来将这些代码保留在此文件中



#### **Minify：代码混淆和缩小相关**

**是一种缩减、混淆和优化应用程序代码的过程**
**它可以减小代码大小并使代码更难以反编译**
**使用 Minify 设置可定义 Unity 应何时以及如何将缩小应用于构建**

**使用该功能会加长发布时间，并且还会让调试变得复杂，所以一般在最终发布时才会使用，平时发布测试应用时不需要开启。**



Use R8：默认情况下，Unity 使用 Proguard 进行缩小。启用此复选框可改为使用 R8。

**Release：如果希望 Unity 在发布构建中缩小应用程序的代码，请启用此复选框。**

Debug：如果希望 Unity 在调试构建中缩小应用程序的代码，请启用此复选框。



#### **Split Application Binary：拆分应用程序二进制文件**

**启用 Split Application Binary 选项可将输出包拆分为主包 (APK) 和扩展包 (OBB) 包。如果要发布大于 100 MB 的应用程序，则 Google Play 应用商店需要此功能。**



## Unity打包Android



### Unity导出APk

没什么可说的直接导出就行了



### Unity导出Android Studio能够打开的工程

1.设置导出基本信息：公司名、游戏名、图标、包名等关键信息

2.在File——>Build Settings中，勾选 Export Project 选项

3.点击Export 导出按钮

#### 在Android Studio中打开Unity导出的工程

1.打开Android Studio

2.新选择 打开工程

3.选择 之前导出的对应文件夹

4.弹出提示时，使用Android Studio中的 Android SDK，可以避免打包失败

5.等待工程加载结束

6.更新Gradle版本，保证之后打包成功

#### 使用Android Studio生成应用程序apk

1.在 工具栏——>Build——>Build Bundle...——>Build APK

 构建一次工程，保证构建成功，根据报错解决问题

 常见问题：

 1.The option 'android.enableR8' is deprecated 由于新版本Gradle默认使用R8混淆

   所以不需要这个选项设置了

   在Project选项中 找到 gradle.properties 删除 android.enableR8

 2.SDK Tools 版本不匹配

   更新SDK使用的路径，使用Android Studio自己的路径，或者更新SDK



2.在 工具栏——>Build——>Build Bundle...——>Generate Signed Bundle

 发布APK，可以设置密钥库相关内容后进行打包即可

3.测试生成的apk文件



## Android调试相关

### Unity进行安卓真机调试

#### 调试指的是什么？

安卓手机的真机调试

由于运行环境的改变（开发时在 Windows 或 MacOS，使用时在Android或IOS）

我们很多时候需要在真机上进行项目的 性能分析 和 bug处理（断点调试修复bug）



#### 准备测试用安卓手机

1.安卓手机需要打开开发者模式可以根据自己的手机品牌自行搜索开启方式

 一般的操作方式：设置——>关于手机——>版本号（点击n次版本号）便可开启

2.完成第一步后，在手机的开发者选项中 开启 USB调试功能一般开发者选项会在：设置——>系统——>开发者选项中找到USB调试功能 开启它

3.将手机通过USB接入到你的电脑上，在手机上一般会弹出一个提示选择允许进行USB调试



#### 在Unity中进行调试相关设置

在 File ——> Build Settings 中进行调试相关的设置

1. 完成知识点一相关内容后，可以在 Run Device 的设备列表中看到你的安卓设备

  如果Refresh刷新后也无法看到你的设备，证明你的连接有问题，或没有开启USB调试模式

2. 调试相关设置

  剖析器 性能问题排查相关（勾选了他们，我们才能在Unity的剖析器中获取完整信息）

  开启 Development Build 开发模式构建

  开启 Autoconnect Profiler 自动连接分析器

  开启 Deep Profiling 深度剖析



  断点 调试相关（勾选了他们，我们才能进行断点调试）

  开启 Script Debuggins 脚本调试

  开启 Wait For Managed Debugger 等待托管调试器



3. 项目基本的发布设置相关内容一定要设置正确

  比如：包名、项目名、公司名、密钥库等等



#### Unity连接安卓真机进行项目调试

点击Build and Run

这时Unity会自动发布apk应用该程序，然后安装到设备上自动启动



这时我们就可以

1. 利用Unity的 剖析器来分析应用程序

2. 进行断点调试，排查问题



注意：如果想要断点调试，自动启动应用程序后，弹出的提示窗口 不要点击OK先在VS中 工具栏——>调试——>附加 Unity 调试程序——>选择对应的设备再在运行的应用程序中点击ok



### Unity Remote



#### Remote是什么？

Remote翻译过来是远程的意思

Unity Remote是Unity提供给我们的一个可以安装到移动平台上的应用程序帮助我们进行调试的远程工具



该应用程序可以安装到 Android、IOS设备上，当我们在Unity编辑器运行项目时，移动设备上安装的Unity Remote会和Unity连接，Unity编辑器中的游戏画面会被发送到移动设备上，移动设备上的输入操作会返回到Unity编辑器运行的项目中



它的主要作用就是可以让我们省去发布安装包、安装到移动设备上的这一繁琐流程

可以直接通过Unity Remote远程连接来达到以下目的：

查看项目在设备上的视觉表现效果

测试移动设备输入逻辑是否能正常使用

设备的以下输入会被回传到Unity引擎中用于测试

触屏输入相关

GPS

陀螺仪

指南针

加速度计

相机拍照信息

等等输入信息

注意：

1.Unity引擎传递给移动设备的画面较模糊，帧率较低，我们只能看看大致的表现效果

2.我们主要通过这种方式，来测试移动设备输入相关的功能

比如：陀螺仪、GPS、触屏功能等等



#### 获取和使用Unity Remote

1.下载Unity Remote应用程序,并安装到移动设备上

2.将移动设备使用USB调试模式和电脑连接

3.在菜单栏Edit——>Project Setting——>Editor中设置Unity Remote的Device为任意设备

4.在移动设备运行Unity Remote

5.在Unity编辑器中启动项目，便可以进行远端调试



注意：

1.如果电脑插入多个Android设备，会自动选择第一台设备

2.Unity Remote存在的主要目的仅仅是为了快速的在设备上测试输入相关功能以及基本表现效果 如果要完整的测试，还是需要发布安装进行调试



### Android Logcat



#### Android Logcat是什么？

Android Logcat是Unity提供的一个拓展包，引入该拓展包后，我们可以在Unity当中对Android应用程序进行调试

我们可以通过该工具获取到：

1.安卓日志打印信息

2.安卓应用程序内存统计

3.安卓屏幕截图

4.安卓屏幕录像

5.堆栈跟踪

Unity2019.4及以上版本都可以引入Android Logcat来帮助我们进行调试



#### 如何使用Android Logcat工具

1.在Unity工程中引入Android Logcat包

2.连接Android设备

3.将Android Logcat连接到Android设备上点的应用程序，便可以查看相关信息



#### Android Logcat窗口基础知识

![image-20220903135914927](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136422.png)

##### A：工具栏

###### Auto Run：自动运行

启用后，当你构建和运行应用程序时Android Logcat窗口会连接到应用程序，显示对应的相关信息

###### No device：设备选择器

当没有Android设备连接电脑是，显示No Device，有设备时可以在这选择连接的Android设备

###### No Filter：过滤选择器

可以指定Android设备上显示消息的应用程序，此处显示的是应用程序的包名

###### 过滤输入框

用于过滤搜索消息日志，可以输入内容搜索信息

消息正文过滤：在输入框输入内容，则会自动筛选消息中存在输入字符的内容

优先级过滤：在下方点击Priority优先级，选择过滤的内容

Verbose：冗长的，所有
Debug：调试信息
Info：信息
Warn：警告
Error：错误
Fatal：致命的

按标签过滤：在下方点击Tag标签，选择过滤的内容

###### Filter Options：过滤器选项

决定如何使用选项过滤器输入过滤消息日志中的消息
Use Regular Expressions：使用正则表达式
Match Case：区分大小写

###### Reconnect：重新连接

重新连接到应用程序和设备，如果程序不再运行则重新连接到设备

###### Disconnect：断开和设备的连接

###### Clear：清除消息列表中的消息日志

###### Tools：工具相关

Screen Capture：屏幕捕获
从连接的Android设备上捕捉屏幕截图和视频

Open Terminal：开放终端
在Windows上打开命令调试符窗口，在macOS和Linux上打开终端

Stacktrace Utility：堆栈跟踪应用程序
解析自定义堆栈跟踪

Memory Window：内存窗口
跟踪为应用程序分配的内存

##### B：消息日志

![image-20220903140638857](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136658.png)



#### 屏幕捕获工具

屏幕捕获工具主要可以帮助我们进行游戏内截图，录屏

该工具可以帮助我们：

1.测试人员用来录制bug表现，反馈给程序人员进行解决

2.获取截屏、录屏信息，获取宣传资料



优点：直接PC端远程获取，方便快捷，便于记录bug表现效果

缺点：录制的视频不会捕获声音，录屏功能可以部分设备不支持

![image-20220903141016807](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136696.png)

![image-20220903141109817](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136092.png)



#### 堆栈跟踪实用工具

目前我们使用Android Logcat连接真机进行测试时

打印、警告、报错等等信息都会直接在主窗口中打印出来让我们进行分析

相当于它帮助我们进行了自动的堆栈跟踪解析



什么是自动堆栈跟踪解析呢？

就是我们可以在打印窗口看到具体是哪个脚本的哪行代码

出现了打印、警告、报错等等信息



而Tools(工具)中的Stacktrace Utility(堆栈跟踪实用工具)的作用是

允许我们复制粘贴自定义日志并解析堆栈跟踪



主要用于没有真机连接设备调试时

或者之后正式发布版本后

我们可以获取应用程序日志信息

将这些日志信息复制到该工具中用于翻译

(一般项目会在崩溃后，在本地存储一份崩溃日志，我们可以获取该日志)



因为这种情况下的日志信息中，不会明确指明是哪个脚本哪行代码

往往是一堆看不懂的符号,我们可以使用该工具进行翻译



比如：下面这个打印信息就是安卓的原始日志

E CRASH   :      #01  pc 01c65330  /data/app/com.CrashComp.Crash-J2Z_L0XSsSAZPkt9lab2rQ==/lib/arm/libunity.so

(DiagnosticsUtils_Bindings::ForceCrash(DiagnosticsUtils_Bindings::ForcedCrashCategory, ScriptingExceptionPtr*)+48)



这里面都是一堆特殊符号，并没有明确告诉你哪个脚本哪行代码

通过翻译后堆栈跟踪实用工具翻译后，我们就能够获取到哪个脚本哪行代码打印了输出了该信息



总而言之它该工具就是用来翻译发布后安卓应用程序的日志信息的



##### 堆栈跟踪实用工具的使用

使用堆栈跟踪实用工具的作用是可以翻译自己获取到的安卓日志信息

使用流程是：

1.设置项目的 so 文件路径

2.将日志信息复制到Original（初始）页签中

3.点击Resolve Stacktraces(解析堆栈跟踪)

4.在Resolved(已解析的日志)页签中查看翻译结果



注意：我们在电脑连接安卓设备通过Android Logcat进行调试时一般不使用该功能，只有当非连接调试时，获取了安卓崩溃日志后才会使用该功能



![image-20220903141656842](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136417.png)

![image-20220903141726069](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136849.png)



#### 内存窗口工具

首先了解 RAM（运行时内存）对于我们的意义

如果应用程序出现闪退情况，往往都是因为应用程序运行时内存的使用超出了设备允许的最大内存量，而被手机系统强行关闭

注意：

不同品牌、型号的手机配置都是不同的，手机后台运行的程序数量也是不同的，所以要根据实际情况进行测试获取目标设备的内存极限值，可以选取市面上的主流机型来进行测试

**内存窗口工具是用于跟踪应用程序RAM（运行时内存）的分配情况的**

我们可以使用它来分析应用程序可能存在的内存问题

我们可以使用它 定期自动的 或者 手动的 捕获内存快照

这里的内存快照就是捕获的这一瞬间应用程序的运行时内存使用情况



根据内存快照我们可以分析内存问题

比如：

1. 切换场景前和切换场景后的内存快照比较，判断是否存在内存泄露

2. 比如闪退前的内存快照，来分析设备占用多少内存时造成了闪退，确定内存瓶颈

等等



![image-20220903142133235](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136108.png)

Auto Capture：自动捕获
Unity会定期捕获应用程序的内存快照
注意：自动捕获，可能会影响性能表现，可能会造成卡顿，如果严重影响测试，建议使用手动捕获

Manual Capture：手动捕获
通过点击按钮，自己手动捕获内存快照

Disabled：不启用



![image-20220903142205592](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136948.png)

![image-20220903142318882](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242136572.png)



### Android Debug Brige

ADB是 Android Debug Brige（安卓调试桥）的简称

它是我们调试Android设备的一套指令集

它可以让我们通过指令来进行一些操作，来获取日志信息

比如：

1.关机、重启

2.安装、启动、卸载应用程序

3.删除、移动、复制文件

4.查看日志信息



我们最常使用的就是查看日志信息的功能

之前学习的Android Logcat工具其实就是利用了ADB来获取的信息



由于Android Logcat工具只能在Unity 2019.4及其以上版本才能使用

所以对于老版本的Unity来说，我们一般使用ADB来帮助我们进行调试



注意：

使用ADB工具的前提是 电脑和安卓设备处于USB调试连接



#### 如何使用ADB？

在Android SDK的路径中

寻找platform-tools文件夹

在其中可以找到adb.exe文件

我们需要配合命令调试符窗口（Windows）使用adb指令集



方法一：

直接在Android Logcat窗口中使用Tools中的Open Terminal（打开终端）

Window系统会自动打开命令调试符窗口

MacOS系统会自动打开终端窗口

会直接来到SDK文件夹中的platform-tools（平台工具）文件夹

在后面直接输入指令即可

adb 指令



方法二：

自己打开命令调试符窗口

输入adb.exe所在文件夹路径

比如：

D:\Android\android-sdk\platform-tools\adb 指令



#### 关键指令

常用指令

1.显示日志信息

 adb logcat

2.获取Unity相关日志信息

 adb logcat -s Unity



做了解的指令

1.查看abb相关信息，比如版本号、常用命令等

 adb help

2.查看adb版本

 adb version

3.安装apk

 adb install apk路径

4.覆盖安装

 adb install -r apk路径

5.卸载应用程序

 adb uninstall 应用程序包名

6.保留数据卸载应用程序

 adb uninstall -k 应用程序包名

7.查看连接的主机设备

 adb devices

8.从电脑上复制文件到手机

 adb push 电脑上文件路径 需要复制到的手机路径

9.从手机上复制文件到电脑

 adb pull 手机上文件路径 需要复制到的电脑路径

10.查看手机上安装的所有应用程序包名

 adb shell pm list packages

11.截图

 adb shell screencap 保存到的手机路径

12.录屏

 adb shell screenrecord 保存到的手机路径

等等



更多信息

https://developer.android.com/studio/command-line/adb.html



### Android Studio中调试应用程序 



#### 回顾Unity导出Android Studio工程

Build Settings中进行设置

1.勾选Export Project

2.勾选Development Build

3.导出到指定目录

4.使用Android Studio打开该工程

5.更新Gradle版本

6.删除 android.enableR8

7.保证构建成功



#### 在Android Studio中调试应用程序 

1.保证Android设备和电脑进行USB调试连接

2.菜单栏的Run页签中进行调试（若上部分Run置灰，可以重启一次Android Studio）

3.在下方的Logcat日志窗口和Profiler性能分析器窗口调试应用程序，排查问题



更多关于Android Studio调试和性能分析内容

调试：https://developer.android.google.cn/studio/debug

性能分析：https://developer.android.google.cn/studio/profile



总结

Android Studio中调试应用程序和Unity中使用 Android Logcat + Unity Profiler窗口类似

一般情况下，没有特殊需求，我们都在Unity中进行Android应用程序调试

相对Android Studio中调试来说更适合我们Unity程序员
