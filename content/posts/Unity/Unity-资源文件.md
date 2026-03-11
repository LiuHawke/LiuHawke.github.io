---
title: "Unity-Asset资源"
date: 2022-02-20T15:37:00+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---
# Unity资源文件夹介绍

![image-20211008233301906](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242137186.png)

## 编辑时

在Asset文件下存在Resources和SteamingAsset文件夹；

### Resources

只读不可修改，打包时直接写死，没有办法通过热更新替换资源；

可以存放任何格式的资源；

通过Resources.Load加载；

一般只存登录UI，congfig配置等不会修改的文件；

```c#
//加载
GameObject go = Resources.Load<GameObject>(path);
Sprite sp = Resouces.Load(path,typeof(sprite)) as sprite;
TextAsset txt = Resources.Load(path) as TextAsset;
```

### SteamingAssets

存放打包后Unity打包后的资源，也就是我说的AssetBundle（ab包）；

可读可写，打包后移动端不能使用File类读取（用过www读取streamingAssetspath数据拷贝到persistentDataPath）；

## 安装后

### Application.dataPath

包含游戏数据文件夹的路径，app程序包安装路径（不常用）；

 1.UnityEditor——**默认Assets**——可读可写 using StreamReader/StreamWriter/异步

 2.Andriod——data/app/xxx.apk——不可读不可写

 3.IOS——Application/.....app/Data——可读不可写（读没意义）

### Application.streamingAssetsPath

数据流换成目录，外部数据文件（二进制，AssetsBundle.csv、数据裸露不加密）；

 1.除了Andriod都可读可写，使用WWW或者System.IO都可以；

 2.Andriod只读，只能用WWW读写——第三方压缩/解压库实现读写（待研究）；

 **3.安装包资源目录，不可修改；**

### Application.presistentDataPath

持久化存储目录，应用更新、覆盖安装都不会清除；

 1.全平台可读可写——stream和file都可以读写；

 **2.热更新资源存放目录；**

### Application.temporaryCachePath

临时换成目录，可读可写（只有ios常用）；

## AssetBundle

资源压缩文件（ab包），非路径，一种压缩文件的格式；

# AssetBundle

## **Asset Bundle：**

**AssetBundle** 是一个存档文件，包含可在运行时加载的特定于平台的资源（模型、纹理、预制件、音频剪辑甚至整个场景）。AssetBundle 可以表达彼此之间的依赖关系；例如 AssetBundle A 中的材质可以引用 AssetBundle B 中的纹理。为了通过网络进行有效传递，可以根据用例要求选用内置算法来压缩 AssetBundle（LZMA 和 LZ4）。

AssetBundle 可用于可下载内容（DLC），减小初始安装大小，加载针对最终用户平台优化的资源，以及减轻运行时内存压力。

## **AssetBundle 中有什么:**

“AssetBundle”可以指两种不同但相关的东西。

首先是磁盘上的实际文件。对于这种情况，我们称之为 AssetBundle 存档，在本文档中简称“存档”。存档可以被视为一个容器，就像文件夹一样，可以在其中包含其他文件。这些附加文件包含两种类型：

序列化文件和资源文件。序列化文件包含分解为各个对象并写入此单个文件的资源。资源文件只是为某些资源（纹理和音频）单独存储的二进制数据块，允许我们有效地在另一个线程上从磁盘加载它们。

其次是通过代码进行交互以便从特定存档加载资源的实际 AssetBundle 对象。此对象包含一个映射，即从已添加到此存档的资源的所有文件路径到按需加载的资源所包含的对象之间的映射。

## 为 AssetBundle 分配资源

要为 AssetBundle 分配指定资源，请按照下列步骤操作：

1.从 Project 视图中选择要为捆绑包分配的资源 

2.在 Inspector 中检查对象

 3.在 Inspector 底部，应该会看到一个用于分配 AssetBundle 和变体的部分：

 4.左侧下拉选单分配 AssetBundle，而右侧下拉选单分配变量 

5.单击左侧下拉选单，其中显示“None”，表示当前注册的 AssetBundle 名称 

6.单击“New…”以创建新的 AssetBundle 

