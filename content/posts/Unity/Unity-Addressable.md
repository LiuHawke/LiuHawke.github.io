---
title: "Unity-Addressable"
date: 2022-04-20T10:30:27+08:00

tags:
  - Unity

categories:
  - Unity
---

## 配置

### Profile 概述窗口配置

![image-20220512235308487](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144110.png)

BuildTarget：构建目标，可以在这里设置是哪个平台，默认是你激活哪个平台就是哪个平台
LocalBuildPath：本地构建路径，默认在项目的Library库文件夹中
LocalLoadPath：本地加载路径，在哪里加载本地已有的资源
RemoteBuildPath：远程构建路径
RemoteLoadPath：远程加载路径，在哪里下载远程内容和目录

注意：
1.一般情况下，不要去修改本地构建和加载路径默认值
2.当我们针对不同平台远程分发内容时，通过多个配置文件最方便。如果你想要最终的发布包包含所有内容，那么一个默认配置就够了

### AddressableAssetSettings 可寻址资源数据设置

![image-20220512235703317](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144015.png)

概述配置
Profile In Use：可以在这选择使用的是哪一套配置文件
Manage Profiles：点击它会打开管理配置文件窗口

![image-20220512235744419](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144370.png)

Diagnostics:诊断
Send Profiler Events：启用分析器事件，启用它后我们可以在Event Viewer窗口查看Addressable相关信息
Log Runtime Exceptions：记录运行时加载相关的异常

![image-20220512235758863](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144647.png)

目录相关设置，将资源的地址映射到其物理位置
Player Version Override：重写用于制定远程目录名称的时间戳
如果不设置默认使用时间戳作为远程目录命名

Compress Local Catalog：在压缩的AssetBundle文件中生成目录。可以压缩大小，但是会增加生成和加载的时间

Optimize Catalog Size：通过为内部ID创建查找表来减小目录的大小。会增加加载目录所需的时间

![image-20220512235829322](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144208.png)

Content Update：内容更新

Disable Catalog Update on Startup：当可寻址系统在运行时初始化时，禁用自动检查更新的远程目录。您可以手动检查更新的目录。

Content State Build Path：在何处生成由默认生成脚本生成的内容状态文件。

Build Remote Catalog：构建远程目录
勾选后会出现新选项

![image-20220512235843681](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144960.png)

Build & Load Paths：
在何处生成和加载远程目录。从列表中选择一个配置文件路径，如果要分别设置生成路径和加载路径，请选择<custom>。
仅在启用生成远程目录时可见。

Build Path：远程构建路径，在何处构建远程目录。通常，应该使用RemoteBuildPath配置文件变量。
仅当将生成和加载路径设置为<custom>时显示。

Load Path：远程加载路径，用于访问远程目录的URL。通常，应该使用RemoteLoadPath配置文件变量。
仅当将生成和加载路径设置为<custom>时显示。

Path Preview：路径预览

![image-20220512235907623](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144880.png)

Custom Certificeate handle：用于自定义证书处理的类。该列表包含项目中扩展UnityEngine的所有类。网络。证书管理员。

Max Concurrent Web Requests：系统对超过此限制的任何请求进行队列处理

Catalog Download Timeout：等待目录文件下载的时间为多少秒。

![image-20220513000829808](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144501.png)

生成构建相关设置

Ignore Invalid/Unsupported Files in Build：忽略生成中无效不受支持的文件，如果启用，Addressable生成脚本将排除无效或不受支持的文件，而不是中止生成

Unique Bundle IDs：唯一约束ID，是否为每个构建中的包生成一个唯一的名称

Contiguous Bundles：连续捆绑，生成更高效的捆绑包布局。如果您有Addressables 1.12.1或更早版本生成的捆绑包，请禁用此选项以最小化捆绑包更改

Non-Recursive Dependency Calculation：非递归依赖计算，不使用递归计算依赖项

Shader Bundle Naming Prefix：着色器包命名前缀

MonoScript Bundle Naming Prefix：Mono脚本包命名前缀

Strip Unity Version From AssetBundles：从AssetBUndles中剥离Unity版本，决定是否从包头中删除版本信息

Disable Visible Sub Asset Representations：禁用可见子资源，如果您不直接使用子对象(Sprite、子网格等)，则启用此选项可以提高构建时间

![image-20220513000001837](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144022.png)

构建和编辑器模式脚本
在编辑器中进入播放模式时，选择Addressable系统如何加载资产
这些脚本处理默认的构建进程，并提供不同的方式在编辑模式下访问数据
我们可以在AddressableAssetData/DataBuilders文件夹中找到这些脚本
如果要为他们自定义脚本，那么对应的脚本需要时BuildScriptBase的子类，并且继承IDataBuilder
Use Asset Database：使用资源数据库
Simulate Groups：模拟组
Use Existing Build：使用现有版本
Default Build Scripts:默认生成脚本

