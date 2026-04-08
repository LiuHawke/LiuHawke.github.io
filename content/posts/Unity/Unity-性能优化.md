---
title: "Unity-性能优化"
date: 2023-03-05T21:31:20+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052044934.jpg
---

一个B站UP主的教程，后半部分涉及到很多urp源码修改暂时还看不懂，只记录了前半部的内容

视频地址：https://www.bilibili.com/video/BV1AL4y1b75c/?spm_id_from=333.999.0.0&vd_source=4c7b49a270731b8ec068bfb156c5af6a

# 资源优化

## Unity资源检测工具

### Asset Checker

**用于本地资源检测，帮助开发者尽早发现资源文件中存在的问题**

- 支持所有版本的Unity项目
- 不依赖Unity Editor，无需安装绿色运行
- 检测速度极快，可在UPR中查阅检测结果
- 支持命令行模式，可与CI/CD工具轻松集成，实现自动化检测
- 规则库持续更新
- 支持AssetBundle冗余检测
- 支持静态代码分析
   [Asset Checker 官方教程地址（包含工具下载地址）](https://upr.unity.cn/instructions/assetchecker#UserManual)

## Audio导入设置检查与优化

![image-20230305213552090](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052135123.png)

根据平台选择合理的音频设置，原始音频资源尽量采用未压缩WAV格式

移动平台对音乐音效统一采用单通道设置（Force to Mono）,并将音乐采样频率设置为22050Hz

一般勾选了强制单声道之后也要勾选Normalize选项，这样引擎内部会选择最合适的方式来强行转换单声道。

移动平台大多数声音尽量采用Vorbis压缩设置，IOS平台或不打算循环的声音可以选择MP3格式，对于简短、常用的音效，可以采用解码速度快的ADPCM格式（PCM为未压缩格式）

音频片段加载类型说明

- 简短音效导入后小于200kb，采用Decompress on Load模式
- 对于复杂音效，大小大于200kb，长度超过5秒的音效采用Compressed In Memory模式
- 对于长度较长的音效或背景音乐则采用Streaming模式，虽然会有CPU额外开销，但节省内存并且加载不卡顿

当实现静音功能时，不要简单的将音量设置为0，应销毁音频（AudioSource）组件，将音频从内存中卸载。

### 优化前后真机对比（Android）

Apk包体由原来的560.7M下降到544.6M

Audio部分的内存由76.1M下降到6.9M

CPU开销由2.5%左右上升到5%左右，这是由于部分音频资源才用了Streaming模式加载

![第壹节 Audio导入设置检查与优化](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303052134075.png)



## Model导入设置检查与优化

### DCC中模型导出

Unity 支持多种标准和专有模型文件格式（DCC）。Unity 内部使用 .fbx 文件格式作为其导入链。最佳做法尽可能使用 .fbx 文件格式，并且不应在生产中使用专有文件格式。

优化原始导入模型文件，删除不需要的数据

- 统一单位
- 导出的网格必须是多边形拓扑网格，不能是贝塞尔曲线、样条曲线、NURBS、NURMS、细分曲面等
- 烘培Deformers,在导出之前，确保变形体被烘培到网格模型上，如骨骼形变烘培到蒙皮权重上
- 不建议模型使用到的纹理随模型导出
- 如果你需要导入blend shape normals，必须要指定光滑组smooth groups
- DCC导出面板设置, 不建议携带场景信息导出，如不建议导出摄像机、灯光、材质等信息，因为这些的信息与Unity内默认都不同。除非你自己为某DCC做过自定义导出插件。

### Unity模型导入流程

![2](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303081151420.png)

### 原始模型文件对性能的影响点

- 最小化面数，不要使用微三角形，分布尽量均匀
- 合理的网络拓扑和平滑组
- 尽量少的使用材质个数
- 尽可能少的使用蒙皮网格
- 尽可能少的骨骼数量
- FK与IK节点没分离，IK节点没删除

### 模型优化

- 尽可能的将网格合并到一起，（）
- 尽可能使用共享材质
- 不要使用网格碰撞体
- 不必要不要开启网格读写
- 使用合理的LOD级别
- Skin Weights受骨骼影响个过多
- 合理压缩网格
- 不需要rigs和BlendShapes尽量关闭
- 如果可能，禁用法线或切线
- 多套模型

### 资源检查报告

FBX部分问题解读

- 其中两项建议与模型动画有关，而测试项目中所有模型资源都不涉及动画，可以将Rig标签下的Animation Type设置为None，并关闭Animation标签下的Import Animations选项，设置Materials标签中的Material Creation Mode为None.
- 开启Project Settings——>Player——>Optimization下的Vertex Compression与Optimize Mesh Data选项

## Texture基础概念

### 纹理类型

- **Default：**默认的纹理类型格式
- **Normal map：**法线贴图，可将颜色通道转换为适合实时法线贴图格式
- **Editor GUI and Legacy GUI：**在编辑器GUI控件上使用纹理请选择此类型
- **Sprite(2D and UI)：**在2D游戏中使用的精灵(Sprite)或UGUI使用的纹理请选择此类型
- **Cursor：**鼠标光标自定义纹理类型
- **Cookie：**用于光照Cookie剪影类型的纹理
- **Lightmap：**光照贴图类型的纹理，编码格式取决于不同的平台
- **Single Channel：**如果原始图片文件只有一个通道，请选择此类型

### 纹理大小

选择合适纹理大小应尽量遵循以下经验：

- 不同平台、不同硬件配置选择不同的纹理大小，Unity下可以采用bundle变体设置多套资源、通过Mipmap限制不同平台加载不同level层级的贴图。
- 根据纹理用途的不同选择不同的纹理加载方式，如流式纹理加载Texture Streaming、稀疏纹理Sparse Texture、虚拟纹理VirtualTexture等方式。
- 不能让美术人员通过增加纹理大小的方式增加细节，可以选择细节贴图DetailMap或增加高反差保留的方式。
- 在不降低视觉效果的情况下尽量减小贴图大小，最好的方式是纹理映射的每一个纹素的大小正好符合屏幕上显示像素的大小，如果纹理小了会造成欠采样，纹理显示模糊，如果纹理大了会造成过采样，纹理显示噪点。这一点做到完美平衡很难保障，可以充分利用Unity编辑器下SceneView->DrawMode->Mipmap来查看在游戏摄像机视角下哪些纹理过采样，哪些纹理欠采样，进而来调整纹理大小。

### 纹理颜色空间

 默认大多数图像处理工具都会使用sRGB颜色空间处理和导出纹理。但如果你的纹理不是用作颜色信息的话，那就不要使用sRGB空间，如金属度贴图、粗糙度贴图、高度图或者法线贴图等。一旦这些纹理使用sRGB空间会造成视觉表现错误。

### 纹理压缩

 纹理压缩是指图像压缩算法，保持贴图视觉质量的同时，尽量减小纹理数据的大小。默认情况下我们的纹理原始格式采用PNG或TGA这类通用文件格式，但与专用图像格式相比他们访问和采样速度都比较慢，无法通用GPU硬件加速，同时纹理数据量大，占用内存较高。所以在渲染中我们会采用一些硬件支持的纹理压缩格式，如ASTC 、ETC、ETC2、DXT等。

 如下图为未压缩、ETC2、ASTC6x6三种格式文件大小对比，我们可以看到在质量相差不大的情况下ASTC6x6压缩下大小可以减少到接近未压缩的十分之一。

![image-20230313202342010](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132023093.png)

### 纹理图集

纹理图集是一系列小纹理图像的集合，

- 优点：

 一是采用共同纹理图集的多个静态网格资源可以进行静态合批处理，减少DrawCall调用次数。

 二是纹理图集可以减少碎纹理过多，因为他们打包在一个图集里，通过压缩可以更有效的利用压缩，降低纹理的内存成本和冗余数据。

- 缺点

 美术需要合理规划模型，并且要求模型有相同的材质着色器，或需要制作通道图去区分不同材质。制作和修改成本较高。

### 纹理过滤

- **Nearest Point Filtering：**临近点采样过滤最简单、计算量最小的纹理过滤形式，但在近距离观察时，纹理会呈现块状。
- **Bilinear Filtering：**双线性采样过滤会对临近纹素采样并插值化处理，对纹理像素进行着色。双线性过滤会让像素看上去平滑渐变，但近距离观察时，纹理会变得模糊。
- **Trilinear Filtering：**三线性过滤除与双线性过滤相同部分外，还增加了Mipmap等级之间的采样差值混合，用来平滑过度消除Mipmap之间的明显变化。
- **Anisotropic Filtering：**各向异性过滤可以改善纹理在倾斜角度下的视觉效果，跟适合用于地表纹理。

### 纹理Mipmap

- Mipmap纹理

 逐级减低分辨率来保存纹理副本。相当于生成了纹理LOD，渲染纹理时，将根据像素在屏幕中占据的纹理空间大小选择合适的Mipmap级别进行采样。

- 优点：

 GPU不需要在远距离上对对象进行全分辨率纹理采样，因此可以提高纹理采样性能。

 同时也解决了远距离下的过采样导致的噪点问题，提高的纹理渲染质量。

- 缺点：

 由于Mipmap纹理要生成低分辨率副本，会造成额外的内存开销。

## Texture导入设置检查与优化

![4](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132030008.png)

### **Texture Shape**

- 2D 最常用的2D纹理，默认选项
- Cube 一般用于天空和与反射探针，默认支持Default、Normal、Single Channel几种类型纹理，可以通过Assets > Create > Legacy > Cubemap生成，也可以通过C#代码 Camera.RenderToCubemap在脚本中生成
- 2D Array 2D纹理数组，可以极大提高大量相同大小和格式的纹理访问效率，但需要特定平台支持，可以通过引擎SystemInfo.supports2DArrayTextures 接口运行时查看是否支持。
- 3D 通过纹理位图方式存储或传递一些3D结构话数据，一般用于体积仿真，如雾效、噪声、体积数据、距离场、动画数据等信息，可以外部导入，也可运行时程序化创建。

### **Alpha Source**

默认选择Input Texture Alpha就好，如果确定不使用原图中的Alpha通道，可以选择None。另外From Gray Scale我们一般不会选用

### **Alpha Is Transparency**

指定Alpha通道是否开启半透明，如果位图像素不关心是否要半透明可以不开启此选项。这样Alpha信息只需要占1bit。节省内存

### **Ignore** **Png** **file gamma**

是否忽略png文件中的gamma属性，这个选项是否忽略取决于png文件中设置不同gamma属性导致的显示不正常，一般原图制作流程没有特殊设置，这个选项一般默认就好。

### **Read/Write**

开启此选项会导致内存量增加一倍，默认我们都是不开启，除非你的脚本逻辑中需要动态读写该纹理时需要打开此选项。

**Streaming Mipmaps**(Texture Streaming部分讲解)

**Virtual Texture Only**(虚拟部分讲解)

### **Generate** **Mip** **Maps**

什么时候不需要生成MipMaps？

1. 2D场景
2. 固定视角，摄像机无法缩放远近

- **Border** **Mip** **Maps** 默认不开启，只有当纹理的是Light Cookies类型时，开启此选项来避免colors bleeding现象导致颜色渗透到较低级别的Mip Level纹理边缘上
- MipMap Filtering
  - **Box** 最简单，随尺寸减小，Mipmap纹理变得平滑模糊
  - **Kaiser**，避免平滑模糊的锐化过滤算法。
- **Mip Maps Preserve Coverage**，只有需要纹理在开启mipmap后也需要做Alpha Coverage时开启。默认不开启。
- **Fadeout MipMaps,** 纹理Mipmap随Mip层级淡化为灰色，一般不开启，只有在雾效较大时开启不影响视觉效果。

### **选择合适纹理过滤的最佳经验：**

- 使用双线性过滤平衡性能和视觉质量。
- 有选择地使用三线性过滤，因为与双线性过滤相比，它需要更多的内存带宽。
- 使用双线性和 2x 各向异性过滤，而不是三线性和 1x 各向异性过滤，因为这样做不仅视觉效果更好，而且性能也更高。
- 保持较低的各向异性级别。仅对关键游戏资源使用高于 2 的级别。

![image-20230313203153527](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132031557.png)

**Untiy官网推荐的、默认的和支持的纹理格式，按平台划分https://docs.unity3d.com/cn/2021.3/Manual/class-TextureImporterOverride.html**

### 其他可能有问题的纹理类型

- 纹理图集大小设置不合理，图集利用率低
- 大量只有颜色差异的图片
- UI背景贴图而不采用9宫格缩放的图
- 纯色图没有使用Single Channel
- 不合理的半透明UI，占据大量屏幕区域，造成Overdraw开销
- 大量2D序列帧动画，而且图片大，还不打图集
- 不合理的通道图利用方案
- 大量渐变色贴图，没有采用1像素过渡图，也不采用Single Channel, 粒子特效中较为常见。

## Animation导入设置检查与优化

### Rig标签页

![5](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132038739.png)

#### **Animation Type**

- None 无动画
- Legacy 旧版动画，不要用
- Generic 通用骨骼框架
- Humanoid 人形骨骼框架

##### **选择原则：**

- 无动画选择None
- 非人形动画选择Generic
- 人形动画
  - 人形动画需要Kinematices或Animation Retargeting功能，或者没有有自定义骨骼对象时选择Humanoid Rig
  - 其他都选择Generic Rig，在骨骼数差不多的情况下,Generic Rig会比Humanoid Rig省30%甚至更多的CPU的时间。

#### **Skin Weights**

 默认4根骨头，但对于一些不重要的动画对象可以减少到1根，节省计算量

#### **Optimize Bones**

 建议开启，在导入时自动剔除没有蒙皮顶点的骨骼

#### **Optimize Game Objects**

 在Avatar和Animatior组件中删除导入游戏角色对象的变换层级结构，而使用Unity动画内部结构骨骼，消减骨骼transform带来的性能开销。可以提高角色动画性能, 但有些情况下会造成角色动画错误，这个选项可以尝试开启但要看表现效果而定。注意如果你的角色是可以换装的，在导入时不要开启此选项，但在换装后在运行时在代码中通过调用AnimatorUtility.OptimizeTransformHierarchy接口仍然可以达到此选项效果。

### Animation标签页

![55](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132038517.png)

#### **Resmple Curves**

 将动画曲线重新采样为四元数数值，并为动画每帧生成一个新的四元数关键帧，仅当导入动画文件包含尤拉曲线时才会显示此选项

#### **Anim.Compression**

- **Off** 不压缩,质量最高，内存消耗最大
- **Keyframe Reduction** 减少冗余关键帧，减小动画文件大小和内存大小。
- **Keyframe Reduction and Compression** 减小关键帧的同时对关键帧存储数据进行压缩，只影响文件大小。
- **Optimal**，仅适用于Generic与Humanoide动画类型，Unity决定如何进行压缩。

#### **Animation Custom Properties**

 导入用户自定义属性，一般对应**DCC**工具中的**extraUserProperties**字段中定义的数据

### 动画曲线数据信息

![555](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132045156.png)=

- **Curves Pos:** 位置曲线
- **Quaternion:** 四元数曲线 **Resample Curves**开启会有
- **Euler:** 尤拉曲线
- **Scale:** 缩放曲线
- **Muscles:** 肌肉曲线，**Humanoid**类型下会有
- **Generic:** 一般属性动画曲线，如颜色，材质等
- **PPtr：**精灵动画曲线，一般2D系统下会有
- **Curves Total: **曲线总数
- **Constant:** 优化为常数的曲线
- **Dense:** 使用了密集数据（线性插值后的离散值）存储
- **Stream:** 使用了流式数据（插值的时间和切线数据）存储

### 动画文件导入设置优化后信息查看原则

1. 一看效果差异（与原始制作动画差异是否明显）
2. 二看曲线数量（总曲线数量与各种曲线数显，常量曲线比重大更好）
3. 三看动画文件大小（以移动平台为例，动画文件在小几百k或更少为合理，查过1M以上的动画文件考虑是否进行了合理优化）

## 工程目录与Assets目录设置

### **Unity**工程目录结构及用途

- **Asset文件夹**：用来存储和重用的项目资产
- **Library文件夹**：用来存储项目内部资产数据信息的目录**
- **Packages文件夹**：用来存储项目的包文件信息
- **Project Settings文件夹**：用来存储项目设置的信息
- **UserSettings文件夹**：用来存储用户设置信息
- **Temp文件夹**：用来存储使用Unity编辑器打开项目时的临时数据，一旦关闭Unity编辑器也会被删除
- **Logs文件夹**：用来存储项目的日志信息（不包含编辑器日志信息）

### Unity Assets目录中的特殊文件夹及用途

- Editor文件夹（可以多个）
- Editor Default Resources文件夹（根目录唯一）
- Gizmos文件夹（根目录唯一）
- Plugins文件夹（2019后已无，但仍可使用，仍能保障其中代码编译的优先顺序）
- Resources文件夹（可以多个，强烈建议正式项目中一定不要有此文件夹）
- Standard Assets文件夹（根目录唯一，其中代码编译优先）
- StreamingAssets文件夹（根目录唯一）
- 忽略导入的文件夹
  1. *隐藏的文件夹*
  2. *以**"."**开头的文件和文件夹*
  3. *以**"~"**结尾的文件和文件夹*
  4. *扩展名为**cvs**的文件和文件夹*
  5. *扩展名为**.tmp**的文件夹*

### **Assets**目录结构设计

**一级目录设计原则：**

- 目录尽可能少
- 区分编辑模式与运行模式
- 区分工程大版本
- 访问场景文件、全局配置文件便捷
- 不在一级目录做资源类别区分，只有Video类视频建议直接放到StreamAssets下

**二级目录设计原则：**

- 只区分资源类型
- 资源类型大类划分要齐全
- 不做子类型区分
- 不做功能区分
- 不做生命周期区分

**三级目录设计原则：**

- Audio/Texture/Models三级目录做子类型区分
- 其他类型资源可按功能模块/生命周期区分

**四级目录设计原则：**

- 只有Audio/Texture/Models做四级目录，可按工呢个模块/生命周期划分

## 资源导入工作流

### **资源导入工作流的三种方案**

#### **1.** **手动编写工具**

**优点：**根据项目特点自定义安排导入工作流，并且可以和后续资源制作与大包工作流结合

**缺点：**存在开发和维护成本，会让编辑器菜单界面变得复杂，对新人理解工程不友好

**适合类型：**大型商业游戏团队

**AssetPostprocessor**：

 编写编辑器代码继承AssetPostprocesser对象自定义实现一些列OnPreprocessXXX接口修改资源导入设置属性

伪代码：

```c#
public class XXXAssetPostprocessor : AssetPostprocessor 
{
		public void OnPreprocessXXXAsset()
		{
				XXXAssetImporter xxxImporter = (XXXAssetImporter)assetImporter;
				xxxImporter.属性 = xxx
				...
				xxxImporter.SaveAndReimport();
		}
}
```

参考资料： [https://docs.unity3d.com/](https://docs.unity3d.com/cn/2021.2/Manual/BestPracticeUnderstandingPerformanceInUnity4.html)[cn](https://docs.unity3d.com/cn/2021.2/Manual/BestPracticeUnderstandingPerformanceInUnity4.html)[/2021.2/Manual/BestPracticeUnderstandingPerformanceInUnity4.html](https://docs.unity3d.com/cn/2021.2/Manual/BestPracticeUnderstandingPerformanceInUnity4.html)

**AssetsModifiedProcessor**（新试验接口）：

 资源被添加、删除、修改、移动时回调该对象的OnAssetsModified接口

```c#
void OnAssetsModified(string[] changedAssets, string[] addedAssets, string[] deletedAssets, AssetMoveInfo[] movedAssets)
{
		...
}
```

#### **2.** **利用Presets功能**

**优点：**使用简单方便，只需要Assets目录结构合理规范即可

**缺点：**无法和后续工作流整合，只适合做资源导入设置。

**适合类型：**小型团队或中小规模项目

参考资料：https://docs.unity3d.com/cn/2021.2/Manual/DefaultPresetsByFolder.html

#### **3.** **利用AssetGraph工具**

**优点：**功能全，覆盖Unity资源工作流全流程，节点化编辑，直观

**缺点：**有一定上手成本，一些自定义生成节点也需要开发，不是Unity标准包，Unity新功能支持较慢。

**适合类型：**任何规模项目和中大型团队

AssetGraph仓库地址：https://github.com/Unity-Technologies/AssetGraph

## 编辑器资源优化

### 场景

**场景结构设计原则**

1. 合理设计场景一级节点的同时，避免场景节点深度太深，一些代码生成的游戏对象如果不需要随父节点进行Transform的，一律放到根节点下。
2. 尽量使用Prefab节点构建场景，而不是直接创建的GameObject节点
3. 避免DontDestroyOnLoad节点下有太多生命周期过长或引用资源过多的复杂节点对象。Additve模式添加的场景要尤为注意。
4. 最好为一些需要经常访问的节点添加tag，静态节点一定要添加Static标记。

> 注意：复杂场景中，对于设置好Tag的节点，使用FindGameObjectWithTag方法取查找该节点更高效

### 预制体

**预制体Prefab**

 Unity中的预制体是用来存储游戏对象、子对象及其所需组件的可重用资源，一般来说预制体资源可充当资源模版，在此模版基础上可以在场景中创建新的预制体实例。

**使用预制体的好处**

- 由于预制体系统可以自动保持所有实例副本同步，因此可以比单纯地简单复制粘贴游戏对象做到更好的对象管理。
- 此外通过预制体嵌套(Nested Prefabs)可以将一个预制体嵌套到另一个预制体中，从而创建多个易于编辑的复杂游戏对象层级视图。
- 可以通过覆盖各个预制体实例的设置来创建预制体变体(Prefabs Variant)，从而可以将一系列覆盖组合在一起形成有意义预制体的变化。

**嵌套预制体与单预制体相比的优点与缺点**

- 优点：
  - 嵌套预制体方便预制体管理，方便资源重复利用，易于统计场景复杂度
  - 美术制作时可以比较合理的分配UV,和贴图利用率
  - 方便关卡设计人员发挥，充分合理利用资源
  - 嵌套预制体比较方便利用工具做LOD，LOD效果也比较好
  - 嵌套预制体修改方便，只需修改子预制体就可以做到所有嵌套预制体同步
  - 比较方便做场景遮挡剔除，可以做到精细的遮挡剔除优化效果
- 缺点：
  - 手动做Bundle依赖时要按Scene方式处理，依赖关系较为复杂
  - 可能会增加材质数量与Drawcall数量
  - 不太适合做大规模远景对象。
  - 美术与关卡设计人员要充分考虑组合复杂度与特例场景显示，避免重复性和单一性，需要更多的沟通成本

**使用Prefab变体的一些限制**

- 不能改变本体Prefab游戏对象 （GameObject）层级
- 不能删除本体Prefab中的游戏对象，但可以通过Deactive游戏对象来达到与删除游戏对象同样的效果
- 对于Prefab变体要保持其Override属性的变化，不能通过Apply to base把这些变化应用到本体Prefab上，这样会破坏基础Prefab的结构和功能。

## UGUI

#### Unity UI性能的四类问题

1. Canvas Re-batch 时间过长
2. Canvas Over-dirty, Re-batch次数过多
3. 生成网格顶点时间过长
4. Fill-rate overutilization

#### Canvas画布

 Canvas负责管理UGUI元素，负责UI渲染网格的生成与更新，并向GPU发送DrawCall指令。

#### Canvas Re-batch过程

1. 根据UI元素深度关系进行排序
2. 检查UI元素的覆盖关系
3. 检查UI元素材质并进行合批

#### UGUI渲染细节

- UGUI中渲染是在Transparent半透明渲染队列中完成的，半透明队列的绘制顺序是从后往前画，由于UI元素做Alpha Blend,我们在做UI时很难保障每一个像素不被重画，UI的Overdraw太高，这会造成片元着色器利用率过高，造成GPU负担。
- UI SpriteAtlas图集利用率不高的情况下，大量完全透明的像素被采样也会导致像素被重绘，造成片元着色器利用率过高；同时纹理采样器浪费了大量采样在无效的像素上，导致需要采样的图集像素不能尽快的被采样，造成纹理采样器的填充率过低，同样也会带来性能问题。

#### Re-Build过程

- 在WillRenderCanvases事件调用PerformUpdate::CanvasUpdateRegistry接口
  - 通过ICanvasElement.Rebuild方法重新构建Dirty的Layout组件
  - 通过ClippingRegistry.Cullf方法，任何已注册的裁剪组件Clipping Compnents(Such as Masks)的对象进行裁剪剔除操作
  - 任何Dirty的 Graphics Compnents都会被要求重新生成图形元素
- Layout Rebuild
  - UI元素位置、大小、颜色发生变化
  - 优先计算靠近Root节点，并根据层级深度排序
- Graphic Rebuild
  - 顶点数据被标记成Dirty
  - 材质或贴图数据被标记成Dirty

#### 使用Canvas的基本准则：

- 将所有可能打断合批的层移到最下边的图层，尽量避免UI元素出现重叠区域
- 可以拆分使用多个同级或嵌套的Canvas来减少Canvas的Rebatch复杂度
- 拆分动态和静态对象放到不同Canvas下。
- 不使用Layout组件
- Canvas的RenderMode尽量Overlay模式，减少Camera调用的开销

#### UGUI射线（Raycaster）优化：

- 必要的需要交互UI组件才开启“Raycast Target”
- 开启“Raycast Targets”的UI组件越少，层级越浅，性能越好
- 对于复杂的控件，尽量在根节点开启“Raycast Target”
- 对于嵌套的Canvas，OverrideSorting属性会打断射线，可以降低层级遍历的成本

#### 滚动视图Scroll View优化

- 使用RectMask2d组件裁剪
- 使用基于位置的对象池作为实例化缓存

#### UI字体

- 避免字体框重叠，造成合批打断
- 字体网格重建
  - UIText组件发生变化时
  - 父级对象发生变化时
  - UIText组件或其父对象enable/disable时
- TrueTypeFontImporter
  - 支持TTF和OTF字体文件格式导入

![10-0](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303132156992.png)

- 动态字体与字体图集
  - 运行时，根据UIText组件内容，动态生成字体图集，只会保存当前Actived状态的 UIText控件中的字符
  - 不同的字体库维护不同的Texture图集
  - 字体Size、大小写、粗体、斜体等各种风格都会保存在不同的字体图集中（有无必要，影响图集利用效率，一些利用不多的特殊字体可以采用图片代替或使用Custom Font，Font Assets Creater创建静态字体资源）
  - 当前Font Texture不包含UIText需要显示的字体时，当前Font Texture需要重建
  - 如果当前图集太小，系统也会尝试重建，并加入需要使用的字形，文字图集只增不减
  - 利用Font.RequestCharacterInTexture可以有效降低启动时间
- UI控件优化注意事项
  - 不需要交互的UI元素一定要关闭Raycast Target选项
  - 如果是较大的背景图的UI元素建议也要使用Sprite的九宫格拉伸处理，充分减小UI Sprite大小，提高UI Atlas图集利用率
  - 对于不可见的UI元素，一定不要使用材质的透明度控制显隐，因为那样UI网格依然在绘制，也不要采用active/deactive UI控件进行显隐，因为那样会带来gc和重建开销
  - 使用全屏的UI界面时，要注意隐藏其背后的所有内容，给GPU休息机会。
  - 在使用非全屏但模态对话框时，建议使用**OnDemandRendering**接口，对渲染进行降频。
  - 优化裁剪UI Shader，根据实际使用需求移除多余特性关键字。

## 物理

### Unity中的物理解决方案

- Box2D
- Nvidia PhysX
- Unity Physics
- Havok Physics for Unity

### Unity中的物理组件Collider部分的优化

- Trigger与Collider
  - Trigger对象的碰撞会被物理引擎所忽略，通过OnTriggerEnter/Stay/Exit函数回调
  - Collider对象由物理引擎触发碰撞，通过OnCollisionEnter/Stay/Exit函数回调
  - Trigger对象不需要RigidBody组件，Collider对象必须至少有一个Collider对象有RigidBody组件
  - Trigger对象更高效

### Unity中的物理组件Collider部分的优化

- 尽量少使用MeshCollider，可以用简单Collider代替，即使用多个简单Collider组合代替也要比复杂的MeshCollider来的高效
- MeshCollider是基于三角形面的碰撞
- MeshCollider生成的碰撞体网格占用内存也较高
- MeshCollider即使要用也要尽量保障其是静态物体
- 可以通过PlayerSetting选项中勾选Prebake Collision Meshes选项来在构建应用时预先Bake出碰撞网格。

### Unity中的物理组件RigidBody部分的优化

- Kinematic与RigidBody
  - Kinematic对象不受物理引擎中力的影响，但可以对其他RigidBody施加物理影响。
  - RigidBody完全由物理引擎模拟来控制，场景中RigidBody数量越多，物理计算负载越高
  - 勾选了Kinematic选项的RigidBody对象会被认为是Kinematic的，不会增加场景中的RigidBody个数
  - 场景中的RigidBody对象越少越好

### Unity中的RayCast与Overlap部分的优化

- Unity物理中RayCast与Overlap都有NoAlloc版本的函数，在代码中调用时尽量用NoAlloc版本，这样可以避免不必要的GC开销
- 尽量调用RayCast与Overlap时要指定对象图层进行对象过滤，并且RayCast要还可以指定距离来减少一些太远的对象查询
- 此外如果是大量的RayCast操作还可以通过RaycastCommand的方式批量处理，充分利用JobSystem来分摊到多核多线程计算。

## 动画

### Animation的一些细节

- 播放单个AnimationClip速度，Legacy Animation系统更快，因为老系统是直接采样曲线并直接写入对象Transform
- 针对动画的缩放曲线比位移、旋转曲线开销更大
- 常数曲线不会每帧写入场景，更高效

### Animator的一些细节

- 不要使用字符串来查询Animator
- 使用曲线标记来处理动画事件
- 使用Target Marching函数来协助处理动画
- 将Animator的CullingMode设置成Based On Renderers来优化动画，并禁用SkinMesh Renderer的Update When Offscreen属性来让角色不可见时动画不更新

### Internal Animation Update

![13-0](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141312998.png)]

### Animator VS Animation

- Animation可以将任何对象属性制作成Animation Clip, Animator是将Animaiton Clip组织到状态机流程图中使用
- Animation与Animator播放动画时的效率是有个临界点的，这个临界点是根据动画曲线条数来的，当动画曲线条数小于这个临界点时Animation快，当动画曲线条数大于这个临界点时Animator快
- 当Cpu核数较少时，Animation播放动画有优势，当Cpu核数较多时，Animator表现会更好
- Animator Controller Graph中的所有动画节点的Animation Clip都会载入到内存中，当有海量动画状态机节点时，内存开销较大

### Playable API VS Animator

#### **Playable API优点**

- 支持动态动画混合，可为场景中的对象提供自己的动画，并可以动态添加到PlayableGraph当中使用
- 允许创建播放单个动画，而并不会产生创建和管理AnimatorController资源所涉及的开销，可更加灵活的控制PlayableGraph的数据流，可以插入自定义的AimationJob。
- 可以控制动画文件加载策略，按需加载、异步加载等
- 允许用户动态创建混合图，并直接逐帧控制混合权重（甚至可以混合AniationClip与AnimatorController动画）
- 可以运行时动态创建，根据条件添加可播放节点。而不需要提前提供一套PlayableGraph运行时启动和禁用节点，可以做到自由度更高的override机制
- 可加载自定义配置数据，更加方便的和其他游戏系统整合

#### **Playable API缺点**

- 没有直接使用Animator直观
- 混合模式没有现成的，需要自己实现
- 需要开发更多的配套工具
- 有一定学习成本

### 解决方案选择

- 一些简单、少量曲线动画可以使用Animation或动画区间库如Dotween\iTween等完成，如UI动画，Transform动画等。
- 角色骨骼蒙皮动画如果骨骼较少，Animation Clip资源不多，对动画混合表现要求不高的项目可以采用Legacy Animation。注意控制总体曲线数量
- 一些角色动画要求与逻辑有较高的交互、并且动画资源不多的项目可以直接用Animator Graph完成
- 一些动作游戏，对动画混合要求较高、有一些高级动画效果要求、动画资源量庞大的项目，建议采用Animator+Playable API扩展Timeline的方式完成。

# 性能优化之道

## 性能优化问题的本质

- 慢与快的问题
- 前提
  - 稳定性：不能因优化造成稳定性变差
  - 兼容性：不能因优化导致兼容性变差
  - 性价比：优化要有度，考虑成本与复杂度

## 性能优化的流程

1. 发现问题（什么平台、什么操作系统、什么情况下出现问题，一般问题还是特例问题等）
2. 定位问题（什么地方造成的性能问题，我们要用什么工具、什么方法确定瓶颈）
3. 研究问题（确定用什么方案处理这个问题，要考虑性能优化的前提）
4. 解决问题（按问题研究的结论去实际处理，并验证处理结果与预期的一致性）

## 影响性能的四大类问题



![image-20230314135721609](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141357740.png)

![image-20230314135853987](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141400550.png)

![image-20230314135947167](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141400230.png)

## 隐藏的几类小问题

- 功耗比
- 填充率
- 发热量

## 性能问题可能的情况

瓶颈可能性按由高到低的顺序排列

- CPU利用率
- 带宽利用率
- CPU/GPU强制同步
- 片元着色器指令
- 几何图形到CPU到GPU的传输
- 纹理CPU到GPU的传输
- 顶点着色器指令
- 几何图形复杂性

## 经常用的优化思路

- 升维与降维
- 维度转换，如空间与时间，量纲转换

# 性能优化实践

## 移动平台优化顺序

先优化ios，再优化android
先共性性能优化，再兼容性方面的性能优化

## 常用性能检测

### 性能检测工具

#### Unity Profiler

**Unity下常见的等待函数**

- WaitForTargetFPS：等待达到目标帧率，一般这种情况CPU与GPU都没什么负载问题
- Gfx.WaitForGfxCommandsFrom MainThread/WaitForcommand：渲染线程已经准备接受新的渲染命令，一般瓶颈在CPU
- Gfx.WaitForPresentonGfxThread/WaitForPresent： 主线程等待渲染线程绘制完成，一般瓶颈在GPU
- WaitForJobGroupID：等待工作线程完成，一般瓶颈在CPU

#### FrameDeubgger

#### [UPR](https://upr.unity.cn/)

#### Xoce中Metal Capture

##### Enabling Frame Capture

Configure your project to work with the Metal frame debugger.

###### [Navigate to the Metal Scheme Settings](https://developer.apple.com/documentation/metal/debugging_tools/enabling_frame_capture#2954760)

1. In the toolbar, choose Edit Scheme from the scheme menu. (Alternatively, choose Product > Scheme > Edit Scheme.)
2. In the scheme action panel, select Run.
3. In the action setting tab, select Options.



![An Xcode screenshot that shows the scheme menu.](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141435454.png)





![An Xcode screenshot that shows the scheme editor, scheme actions, scheme options, and Metal tools.](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141435695.png)





###### [Choose a GPU Frame Capture Option](https://developer.apple.com/documentation/metal/debugging_tools/enabling_frame_capture#2943446)

Choose the option that best suits your debugging needs:

- Automatically Enabled: Captures Metal or OpenGL ES API usage in your app. If your target doesn’t link to the Metal or OpenGL ES framework, the Capture GPU Frame icon isn’t shown in the debug bar. If your app uses both the Metal and OpenGL ES API, you can click and hold the Capture GPU Frame icon to choose which API usage to capture.
- Metal: Captures only Metal API usage in your app. If your app doesn’t use Metal API, Xcode disables the Capture GPU Frame icon in the debug bar.
- Disabled: Doesn’t capture Metal usage in your app.

You can also choose whether Xcode profiles the trace data once capture is complete.

#### 游戏性能调试工具汇总

##### PC通用性能分析工具（某些需要特殊硬件支持）

AMD μProf：   https://developer.amd.com/amd-uprof/

NVIDIA NSight：https://developer.nvidia.com/tools-overview

Intel VTune：https://www.intel.com/content/www/us/en/developer/tools/oneapi/vtune-profiler.html

Intel GPA suite：https://www.intel.com/content/www/us/en/developer/tools/graphics-performance-analyzers/overview.html

Superluminal：https://superluminal.eu/

##### 平台性能分析工具（需要特殊操作系统、浏览器或硬件支持）

IOS:
Xcode Instruments: 

https://developer.apple.com/library/archive/documentation/AnalysisTools/Conceptual/instruments_help-collection/Chapter/Chapter.html

Android
Android Studio:  

https://developer.android.com/studio/profile

Arm Mobile Studio: 

https://developer.arm.com/Tools%20and%20Software/Arm%20Mobile%20Studio

Snapdragon Profiler:

https://developer.qualcomm.com/software/snapdragon-profiler

Windows/XBox
PIX:

https://devblogs.microsoft.com/pix/introduction/

PlayStation
CPU profiler tools:

https://partners.playstation.net/

WebGL
Chrome DevTools Performance:

https://developer.chrome.com/docs/devtools/evaluate-performance/

Firefox Profiler:

https://profiler.firefox.com/

GPU性能调试工具（某些需要对应硬件支持）
Arm Graphics Analyzer：

https://developer.arm.com/Tools%20and%20Software/Graphics%20Analyzer

Tegra Graphics Debugger：

https://developer.nvidia.com/tegra-graphics-debugger

Intel Graphics Performance Analyzers：

https://www.intel.com/content/www/us/en/developer/tools/graphics-performance-analyzers/overview.html

NVIDIA Nsight Frame Debugger:

https://docs.nvidia.com/gameworks/content/developertools/desktop/frame_debugger_ogl.htm

AMD Radeon Developer Tool Suite:

https://gpuopen.com/tools/

Xcode Frame Capture Debugging Tools:

https://developer.apple.com/documentation/metal/debugging_tools

Visual Studio Graphics Diagnostics:

https://docs.microsoft.com/en-gb/visualstudio/debugger/graphics/visual-studio-graphics-diagnostics?view=vs-2019&redirectedfrom=MSDN&viewFallbackFrom=vs-2015

RenderDoc:

https://renderdoc.org/builds 

## 渲染优化

### SSAO优化

#### 什么是SSAO

SSAO(Screen Space Ambient Occlusion 屏幕空间环境光遮蔽) : **可以实时实现环境光遮蔽效果，让画面更'真实'的一种渲染技术**。 通过获取像素的 深度缓冲 、 法线缓冲 以及 像素坐标 来计算实现，来近似的表现物体在间接光下产生的阴影。SSAO(Screen Space Ambient Occlusion 屏幕空间环境光遮蔽) : **可以实时实现环境光遮蔽效果，让画面更'真实'的一种渲染技术**。 通过获取像素的 深度缓冲 、 法线缓冲 以及 像素坐标 来计算实现，来近似的表现物体在间接光下产生的阴影。

#### Unity中的SSAO设置

打开**RenderData**, 再**Add Renderer Feature**, 选择**Screen Space Ambient Occlusion**

![image-20230314144543131](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141445224.png)

#### SSAO进一步优化

- 使用HBAO(horizon-based ambient occlusion)或GTAO(Ground Truth Ambient Occlusion)方案替代SSAO
- 针对SSAO的Shader指令做进一步优化
- 可以采用烘焙AO到光照贴图的方案替换SSAO方案



### AA反走样优化

#### 什么是AA

**anti-aliasing**简称**AA**，也译为**抗锯齿**或**反走样**、消除混叠、抗图像折叠失真等。它是一种消除显示器输出的画面中图物边缘出现凹凸锯齿的技术，那些凹凸的锯齿通常因为高解析度的讯号以低解析度表示或无法准确运算出3D图形坐标定位时所导致的图形混叠（aliasing）而产生的，反锯齿技术能有效地解决这些问题。

#### 反走样方案的发展

- 第一代：SSAA（超级采样抗锯齿Super-Sampling Anti-Aliasing ）

- 第二代：MSAA（多重采样抗锯齿 Multi Sampling Anti-Aliasing）

  ​              FXAA（快速近似抗锯齿Fast Approximate Anti-Aliasing)

  ​              SMAA（增强子像素形变抗锯齿Enhanced Subpixel Morphological Anti-Aliasing）