7.输入所需的 AssetBundle 名称。请注意，AssetBundle 名称支持某种类型的文件夹结构，具体取决于您输入的内容。要添加子文件夹，请用“/”分隔文件夹名称。例如：AssetBundle 名称“environment/forest”将在 environment 子文件夹下创建名为 forest 的捆绑包 

8.一旦选择或创建了 AssetBundle 名称，便可以重复此过程在右侧下拉选单中分配或创建变体名称（如果需要）。构建 AssetBundle 不需要变体名称

## 构建 AssetBundle

在 Assets 文件夹中创建一个名为 Editor 的文件夹，并将包含以下内容的脚本放在该文件夹中：

```
using System.IO;
using UnityEditor;

public class CreateAssetBundles
{
    [MenuItem("Assets/Build AssetBundles")]
    static void BuildAllAssetBundles()
    {
        string assetBundleDirectory = "Assets/AssetBundles";
        if (!Directory.Exists(assetBundleDirectory))
        {
            Directory.CreateDirectory(assetBundleDirectory);
        }
        BuildPipeline.BuildAssetBundles(assetBundleDirectory, BuildAssetBundleOptions.None, BuildTarget.StandaloneOSX);
    }
}

```

此脚本将在 Assets 菜单底部创建一个名为“Build AssetBundles”的菜单项，该菜单项将执行与该标签关联的函数中的代码。单击 Build AssetBundles 时，将随构建对话框一起显示一个进度条。此过程将会获取带有 AssetBundle 名称标签的所有资源，并将它们放在 assetBundleDirectory 定义的路径中的文件夹中。

## 将 AssetBundle 上传到场外存储

此步骤对每个用户都是不同的，因此 Unity 不能告诉您应该具体如何操作。如果计划将 AssetBundle 上传到第三方托管站点，请在此步中执行该操作。如果正在严格执行本地开发并打算将所有 AssetBundle 都放在磁盘上，请跳转到下一步。

## 加载 AssetBundle 和资源

打算从本地存储加载的用户可能会对 AssetBundles.LoadFromFile API 感兴趣。该 API 如下所示：

```
public class LoadFromFileExample extends MonoBehaviour {
    function Start() {
        var myLoadedAssetBundle = AssetBundle.LoadFromFile(Path.Combine(Application.streamingAssetsPath, "myassetBundle"));
        if (myLoadedAssetBundle == null) {
            Debug.Log("Failed to load AssetBundle!");
            return;
        }
        var prefab = myLoadedAssetBundle.LoadAsset.<GameObject>("MyObject");
        Instantiate(prefab);
    }
}
```

# 为 AssetBundle 准备资源

使用 AssetBundle 时，可以任意将任何资源分配给所需的任何捆绑包。但是，在设置捆绑包时需要考虑某些策略。以下分组策略旨在用于您认为适合的具体项目。可以根据需要随意混合和搭配这些策略。

## 逻辑实体分组

逻辑实体分组是指根据资源所代表的项目功能部分将资源分配给 AssetBundle。这包括各种不同部分，比如用户界面、角色、环境以及在应用程序整个生命周期中可能经常出现的任何其他内容。

### 示例

- 捆绑用户界面屏幕的所有纹理和布局数据
- 捆绑一个/一组角色的所有模型和动画
- 捆绑在多个关卡之间共享的景物的纹理和模型

逻辑实体分组非常适合于可下载内容 (DLC)，因为通过这种方式将所有内容隔离后，可以对单个实体进行更改，而无需下载其他未更改的资源。

为了能够正确实施此策略，最大诀窍在于，负责为各自捆绑包分配资源的开发人员必须熟悉项目使用每个资源的准确时机和场合。

## 类型分组

根据此策略，可以将相似类型的资源（例如音频轨道或语言本地化文件）分配到单个 AssetBundle。

要构建供多个平台使用的 AssetBundle，类型分组是最佳策略之一。例如，如果音频压缩设置在 Windows 和 Mac 平台上完全相同，则可以自行将所有音频数据打包到 AssetBundle 并重复使用这些捆绑包，而着色器往往使用更多特定于平台的选项进行编译，因此为 Mac 构建的着色器捆绑包可能无法在 Windows 上重复使用。此外，这种方法非常适合让 AssetBundle 与更多 Unity 播放器版本兼容，因为纹理压缩格式和设置的更改频率低于代码脚本或预制件。

