---
title: "Unity 小游戏优化"
date: 2026-03-11
tags:
  - Unity
  - WebGL
  - 性能优化
categories:
  - Unity
---

# Unity小游戏优化

**性能优化主要的4个方向：启动速度，资源加载，内存占用，减少CPU消耗**

---

# 加快游戏启动速度

- 构建选项中仅勾选首场景，其他场景用到时再下载
- 精简首场景物件，尽快渲染让玩家看到游戏首画面
- 减少代码包体，剔除不必要的插件
- 减少初始化与首帧逻辑，首场景 Awake / Start / 首次 Update 不要包含过重逻辑
- 减少代码包体，剔除不必要的插件
- CDN 必须开启 Brotli 或 gzip 压缩

---

## 优化的目标与标准

目前普通小游戏普遍启动时间为 **5~6s**，  
**优化目标首屏启动时间控制在 5~10s 甚至更短。**

---

# 启动耗时

小游戏启动主要由三部分影响：

1. 首包资源下载  
2. wasm代码下载和编译  
3. 引擎初始化与开发者首帧逻辑  

---

## 首包资源

首包资源（webgl/Build 目录下的 data 文件）主要有以下组成：

- Unity default resources 文件，引擎默认资源，如 Arial 字体、默认 mesh、纹理等
- il2cppmetadata，C#代码使用 il2Cpp 生成 cpp 代码时生成的类、方法等信息
- unity builtin_extra，always include 的 shader
- BuildSettings 中所有 active 的场景
- Resources 文件夹中的资源，以及其中引用到的其他资源
- 全局设置及引用到的资源，例如 splash 图片

---

## wasm代码

生成的 **原始代码不超过 30MB**

路径：Build/webgl_package/code.unityweb

优化方法：

- 勾选 `Strip Engine Code`
- 设置 `Managed Stripping Level` 为 `High`
- 配置 `link.xml` 文件告诉 Unity 哪些代码必须保留
- 使用代码分包工具减少 wasm 首包大小
- Unity 2021 以上版本可将 PlayerSettings 中 IL2CPP 选项设置为 **SIZE** 以减少函数数量

---

## 引擎初始化与首帧逻辑

优化建议：

- MonoBehaviour 脚本的首帧 Start / Awake 方法尽量不要包含复杂逻辑
- 初始场景尽量简单，例如 Splash 场景
- 主场景资源加载可以采用 **分帧加载**
- 避免在 Start 或 Awake 中阻塞主线程

示例：

```csharp
IEnumerator InitGame()
{
    yield return null;
    LoadConfig();

    yield return null;
    InitSystems();
}
```



## **资源按需加载**

- 切换TTAssetBundle、WXAssetBundle ，资源按需下载

- 检查各级 Resource 目录即 StreamingAssets 目录，避免非必要资源直接打包在首资源包内

- 单个ab的包体不推荐超过2MB；资源请求并发数不超过20个

- 网络闲时可做一些预下载功能。



##  降低小游戏内存使用

#### WASM代码编译内存

- 微信/抖音提供的代码分包工具，能将编译内存降低50%以上。
- 删除无用插件与模块：在`Package Manager`和`Player Settings`中移除未使用的服务（Analytics、Physics模块等）。

#### GPU内存

| 优化项                 | 具体操作                                             | 预期节省                  |
| :--------------------- | :--------------------------------------------------- | :------------------------ |
| **压缩纹理**           | 所有平台纹理设为**ASTC**格式，尺寸≤1024x1024         | 内存减少70%+              |
| **关闭HDR**            | 内置管线：`Graphics Settings (Tier 2) ->取消Use HDR` | 减少50% RenderTexture内存 |
| **MipMap与Read/Write** | 仅3D模型纹理开MipMap；UI纹理全部关闭`Read/Write`     | 减少30%-50%纹理内存       |

#### UnityHeap内存

精确设置Heap预留：

1. 构建时开启性能面板（转换面板勾选“显示性能面板”）。
2. 在低端机上运行最复杂场景，记录`DynamicMemory`峰值。
3. UnityHeap预留值 = DynamicMemory峰值 + (50-100MB)。

#### AssetBundle与音频内存优化

- 减少首资源包大小,此部分始终占用内存无法释放。
- 按需加载,及时释放以节省内存。
- 禁用Unity文件缓存：在构建时确保不勾选缓存相关选项。
- 所有音频勾选`Force To Mono`（单声道）。
- 加载类型设为`Compressed In Memory`。
- 同时播放音效数≤20，避免使用FMOD播放长BGM。



## **降低CPU消耗**

- iOS使用高性能模式
- 提前在Unity环境使用Unity Profiler发现问题
- 物理计算较重的游戏使用Fixed Timestep控制计算频率
- 在中低端机型限制帧率以减轻设备发烫