- 第三代：TAA（时间序列抗锯齿Temporal Anti-Aliasing)

- 第四代：DLSS（基于深度学习的超级采样Deep Learning Super Sampling

注：这里只列出了Unity反走样的一些发展流程，其他反走样方案这里不包括。

![image-20230314145703249](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141457343.png)

#### Uniy中的AA设置

效率：FXAA>SMAA>TAA>MSAA

质量：MSAA>SMAA>TAA>FXAA

![image-20230314150003976](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141500080.png)

### 后处理优化

#### 什么是后处理

Post Process是基于屏幕的后期处理，原理是将摄像机显示的画面在缓冲区中使用滤镜和效果处理，然后再显示出来。可以用来模拟物理摄像机和电影特效。

#### Unity中的后处理设置

**Post Process Layer**

通常我们在Camera上挂一个Post Process Layer脚本用来控制后期效果。也可以挂在空物体上，通过**Trigger**设置Camera来对相机起作用。**Layer**用来管理Post Process Volume。对Post Process Volume设置Layer，属于Post Process Layer中设置的Layer才会生效。

**URP内置了Post-process，不需要Layer，直接使用Volume System控制后期效果。**

#### Unity URP下支持的后处理效果分类

- 色彩校正与增强
  Channel Mixer、Color Adiustment、color Curves.
  Lift, Gamma,and Gain. Shadows/Midtones/Highlights、
  Tonemapping、 White Balance 、 Split Toning