## 并发内容分组

并发内容分组是指将需要同时加载和使用的资源捆绑在一起。可以将这些类型的捆绑包用于基于关卡的游戏（其中每个关卡包含完全独特的角色、纹理、音乐等）。有时可能希望确保其中一个 AssetBundle 中的资源与该捆绑包中的其余资源同时使用。依赖于并发内容分组捆绑包中的单个资源会导致加载时间显著增加。您将被迫下载该单个资源的整个捆绑包。

并发内容分组捆绑包最常见的用例是针对基于场景的捆绑包。在此分配策略中，每个场景捆绑包应包含大部分或全部场景依赖项。

请注意，项目绝对可以也应该根据您的需求混用这些策略。对任何给定情形使用最优资源分配策略可以大大提高项目的效率。

例如，一个项目可能决定将不同平台的用户界面 (UI) 元素分组到各自的 Platform-UI 特定捆绑包中，但按关卡/场景对其交互式内容进行分组。

无论遵循何种策略，下面这些额外提示都有助于掌控全局：

- 将频繁更新的对象与很少更改的对象拆分到不同的 AssetBundle 中
- 将可能同时加载的对象分到一组。例如模型及其纹理和动画
- 如果发现多个 AssetBundle 中的多个对象依赖于另一个完全不同的 AssetBundle 中的单个资源，请将依赖项移动到单独的 AssetBundle。如果多个 AssetBundle 引用其他 AssetBundle 中的同一组资源，一种有价值的做法可能是将这些依赖项拉入一个共享 AssetBundle 来减少重复。
- 如果不可能同时加载两组对象（例如标清资源和高清资源），请确保它们位于各自的 AssetBundle 中。
- 如果一个 AssetBundle 中只有不到 50% 的资源经常同时加载，请考虑拆分该捆绑包
- 考虑将多个小型的（少于 5 到 10 个资源）但经常同时加载内容的 AssetBundle 组合在一起
- 如果一组对象只是同一对象的不同版本，请考虑使用 AssetBundle 变体

# 构建 AssetBundle

在有关 [AssetBundle 工作流程](AssetBundles-Workflow.html)的文档中，我们有一个代码示例将三个参数传递给 `BuildPipeline.BuildAssetBundles` 函数。让我们深入了解一下这方面。

_Assets/AssetBundles_：这是 AssetBundle 要输出到的目录。可以将其更改为所需的任何输出目录，只需确保在尝试构建之前文件夹实际存在。

#### BuildAssetBundleOptions

可以指定几个具有各种效果的不同 `BuildAssetBundleOptions`。请参阅关于 [BuildAssetBundleOptions](../ScriptReference/BuildAssetBundleOptions.html) 的脚本 API 参考查看所有这些选项的表格。

虽然可以根据需求变化和需求出现而自由组合 `BuildAssetBundleOptions`，但有三个特定的 `BuildAssetBundleOptions` 可以处理 AssetBundle 压缩：

- `BuildAssetBundleOptions.None`: 
- 此捆绑选项使用 LZMA 格式压缩，它是序列化数据文件的单个压缩 LZMA 流。LZMA 压缩要求在使用之前解压缩整个包。这会导致文件大小尽可能小，但由于解压缩，加载时间会稍长。值得注意的是，在使用此 BuildAssetBundleOptions 时，为了使用包中的任何资产，必须首先解压缩整个包。
  解压缩包后，它将使用 LZ4 压缩在磁盘上重新压缩，这不需要在使用包中的资产之前解压缩整个包。当捆绑包包含资产时最好使用此方法，以便使用捆绑包中的一个资产意味着所有资产都将被加载。打包角色或场景的所有资产是可能使用此功能的捆绑包的一些示例。
  由于文件较小，建议仅在从异地主机初始下载 AssetBundle 时使用 LZMA 压缩。[通过UnityWebRequestAssetBundle](../ScriptReference/Networking.UnityWebRequestAssetBundle.html)加载的 LZMA 压缩资产包会自动重新压缩为 LZ4 压缩并缓存在本地文件系统上。如果您通过其他方式下载并存储该捆绑包，您可以使用[AssetBundle.RecompressAssetBundleAsync](../ScriptReference/AssetBundle.RecompressAssetBundleAsync.html) API。