![image-20220513000018046](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144527.png)

资源组模板

Packed Asset：打包资源
定义可用于创建新组的模板列表
创建新模板时，必须先将其添加到此列表中，然后才能使用它

Addressables包包含一个模板
其中包含默认构建脚本使用的模式。您可以在AddressableAssetData/AssetGroupTemplates文件夹中找到该模板。

![image-20220513000041149](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144161.png)

初始化对象列表
配置初始化对象

可以在Project右键Create>Addressables>Initialization(初始化)>Cache Initialization Setting（缓存初始化设置）

![image-20220513000924257](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144224.png)

Compress Bundles：是否压缩包
Cache Directory Override：缓存目录覆盖
[Obsolete]Expiration Delay：过期延迟（过时了）
Limit Cache Size：限制缓存的大小
Maximum Cache Size：最大缓存大小

### Packed Assets 打包资源数据配置

![image-20220513001009620](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242144296.png)

Build & Load Paths：配置文件路径对，定义可寻址构建系统在哪个路径为此组创建内容，以及可寻址系统在运行时在何处加载这些内容

Build Path：打包路径
LocalBuildPath-本地路径
RemoteBuildPath-远程路径

Load Path：加载路径
LocalLoadPath-本地路径
RemoteLoadPath-远程路径

Path Preview：路径预览

![image-20220513001032183](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242145809.png)

Asset Bundle Compression：AB包的压缩方式，默认为LZ4，它通常是最有效的选项，建议使用

Uncompressed：不压缩，包较大，不推荐

LZ4：压缩，相对LZMA大一点点，建议使用，用什么解压什么，内存占用低

LZMA：压缩最小，解压慢，用一个资源要解压所有

Include In Build：包含在构建中，是否在内容生成中包含此组中的资源。如果取消勾选，那么在选择打包时，不会打包该组内容

Force Unique Provider：强制唯一提供者，Addressable是否对此组使用资源提供程序类的唯一实例。如果您有此组中的资产类型的自定义提供程序实现，并且这些提供程序的实例不能在组之间共享，则启用此选项。

Use Asset Bundle Cache：使用AB包缓存，是否缓存远程分发的包

Asset Bundle CRC：是否在加载前验证AB包的完整性

Disabled：永远不检查完整性
Enabled，Including Cached：检查完整性，包括缓存也检查
Enabled，Excluding Cached：检查完整性，但是不检查缓存的包

Use UnityWebRequest for Local Asset Bundle：
加载AB包时，使用UnityWebRequestAssetBundle.GetAssetBundle而不是AssetBundle.LoadFromFileAsync

Request Timeout：下载远程包时超时的间隔时间

Use Http Chunked Transfer：下载包时是否使用HTTP/1.1块传输编码方法。
在2019.3+中被废弃和忽略

Http Redirect Limit：下载包时允许重定向的次数。无限制设置为-1

Retry Count：重试失败下载的次数

Include Addresses in Catalog：是否将地址字符串包括在目录中。如果不使用地址字符串在组中加载资产，则可以通过不包括它们来减小目录的大小。

Include GUIDs in Catalog：是否在目录中包含GUID字符串。您必须包含guid字符串才能使用资产参考。如果不使用AssetReferences或GUID字符串在组中加载资产，则可以通过不包括它们来减小目录的大小。

Include Labels in Catalog：是否在目录中包含标签字符串。如果不使用标签在组中加载资产，则可以通过不包括这些资产来缩小目录的大小。

Internal Asset Naming Mode：如何在内部命名目录中的资源

Full Path：全路径
FileName：文件名
GUID：资源的Guid字符串
Dynamic：Addressables根据组中的资源选择最小的内部名称

Internal Bundle Id Mode	：确定如何构造资产绑定的内部ID。例如，当您设置群GUID选项，Addressables通过将组名与包guid字符串组合来创建包ID。

Group Guid：组的Guid
Group Guid Project Id Hash：组的ID和工程ID的哈希
Group Guid Project Id Entries Hash：组的ID和工程ID项的哈希

Cache Clear Behavior：确定安装的应用程序何时从缓存中清除AB包

Clear When Space Is Needed In Cache：在缓存中需要空间时清除
Clear When When new Version Loaded：加载新版本时清楚

Bundle Mode：打包模式，如何将此组中的资产打包到包中

Pack Together：创建包含所有资产的单个包
Pack Separately：为组中的每个主要资产创建一个包。如精灵图片中的精灵图片被包装在一起。添加到组中的文件夹中的资产也打包在一起
Pack Together by Label：为共享相同标签组合的资产创建一个包

Bundle Naming Mode：如何构造AB包的文件名