- 画面效果增强
  Bloom、hromatic Aberration. Depth of Field、 Film Grain.
  Motion Blur
- 镜头效果
  Lens Disortion、 Panini Projection. Vignette、 Lens Flare

![image-20230314153336633](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141533743.png)

![image-20230314153558542](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141535649.png)

![image-20230314153650833](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141536946.png)



## 渲染提前优化

### Culling-剔除

#### 哪些是需要剔除的内容 

广义上讲：

- 看不见的像素、网格和对象 
- 重复的、用不到的资源 
- 不需要、不执行的代码

#### Unity中的剔除

- 像素剔除 ：摄像机平截头体剔除、Back-face Culling、Early- Z、Pre-Z Pass 
- 网格剔除：Layer Mask、可见距离剔除、Occlusion •灯光剔除：Tile-Based Deferred Rendering、Forward+ 
- 场景剔除：Additive Scene

![image-20230314155939300](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141559416.png)

#### 用户护展剔除 

- 场景数据结构：Octree、BSP Tree、Portal. voxelization. SDF等
- GPU Culing: Hi-Z Pass. Temporal Reprojection Culing、 cluster、 Tile-based Visible Buffer等

### Simplization-简化

#### 哪些是需要简化的内容

广义上讲：

- 运行效率较重的资源 
- 低效、不合适功能