- `BuildAssetBundleOptions.UncompressedAssetBundle`：此捆绑包选项采用使数据完全未压缩的方式构建捆绑包。未压缩的缺点是文件下载大小增大。但是，下载后的加载时间会快得多。
- `BuildAssetBundleOptions.ChunkBasedCompression`：此捆绑包选项使用称为 LZ4 的压缩方法，因此压缩文件大小比 LZMA 更大，但不像 LZMA 那样需要解压缩整个包才能使用捆绑包。LZ4 使用基于块的算法，允许按段或“块”加载 AssetBundle。解压缩单个块即可使用包含的资源，即使 AssetBundle 的其他块未解压缩也不影响。

使用 `ChunkBasedCompression` 时的加载时间与未压缩捆绑包大致相当，额外的优势是减小了占用的磁盘大小。

#### BuildTarget

`BuildTarget.Standalone`：这里我们告诉构建管线，我们要将这些 AssetBundle 用于哪些目标平台。可以在关于 [BuildTarget](../ScriptReference/BuildTarget.html) 的脚本 API 参考中找到可用显式构建目标的列表。但是，如果不想在构建目标中进行硬编码，请充分利用 `EditorUserBuildSettings.activeBuildTarget`，它将自动找到当前设置的目标构建平台，并根据该目标构建 AssetBundle。

一旦正确设置构建脚本，最后便可以开始构建资源包了。如果是按照上面的脚本示例进行的操作，请单击 **Assets** > **Build AssetBundles** 以开始该过程。

现在已经成功构建了 AssetBundle，您可能会注意到 AssetBundles 目录包含的文件数量超出了最初的预期。确切地说，是多出了 2*(n+1) 个文件。让我们花点时间详细了解一下 `BuildPipeline.BuildAssetBundles` 产生的结果。

对于在编辑器中指定的每个 AssetBundle，可以看到一个具有 AssetBundle 名称+“.manifest”的文件。

随后会有一个额外捆绑包和清单的名称不同于先前创建的任何 AssetBundle。相反，此包以其所在的目录（构建 AssetBundle 的目录）命名。这就是清单捆绑包。我们以后会对此进行详细讨论并介绍使用方法。

#### AssetBundle 文件

这是缺少 .manifest 扩展名的文件，其中包含在运行时为了加载资源而需要加载的内容。

AssetBundle 文件是一个存档，在内部包含多个文件。此存档的结构根据它是 AssetBundle 还是场景 AssetBundle 可能会略有不同。以下是普通 AssetBundle 的结构：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242142639.png)

场景 AssetBundle 与普通 AssetBundle 的不同之处在于，它针对场景及其内容的串流加载进行了优化。

#### 清单文件

对于生成的每个捆绑包（包括附加的清单捆绑包），都会生成关联的清单文件。清单文件可以使用任何文本编辑器打开，并包含诸如循环冗余校验 (CRC) 数据和捆绑包的依赖性数据之类的信息。对于普通 AssetBundle，它们的清单文件将如下所示：

```c#
ManifestFileVersion: 0
CRC: 2422268106
Hashes:
  AssetFileHash:
    serializedVersion: 2
    Hash: 8b6db55a2344f068cf8a9be0a662ba15
  TypeTreeHash:
    serializedVersion: 2
    Hash: 37ad974993dbaa77485dd2a0c38f347a
HashAppended: 0
ClassTypes:
- Class: 91
  Script: {instanceID: 0}
Assets:
  Asset_0: Assets/Mecanim/StateMachine.controller
Dependencies: {}
```

其中显示了包含的资源、依赖项和其他信息。

生成的清单捆绑包将有一个清单，但看起来更可能如下所示：

```c#
ManifestFileVersion: 0
AssetBundleManifest:
  AssetBundleInfos:
    Info_0:
      Name: scene1assetbundle
      Dependencies: {}
```

这将显示 AssetBundle 之间的关系以及它们的依赖项。就目前而言，只需要了解这个捆绑包中包含 AssetBundleManifest 对象，这对于确定在运行时加载哪些捆绑包依赖项非常有用。

# AssetBundle 依赖项