Filename：文件名
Append Hash to Filename：将哈希附加到文件名
Use Hash of AssetBundle：使用AB包的哈希
Use Hash of FileName：使用文件名的哈希

Asset Load Mode：资源加载模式

Requested Asset And Dependencies：请求的资源和依赖项
All Packed Assets And Dependencies：所有包中的资源和依赖项

Asset Provider：资源提供者
定义提供程序类Addressable用于从该组生成的AssetBundles加载资产。将此选项设置为Bundles供应商的资产除非您有一个自定义提供程序实现来从资产绑定中提供资产。

Asset Bundle Provider：AB包提供器
定义由该组生成的提供程序类Addressable用于加载AssetBundles。将此选项设置为资产束提供者除非您有一个自定义提供程序实现来提供资产绑定。

![image-20220513001612615](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242145042.png)

内容更新限制

Add Schema（添加模式）
你可以将任意数量的架构模式分配给一个组

Content Packing Loading：内容打包加载相关
Content Update Restriction：内容更新限制
Resources and Built In Scenes：在内置数据中显示哪些类型的内置资源，可以选择是否显示 资源和内置场景

我们甚至可以通过继承AddressableAssetGroupSchema
定义自己的架构模式

![image-20220513001644897](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242145113.png)

Can Change Post Release：可以改变发行后内容，该模式不移动任何资源，如果包中的任何资源发生了更改，则重新构建整个包

Cannot Change Post Release：无法改变发布后内容，如果包中任何资源已经改变，则[检查内容更新限制]工具会将其移动到为更新创建的新组中。在进行更新构建时，从这个新组创建的AssetBundles中的资产将覆盖现有包中的版本。

## 异步资源加载方式

```c++
 AsyncOperationHandle<GameObject> handle;
    // Start is called before the first frame update
    void Start()
    {
        #region 知识点一 回顾目前动态异步加载的使用方式
        //handle = Addressables.LoadAssetAsync<GameObject>("Cube");
        //通过事件监听的方式 结束时使用资源
        //handle.Completed += (obj) =>
        //{
        //    if (handle.Status == AsyncOperationStatus.Succeeded)
        //    {
        //        print("事件创建对象");
        //        Instantiate(obj.Result);
        //    }

        //};
        #endregion

        #region 知识点二 3种使用异步加载资源的方式
        //1.事件监听（目前学习过的）
        //2.协同程序
        //3.异步函数（async和await ）
        #endregion

        #region 知识点三 通过协程使用异步加载
        //StartCoroutine(LoadAsset());
        #endregion

        #region 知识点四 通过异步函数async和await加载
        //注意：WebGL平台不支持异步函数语法

        //单任务等待
        Load();
        //多任务等待
        #endregion
    }

    IEnumerator LoadAsset()
    {
        handle = Addressables.LoadAssetAsync<GameObject>("Cube");
        //一定是没有加载成功 再去 yield return
        if(!handle.IsDone)
            yield return handle;

        //加载成功 那么久可以使用了
        if (handle.Status == AsyncOperationStatus.Succeeded)
        {
            print("协同程序创建对象");
            Instantiate(handle.Result);
        }
        else
            Addressables.Release(handle);
    }

    async void Load()
    {
        handle = Addressables.LoadAssetAsync<GameObject>("Cube");

        AsyncOperationHandle<GameObject> handle2 = Addressables.LoadAssetAsync<GameObject>("Sphere");

        //单任务等待
        //await handle.Task;

        //多任务等待
        await Task.WhenAll(handle.Task, handle2.Task);

        print("异步函数的形式加载的资源");
        Instantiate(handle.Result);
        Instantiate(handle2.Result);
    }
```



## Addressables资源管理器