#### Unity下的简化

- Quality Settings 
- 通过烘焙光照简化实时光照 
- 通过Bounding 
- Box或替代体碰撞代替Mesh碰撞 
- 通过Local Volume代替Global Volume RayCast代替Spherecast、 CapsuleCast等 
- 纹理文字代替系统文字 
- Mesh LoD 
- Shader LoD 
- HLOD 
- 通过Camer Override代替URP管线中的一些通用设置 
- 各种OnDemand更新或分级设置接口
- ……

#### 用户扩展简化

- 场景简化数据结构
- 第三方LOD方案
- Mesh Impostor
- Animation LoD
- 骨骼LOD
- 2D寻路代替Navigation Mesh
- 扩屐类似OnDemand接口

### Batching-合批

#### 哪些内容需要Batching 

广义上讲： 

- 资源Batching (Mesh、Texture、Shader参数、材质属性） 
- Draw call Batching (Static Batching、Dynamic Batching) 
- GPU Instancing(直接渲染、间接渲染、程序化间接渲染） 
- Set Pass call Batching. ( SRP Batching)

##### 资源的Batching

- Mesh:

  Mesh.CombineMesh，合并静态网格对象

  Submeshes->Single Mesh,合并材质与贴图，不同材质通过通道图标记

- Texture:

  AtlasTexture,通过纹理坐标映射多张贴图

  TextureArray纹理数组

- Shader变量与材质属性

  Material Property Block (Build In管线）

  Const buffer (SRP管线）

![image-20230314163934101](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303141639222.png)

#### Batching优化顺序 

**资源Batching >SRP Batching = Static Batching > GPU Instancing > Dynamic Batching**

#### Batching使用限制

##### 资源Batching限制

- Static Batching限制

  额外内存开销

  64000个顶点限制

  影响Culling剔除

- Dynamic Batching限制

  合批不超过900个顶点属性 ，（注意不是900个顶点）

  除了渲染阴影对象外，相同材质，不同材质实例也不能合并

  具有光照贴图的游戏对象如果有附加渲染器参数时 ，如果需要动态合批这些对象，他们必须指向相同的光照贴图位置。

  有多Shader Pass的游戏对象无法做动态合批

  受多个光照影响的游戏对象，满足动态合批条件合批后，只会受一个光源的影响

  延迟涫染下不支持动态合批

  CPU开销可能会增大，需要测试开启使用

##### GPU Instancing限制

- 图形API版本要求
- 与SRPBatcher不兼容
- 不同绘制API的参数与绘制个数不同
- 渲染顶点数较少的网格时，效率可能会较差。（测试使用）

##### SRP Batching限制

- 图形API版本要求
- 必须是SRP渲染管线
- 粒子对象不能合批
- 使用MaterialProperty Blocks的游戏对象不能合批
- Shader必须是compatible的

#### Batching 失败信息与原因

1. “An object is affected by multiple forward lights.” 此物体受到多个前向灯光的影
2. “Objects have different materials.” 此物体有不同的材质
3. “An object is using a multi-pass shader.” 此物体使用了多pass着色器
4. “An object has odd negative scaling.” 此物体Trasform的Scale使用了负数
5. “Either objects have different \”Receive Shadows\“ settings, or some objects are within the shadow distance, while some other objects are not.” 此物体接收阴影的设置不同，或者物体有不同的的阴影距离设置
6. “Objects are affected by different forward lights.” 此物体受到不同的前向灯光影响
7. “Objects are on different lighting layers.” 物体在不同的Lighting Layer上
8. “Objects have different \”Cast Shadows\“ settings.”  此物体有不同的投影体设置
9. “Objects either have different shadow caster shaders, or have different shader properties / keywords that affect the output of the shadow caster pass.” 此物体有不同的投影着色器设置或者有不同的着色器属性或者关键字影响Shadow Caster Pass的输出
10. “The shader explicitly disables batching with the \”DisableBatching\“ tag.” 着色器中显式设置了DisableBatching的标记
11. “Objects have different MaterialPropertyBlock set.”  此物体有不同的MaterialPropertyBlock集合
12. “Non-instanced properties set for instanced shader.”  Instanced的着色器有非instanced属性集
13. “Objects are lightmapped.”  物体使用了不同的LightMap或者虽然使用相同的LightMap但使用的UV不同
14. “Objects are affected by different light probes.”  此物体受到不同的光照探针影响
15. “Objects are shadowed by baked occlusions and have different occlusion factors.”  此物体烘焙了遮挡，并且设置了不同的遮挡因子
16. “Objects are affected by different reflection probes.”  此物体受到不同的反射探针影响
17. “Rendering different meshes or submeshes with GPU instancing.” 使用GPU实例化渲染不同的网格或子网格
18. “Objects have different batching-static settings.” 此物体有不同的静态合批设置
19. “Objects belong to different static batches.” 此物体归属不同的Static Batches
20. "Dynamic Batching is turned off in the Player Settings or is disabled temporarily in the current context to avoid z-fighting.” 在Player Settings中关闭了动态合批，或者在当前的环境中为了避免深度冲突而临时关闭了合批
21. “There are too many indices (more than 32k) in a dynamic batch.” 动态合批中有太多的索引（大于32k）
22. “A mesh renderer has additional vertex streams. Dynamic batching doesn‘t support such mesh renderers.” Mesh Renderer具有其他顶点流。动态批处理不支持此类网格渲染器。
23. “A submesh we are trying to dynamic-batch has more than 300 vertices.” 动态合批超过300个顶点
24. “A submesh we are trying to dynamic-batch has more than 900 vertex attributes.” 动态合批超过900个顶点属性
25. “This is the first draw call of a new shadow cascade.” 新阴影级联的第一次绘制调用
26. “The material doesn‘t have GPU instancing enabled.”  材质未启用GPU Instancing功能
27. “Objects are rendered using different rendering functions. This can happen if the type of renderer is different (eg Mesh/Skinned Mesh) or when using different settings within the same renderer, such as Sprite Masking.”  使用不同的渲染。如果渲染器的类型不同（例如网格/蒙皮网格），或者在同一渲染器中使用不同的设置（例如精灵遮罩），则可能会发生这种情况。
28. “Objects have different batching keys. This is usually caused by using different vertex streams on Particle Systems, or by mixing Lines and Trails, or by mixing lit and unlit geometry.” 此对象具有不同的Batching Keys。 这通常是由于在粒子系统上使用不同的顶点流，或混合线和轨迹，或混合Lit和Unlit的几何体造成的。"
29. “Mesh uses 32 bit index buffer.” Mesh使用了32位的索引缓冲
30. “Submesh has non-zero base vertex.”  子网格对象有非0的基础顶点， submesh.BaseVertexLocation != 0
31. “The previous instanced draw call has reached its maximum instance count.” 先前的InstanceDrawCall已经达到了Instance的最大数量
32. “Motion Vector rendering doesn‘t support batching.” Motion Vector的渲染不支持Batching
33. “When using late latching, children of an XR late latched GameObject do not use batching.” 使用late latching时，XR late latched GameObject的子级不能合批
34. “Objects have different bounds and bounds instancing is disabled.” 对象具有不同的包裹体，那么包裹体实例化被禁用
35. “SRP: Node have different shaders.” 节点具有不同的着色器
36. “SRP: Node use multi-pass shader” 节点使用了多Pass着色器
37. “SRP: Node use different shader keywords” 节点使用了不同的着色器关键字
38. “SRP: End of the batch flush” Batch Flush结束
39. “SRP: Node is not compatible with SRP batcher” 节点与SRP Batcher不兼容
40. “SRP: Node material requires device state change” 节点材质需要改变渲染设备状态  
41. “SRP: First call from ScriptableRenderLoopJob” ScriptableRenderLoopJob第一次调用
42. “SRP: This material has custom buffer override” 材质有自定义重写的Buffer

# 其他内容

![image-20230319175837322](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303191758424.png)

![image-20230319175846631](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303191758715.png)