如果一个或多个 `UnityEngine.Objects` 包含对位于另一个捆绑包中的 `UnityEngine.Object` 的引用，则 AssetBundle 可以变为依赖于其他 AssetBundle。如果 `UnityEngine.Object` 包含对任何 AssetBundle 中未包含的 `UnityEngine.Object` 的引用，则不会发生依赖关系。在这种情况下，在构建 AssetBundle 时，捆绑包所依赖的对象的副本将复制到捆绑包中。如果多个捆绑包中的多个对象包含对未分配给捆绑包的同一对象的引用，则每个对该对象具有依赖关系的捆绑包将创建其自己的对象副本并将其打包到构建的 AssetBundle 中。

如果 AssetBundle 中包含依赖项，则在加载尝试实例化的对象之前，务必加载包含这些依赖项的捆绑包。Unity 不会尝试自动加载依赖项。

参考以下示例，__Bundle 1__ 中的材质引用了 **Bundle 2** 中的纹理：

在此示例中，在从 **Bundle 1** 加载材质之前，需要将 **Bundle 2** 加载到内存中。加载 **Bundle 1** 和 **Bundle 2** 的顺序无关紧要，重要的是在从 **Bundle 1** 加载材质之前应加载 **Bundle 2**。在下一部分，我们将讨论如何使用我们在上一部分介绍的 `AssetBundleManifest` 对象在运行时确定并加载依赖项。

# 使用 AssetBundle

可以使用四种不同的 API 来加载 AssetBundle。它们的行为根据加载捆绑包的平台和构建 AssetBundle 时使用的压缩方法（未压缩、LZMA 和 LZ4）而有所不同。

我们必须使用的四个 API 是：