```c++
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;

//可寻址资源 信息
public class AddressablesInfo
{
    //记录 异步操作句柄
    public AsyncOperationHandle handle;
    //记录 引用计数
    public uint count;

    public AddressablesInfo(AsyncOperationHandle handle)
    {
        this.handle = handle;
        count += 1;
    }
}

public class AddressablesMgr
{
    private static AddressablesMgr instance = new AddressablesMgr();
    public static AddressablesMgr Instance => instance;

    //有一个容器 帮助我们存储 异步加载的返回值
    public Dictionary<string, AddressablesInfo> resDic = new Dictionary<string, AddressablesInfo>();

    private AddressablesMgr() { }

    //异步加载资源的方法
    public void LoadAssetAsync<T>(string name, Action<AsyncOperationHandle<T>> callBack)
    {
        //由于存在同名 不同类型资源的区分加载
        //所以我们通过名字和类型拼接作为 key
        string keyName = name + "_" + typeof(T).Name;
        AsyncOperationHandle<T> handle;
        //如果已经加载过该资源
        if (resDic.ContainsKey(keyName))
        {
            //获取异步加载返回的操作内容
            handle = resDic[keyName].handle.Convert<T>();
            //要使用资源了 那么引用计数+1
            resDic[keyName].count += 1;
            //判断 这个异步加载是否结束
            if(handle.IsDone)
            {
                //如果成功 就不需要异步了 直接相当于同步调用了 这个委托函数 传入对应的返回值
                callBack(handle);
            }
            //还没有加载完成
            else
            {
                //如果这个时候 还没有异步加载完成 那么我们只需要 告诉它 完成时做什么就行了
                handle.Completed += (obj) => {
                    if (obj.Status == AsyncOperationStatus.Succeeded)
                        callBack(obj);
                };
            }
            return;
        }
        
        //如果没有加载过该资源
        //直接进行异步加载 并且记录
        handle = Addressables.LoadAssetAsync<T>(name);
        handle.Completed += (obj)=> {
            if (obj.Status == AsyncOperationStatus.Succeeded)
                callBack(obj);
            else
            {
                Debug.LogWarning(keyName + "资源加载失败");
                if(resDic.ContainsKey(keyName))
                    resDic.Remove(keyName);
            }
        };
        AddressablesInfo info = new AddressablesInfo(handle);
        resDic.Add(keyName, info);
    }

    //释放资源的方法 
    public void Release<T>(string name)
    {
        //由于存在同名 不同类型资源的区分加载
        //所以我们通过名字和类型拼接作为 key
        string keyName = name + "_" + typeof(T).Name;
        if(resDic.ContainsKey(keyName))
        {
            //释放时 引用计数-1
            resDic[keyName].count -= 1;
            //如果引用计数为0  才真正的释放
            if(resDic[keyName].count == 0)
            {
                //取出对象 移除资源 并且从字典里面移除
                AsyncOperationHandle<T> handle = resDic[keyName].handle.Convert<T>();
                Addressables.Release(handle);
                resDic.Remove(keyName);
            }
        }
    }

    //异步加载多个资源 或者 加载指定资源
    public void LoadAssetAsync<T>(Addressables.MergeMode mode, Action<T> callBack, params string[] keys)
    {
        //1.构建一个keyName  之后用于存入到字典中
        List<string> list = new List<string>(keys);
        string keyName = "";
        foreach (string key in list)
            keyName += key + "_";
        keyName += typeof(T).Name;
        //2.判断是否存在已经加载过的内容 
        //存在做什么
        AsyncOperationHandle<IList<T>> handle;
        if (resDic.ContainsKey(keyName))
        {
            handle = resDic[keyName].handle.Convert<IList<T>>();
            //要使用资源了 那么引用计数+1
            resDic[keyName].count += 1;
            //异步加载是否结束
            if (handle.IsDone)
            {
                foreach (T item in handle.Result)
                    callBack(item);
            }
            else
            {
                handle.Completed += (obj) =>
                {
                    //加载成功才调用外部传入的委托函数
                    if(obj.Status == AsyncOperationStatus.Succeeded)
                    {
                        foreach (T item in handle.Result)
                            callBack(item);
                    }
                };
            }
            return;
        }
        //不存在做什么
        handle = Addressables.LoadAssetsAsync<T>(list, callBack, mode);
        handle.Completed += (obj) =>
        {
            if(obj.Status == AsyncOperationStatus.Failed)
            {
                Debug.LogError("资源加载失败" + keyName);
                if (resDic.ContainsKey(keyName))
                    resDic.Remove(keyName);
            }
        };
        AddressablesInfo info = new AddressablesInfo(handle);
        resDic.Add(keyName, info);
    }

    public void LoadAssetAsync<T>(Addressables.MergeMode mode, Action<AsyncOperationHandle<IList<T>>> callBack, params string[] keys)
    {

    }

    public void Release<T>(params string[] keys)
    {
        //1.构建一个keyName  之后用于存入到字典中
        List<string> list = new List<string>(keys);
        string keyName = "";
        foreach (string key in list)
            keyName += key + "_";
        keyName += typeof(T).Name;
        
        if(resDic.ContainsKey(keyName))
        {
            resDic[keyName].count -= 1;
            if(resDic[keyName].count == 0)
            {
                //取出字典里面的对象
                AsyncOperationHandle<IList<T>> handle = resDic[keyName].handle.Convert<IList<T>>();
                Addressables.Release(handle);
                resDic.Remove(keyName);
            }
            
        }
    }

    //清空资源
    public void Clear()
    {
        foreach (var item in resDic.Values)
        {
            Addressables.Release(item.handle);
        }
        resDic.Clear();
        AssetBundle.UnloadAllAssetBundles(true);
        Resources.UnloadUnusedAssets();
        GC.Collect();
    }
}