- [AssetBundle.LoadFromMemoryAsync](https://docs.unity3d.com/ScriptReference/AssetBundle.LoadFromMemoryAsync.html?_ga=1.226802969.563709772.1479226228)
- [AssetBundle.LoadFromFile](https://docs.unity3d.com/ScriptReference/AssetBundle.LoadFromFile.html?_ga=1.259297550.563709772.1479226228)
- [WWW.LoadfromCacheOrDownload](https://docs.unity3d.com/ScriptReference/WWW.LoadFromCacheOrDownload.html?_ga=1.226802969.563709772.1479226228)
- [UnityWebRequest](https://docs.unity3d.com/ScriptReference/Networking.UnityWebRequest.html?_ga=1.259297550.563709772.1479226228) 的 [DownloadHandlerAssetBundle ](https://docs.unity3d.com/ScriptReference/Networking.DownloadHandlerAssetBundle.html?_ga=1.264500235.563709772.1479226228)（Unity 5.3 或更高版本）

## AssetBundle.LoadFromMemoryAsync

[AssetBundle.LoadFromMemoryAsync](../ScriptReference/AssetBundle.LoadFromMemoryAsync.html)

此函数采用包含 AssetBundle 数据的字节数组。也可以根据需要传递 CRC 值。如果捆绑包采用的是 LZMA 压缩方式，将在加载时解压缩 AssetBundle。LZ4 压缩包则会以压缩状态加载。

以下是如何使用此方法的一个示例：

```c#
using UnityEngine;
using System.Collections;
using System.IO;

public class Example : MonoBehaviour
{
 		IEnumerator Start()
    {
        string path = Application.streamingAssetsPath + "/AssetBundles/OSX/wall";
    		AssetBundleCreateRequest createRequest = AssetBundle.LoadFromMemoryAsync(File.ReadAllBytes(path));
        Debug.Log("Failed to load AssetBundle!" + createRequest);
        yield return createRequest;

        AssetBundle bundle = createRequest.assetBundle;
        var prefab = bundle.LoadAsset<GameObject>("Cube");
        Instantiate(prefab);
     }
}
```

但是，这不是实现 LoadFromMemoryAsync 的唯一策略。File.ReadAllBytes(path) 可以替换为获得字节数组的任何所需过程。

## AssetBundle.LoadFromFile

[AssetBundle.LoadFromFile](../ScriptReference/AssetBundle.LoadFromFile.html)

从本地存储中加载未压缩的捆绑包时，此 API 非常高效。如果捆绑包未压缩或采用了数据块 (LZ4) 压缩方式，LoadFromFile 将直接从磁盘加载捆绑包。使用此方法加载完全压缩的 (LZMA) 捆绑包将首先解压缩捆绑包，然后再将其加载到内存中。

如何使用 `LoadFromFile` 的一个示例：

```c#
public class LoadFromFileExample extends MonoBehaviour {
    function Start() {
        var myLoadedAssetBundle = AssetBundle.LoadFromFile(Path.Combine(Application.streamingAssetsPath, "myassetBundle"));
        if (myLoadedAssetBundle == null) {
            Debug.Log("Failed to load AssetBundle!");
            return;
        }
        var prefab = myLoadedAssetBundle.LoadAsset.<GameObject>("MyObject");
        Instantiate(prefab);
    }
}
```

注意：在使用 Unity 5.3 或更早版本的 Android 设备上，尝试从流媒体资源 (Streaming Assets) 路径加载 AssetBundle 时，此 API 将失败。这是因为该路径的内容将驻留在压缩的 .jar 文件中。Unity 5.4 和更高版本则可以将此 API 调用与流媒体资源一起使用。

## WWW.LoadFromCacheOrDownload

[WWW.LoadFromCacheOrDownload](../ScriptReference/WWW.LoadFromCacheOrDownload.html)

**即将弃用（使用 UnityWebRequest）**

此 API 对于从远程服务器下载 AssetBundle 或加载本地 AssetBundle 非常有用。这是一个陈旧且不太理想的 UnityWebRequest API 版本。

从远程位置加载 AssetBundle 将自动缓存 AssetBundle。如果 AssetBundle 被压缩，则将启动工作线程来解压缩捆绑包并将其写入缓存。一旦捆绑包被解压缩并缓存，它就会像 AssetBundle.LoadFromFile 一样加载。

如何使用 `LoadFromCacheOrDownload` 的一个示例：

```C#
using UnityEngine;
using System.Collections;

public class LoadFromCacheOrDownloadExample : MonoBehaviour
{
    IEnumerator Start ()
    {
           while (!Caching.ready)
            yield return null;

        var www = WWW.LoadFromCacheOrDownload("file://" + path, 5);
        Debug.Log(path);
        yield return www;
        if (!string.IsNullOrEmpty(www.error))
        {
            Debug.Log(www.error);
            yield break;
        }
        AssetBundle bundle = www.assetBundle;
    }
}
```

由于在 WWW 对象中缓存 AssetBundle 字节所需的内存开销，建议所有使用 WWW.LoadFromCacheOrDownload 的开发人员都应该确保自己的 AssetBundle 保持较小的大小 - 最多只有几兆字节。此外，还建议在有限内存平台（如移动设备）上运行的开发人员确保其代码一次只下载一个 AssetBundle，以此避免内存峰值。

如果缓存文件夹没有任何空间来缓存其他文件，LoadFromCacheOrDownload 将以迭代方式从缓存中删除最近最少使用的 AssetBundle，直到有足够的空间来存储新的 AssetBundle。如果无法腾出空间（因为硬盘已满，或者缓存中的所有文件当前都处于使用状态），LoadFromCacheOrDownload() 将不会使用缓存，而将文件流式传输到内存中

为了强制执行 LoadFromCacheOrDownload，需要更改版本参数（第二个参数）。仅当传递给函数的版本与当前缓存的 AssetBundle 的版本匹配，才会从缓存加载 AssetBundle。

## UnityWebRequest

[UnityWebRequest](../ScriptReference/Networking.UnityWebRequest.GetAssetBundle.html)

UnityWebRequest 有一个特定 API 调用来处理 AssetBundle。首先，需要使用 `UnityWebRequest.GetAssetBundle` 来创建 Web 请求。返回请求后，请将请求对象传递给 `DownloadHandlerAssetBundle.GetContent(UnityWebRequest)`。`GetContent` 调用将返回 AssetBundle 对象。

下载捆绑包后，还可以在 [DownloadHandlerAssetBundle](../ScriptReference/Networking.DownloadHandlerAssetBundle.html) 类上使用 `assetBundle` 属性，从而以 `AssetBundle.LoadFromFile` 的效率加载 AssetBundle。

以下示例说明了如何加载包含两个游戏对象的 AssetBundle 并实例化这些游戏对象。要开始这个过程，我们只需要调用 `StartCoroutine(InstantiateObject())`;

```C#
IEnumerator InstantiateObject()

    {
        UnityWebRequest request = UnityWebRequestAssetBundle.GetAssetBundle("file://" + path, 0);
        yield return request.SendWebRequest();
        AssetBundle bundle = DownloadHandlerAssetBundle.GetContent(request);
        GameObject cube = bundle.LoadAsset<GameObject>("Cube");

        Instantiate(cube);
    }
```

使用 UnityWebRequest 的优点在于，它允许开发人员以更灵活的方式处理下载的数据，并可能消除不必要的内存使用。这是比 UnityEngine.WWW 类更新和更优的 API。

#### 从 AssetBundle 加载资源

现在已经成功下载 AssetBundle，因此是时候最终加载一些资源了。

通用代码片段：

```C#
T objectFromBundle = bundleObject.LoadAsset<T>(assetName);
```

T 是尝试加载的资源类型。

决定如何加载资源时有几个选项。我们有 `LoadAsset`、`LoadAllAssets` 及其各自的异步对应选项 `LoadAssetAsync` 和 `LoadAllAssetsAsync`。

同步从 AssetBundle 加载资源的方法如下：

加载单个游戏对象：

```C#
GameObject gameObject = loadedAssetBundle.LoadAsset<GameObject>(assetName);
```

加载所有资源：

```
Unity.Object[] objectArray = loadedAssetBundle.LoadAllAssets();
```

现在，在前面显示的方法返回要加载的对象类型或对象数组的情况下，异步方法返回 [AssetBundleRequest](../ScriptReference/AssetBundleRequest.html)。在访问资源之前，需要等待此操作完成。加载资源：

```C#
AssetBundleRequest request = loadedAssetBundleObject.LoadAssetAsync<GameObject>(assetName);
yield return request;
var loadedAsset = request.asset;
```

以及

```C#
AssetBundleRequest request = loadedAssetBundle.LoadAllAssetsAsync();
yield return request;
var loadedAssets = request.allAssets;
```

加载资源后，就可以开始了！可以像使用 Unity 中的任何对象一样使用加载的对象。

#### 加载 AssetBundle 清单

加载 AssetBundle 清单可能非常有用。特别是在处理 AssetBundle 依赖关系时。

要获得可用的 [AssetBundleManifest](../ScriptReference/AssetBundleManifest.html) 对象，需要加载另外的 AssetBundle（与其所在的文件夹名称相同的那个）并从中加载 AssetBundleManifest 类型的对象。

加载清单本身的操作方法与 AssetBundle 中的任何其他资源完全相同：

```C#
AssetBundle assetBundle = AssetBundle.LoadFromFile(manifestFilePath);
AssetBundleManifest manifest = assetBundle.LoadAsset<AssetBundleManifest>("AssetBundleManifest");
```

现在，可以通过上面示例中的清单对象访问 `AssetBundleManifest` API 调用。从这里，可以使用清单获取所构建的 AssetBundle 的相关信息。此信息包括 AssetBundle 的依赖项数据、哈希数据和变体数据。

别忘了在前面的部分中，我们讨论过 AssetBundle 依赖项以及如果一个捆绑包对另一个捆绑包有依赖性，那么在从原始捆绑包加载任何资源之前，需要加载哪些捆绑包？清单对象可以动态地查找加载依赖项。假设我们想要为名为“assetBundle”的 AssetBundle 加载所有依赖项。

```C#
AssetBundle assetBundle = AssetBundle.LoadFromFile(manifestFilePath);
AssetBundleManifest manifest = assetBundle.LoadAsset<AssetBundleManifest>("AssetBundleManifest");
string[] dependencies = manifest.GetAllDependencies("assetBundle"); //传递想要依赖项的捆绑包的名称。
foreach(string dependency in dependencies)
{
    AssetBundle.LoadFromFile(Path.Combine(assetBundlePath, dependency));
}
```

现在已经加载 AssetBundle、AssetBundle 依赖项和资源，因此是时候讨论如何管理所有这些已加载的 AssetBundle 了。

## 管理已加载的 AssetBundle

从活动场景中删除对象时，Unity 不会自动卸载对象。资源清理在特定时间触发，也可以手动触发。

了解何时加载和卸载 AssetBundle 非常重要。不正确地卸载 AssetBundle 会导致在内存中复制对象或其他不良情况，例如缺少纹理。

关于 AssetBundle 管理最重要的事情就是何时调用

[AssetBundle.Unload(bool)](../ScriptReference/AssetBundle.Unload.html); 以及应该将 true 还是 false 传递给函数调用。Unload 是一个非静态函数，可用于卸载 AssetBundle。此 API 会卸载正在调用的 AssetBundle 的标头信息。该参数指示是否还要卸载通过此 AssetBundle 实例化的所有对象。

`AssetBundle.Unload(true)` 卸载从 AssetBundle 加载的所有游戏对象（及其依赖项）。这不包括复制的游戏对象（例如实例化的游戏对象），因为它们不再属于 AssetBundle。发生这种情况时，从该 AssetBundle 加载的纹理（并且仍然属于它）会从场景中的游戏对象消失，因此 Unity 将它们视为缺少纹理。

假设材质 M 是从 AssetBundle AB 加载的，如下所示。

如果调用 AB.Unload(true)，活动场景中的任何 M 实例也将被卸载并销毁。

如果改作调用 AB.Unload(false)，那么将会中断 M 和 AB 当前实例的链接关系。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242143748.png)

如果稍后再次加载 AB 并且调用 AB.LoadAsset()，则 Unity 不会将现有 M 副本重新链接到新加载的材质。而是将加载 M 的两个副本。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242143614.png)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242143747.png)

通常，使用 `AssetBundle.Unload(false)` 不会带来理想情况。大多数项目应该使用 `AssetBundle.Unload(true)` 来防止在内存中复制对象。

大多数项目应该使用 `AssetBundle.Unload(true)` 并采用一种方法来确保对象不会重复。两种常用方法是：

- 在应用程序生命周期中具有明确定义的卸载瞬态 AssetBundle 的时间点，例如在关卡之间或在加载屏幕期间。
- 维护单个对象的引用计数，仅当未使用所有组成对象时才卸载 AssetBundle。这允许应用程序卸载和重新加载单个对象，而无需复制内存。

如果应用程序必须使用 `AssetBundle.Unload(false)`，则只能以两种方式卸载单个对象：

- 在场景和代码中消除对不需要的对象的所有引用。完成此操作后，调用 [Resources.UnloadUnusedAssets](../ScriptReference/Resources.UnloadUnusedAssets.html)。
- 以非附加方式加载场景。这样会销毁当前场景中的所有对象并自动调用 [Resources.UnloadUnusedAssets](../ScriptReference/Resources.UnloadUnusedAssets.html)。

# 加载依赖资源

## 通过 API 获取 AssetBundle 依赖信息

- GetAllAssetBundles()
  - 描述：获取所有的 AssetBundle 的名字
- GetAllDependencies(bundleName)
  - 描述：获取目标 AssetBundle 的所有依赖 AssetBundle 名字 (递归遍历)
- GetDirectDependencies(bundleName)
  - 获取目标 AssetBundle 的直接的依赖 AssetBundle 名字(只遍

代码如下：

```C#
using System.Linq;
using UnityEngine;

namespace LYFramework.Tests
{
	public class AssetBundleManifestExample : MonoBehaviour
	{
		void Start()
		{
			var windowsBundlePath = AssetBundleUtil.FullPathForAssetBundleName("Windows");

			var windowsBundle = AssetBundle.LoadFromFile(windowsBundlePath);

			var manifest = windowsBundle.LoadAsset<AssetBundleManifest>("AssetBundleManifest");

			manifest.GetAllAssetBundles()
				.ToList()
				.ForEach(Debug.Log);

			manifest.GetAllDependencies("coin_get_prefab")
				.ToList()
				.ForEach(dependBundle => Debug.LogFormat("coin_get_prefab depends:{0}", dependBundle));

			windowsBundle.Unload(true);
		}
	}
}
```

输出的结果如下。

```C#
coin_get
coin_get_prefab
coin_get_prefab depends:coin_get
```
