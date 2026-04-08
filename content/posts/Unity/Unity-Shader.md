---
title: "Unity-Shader"
date: 2022-03-06T22:25:02+08:00

tags:
  - Unity
  - Shader

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---

# Mesh

**MeshFilter 网格过滤器**

主要从众多的资源中挑选需要的**Mesh**，把它丢给**MeshRender**。

**MeshRenderer 网格渲染器**

主要是负责把**MeshFilter**丢过来的**Mesh**，绘制显示到我们的场景中，当然这项工作是非常复杂的。

**Material 材质球**

**Material**是**MeshRenderer**中非常重要的角色，它的配置决定了物体表面的外观将以怎样的质地呈现到我们眼前。如果天**MeshRenderer**不小心弄丢了**Material**，那这个物体就会变成让人烦躁的品红色。其实它跟大家经常看到的网页Error404差不多，RGB调成101就是这个颜色啦。

 如果说**Material**是**MeshRenderer**的灵魂，那**Shader**就是**Material**的灵魂，但凡**Shader**哪里不开心了，即使**MeshRenderer**没有弄丢**Material**，物体依旧会变成前面所说的101颜色。

 **Mesh 网格** 

 **Mesh**指的就是模型的网格，它决定了物体的表面形状是怎样的，一个模型的表面大多是由多个彼此相连的三角面构成，当然也有其它类型。我们平时听到的建模，可以简单理解为就是在建网格，那为什么Unity中的网格大多都是三角形而不是四边形呢？正所谓一生二，二生三，三生万物。三角形可以说是最为基础的面了，可以简单理解为三角形具有更广泛的适用性，而Mesh则是构成这些三角面所需的信息集合。

通过 [Mesh data - Unity 手册](https://docs.unity.cn/cn/2021.3/Manual/AnatomyofaMesh.html) 我们可以看到构成这些三角面所需的信息。

**Vertices** **顶点数组 Vector3[]**

顾名思义它存储的是顶点的相关信息，所谓点成线，线成面，可以理解为这里面存储的是构成网格面全部的点

**Topology 拓扑类型**

它存储的就是一个类型信息，可以理解为它是图形表面排列结构的组成方式，Unity给我们提供了5种拓扑类型，三角面、四边形、线条、虚线、点阵，最常用的则是三角面。

**Indices** **索引数组 int[]**

它是每个三角面顶点 的索引，可以理解为他存储了构网格三角面所用到的顶点索引。

**Vertex data** **顶点数据**

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171628215.png)

它包含了顶点的位置、法线、切线、UV等属性 

 **Normal 法线 Vector3[]** 

法线就是垂直于该顶点三角面的一条三维向量，它只有方向，没有大小。法线的方向就是顶点三角面朝外的方向。假设我们面前有一面镜子，它的正中心会有一条法线垂直于镜面指向我们，指向我们的面就是正面，相反就是背面

 **Tangent 切线 Vector3[]** 

它是垂直于法线的一条向量，而由于垂直于法线的向量有无数条，所以切线最终是由UV坐标来决定朝向的

 **UV  纹理坐标 Vector2[]** 

上面所说的UV坐标其实就是它，U增长的方向就是切线的方向，它和三维空间的X, Y, Z较为类似，它是一个二维的坐标系统，模型网格除了有三维空间的xyz坐标外，还有一个二维的UV坐标，在UV坐标中，U和V分别代表顶点在Texture水平和垂直方向上的采样坐标，这些坐标通常位于(0，0)和(1，1)之间，（0，0）代表最左下角，而（1，1）代表最右上角。这就跟平时装修房子贴墙纸一样，可以理解为它是Texture映射到模型表面的依据，模型顶点 会依据UV坐标对Texture进行采样。

 **Index data 索引数据**  

这个数据取决于拓扑类型，如果是三角面他储存的就是[0,1,2]，四边形储存的就是[0,1,2,3]，这个索引数值对应的就是顶点数组的下标。

# 渲染

## 渲染管线

渲染管线通常来说就是在虚拟相机、三维物体、光源、照明模式、纹理等诸多条件都给定的情况下，生成或是绘制一幅二维图像的过程。而这个过程会有很多步骤，这些步骤就渲染阶段。

一般这个过程会分为四个主要阶段：应用程序阶段、几何阶段、光栅化阶段、像素处理阶段。而每个阶段 又会分为很多个部分。

![image-20230317163626180](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171636205.png)

**应用程序阶段 （The Application Stage） CPU**

它最主要是负责数据的准备，也就是准备后面的阶段 所需的数据，像如模型，贴图，光照，相机位置等信息。

\-------------------------------------------------------------------------------------------------------------

**几何阶段（The Geometry Stage） GPU**

**顶点着色：**可编程部分，顾名思义，它会对逐个顶点相关的信息进行处理，生成图元，计算并传递给接下来的渲染流程。就像我们平时切菜一样，这个阶段就是为了控制食材的形态，

它对应的则是这里的VertxShader，它的工作主要是计算顶点的位置、法线、纹理坐标，根据材质、纹理、以及光源属性进行顶点光照的计算，平时常见的顶点动画一般就是在这里实现的。

[ **图元：**可以简单理解为它是渲染管线中所有点，线，面的统称 ]

**几何着色：** 可选可编程部分，并非所有GPU都支持 ，它可以把简单的图元拓展成更复杂的形式，通常我们认为，这两大着色器共同构成了 几何阶段的可编程部分。

**裁剪 ：** 固定功能硬件实现，对顶点几何两大着色器的输出结果进行处理，它会把完全处于视锥体交界外  以及屏幕窗口外的 几何体部分裁剪掉， 只留下用户能看到的部分，并且对生成的新顶点部分进行插值，输送给接下来的阶段。

\-------------------------------------------------------------------------------------------------------------

**光栅化阶段（Rasterization）** **GPU**

**屏幕映射：**经过裁剪之后，硬件会通过透视除法将物体从 裁剪空间 变换 为 标准化设备坐标也叫**NDC**，之后GPU会把得到的NDC空间坐标下的顶点，映射到屏幕空间坐标中，进行图元装配，这一步会计算微分、边方程和其他三角形数据，三角形的朝向剔除就是在这个阶段完成的。

**NDC：**全称Normalized Device Coordinates，一般来说裁剪完成后，会通过透视除法，将物体从裁剪空间 变换为标准化设备坐标NDC，透视除法是将裁剪空间中，顶点的4个分量都除以w分量，从裁剪空间转换到NDC。它是一个长宽高取值范围为[-1,1]的立方体，之所以要转到NDC，是为了方面我们后面进行视口变换把它映射到屏幕空间，不过Unity已经帮我们都完成这些了。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171640103.png)

**图元装配：**

主要是计算微分（differentials）、边方程（edge equations）和其他三角形数据（顶点属性插值）

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171640429.png)

 

**光栅化：**

它会在每个像素点上生成一个片元，如果开启了多重采样抗锯齿，就会对每个像素进行多次采样，产生多个片元，最终进行混合来达到抗锯齿的效果。

[ **片元**：是光栅化之后产生的像素点，因为没有被画到屏幕上，不能被直接称为像素一个像素的最终结果可能是由多个片元来决定的，渲染管线为了细分，就单独创造了片元这个词来描述它，片元只是渲染管线的概念 ]

[ **像素**：则是最后写到图像上的值 ]

\-------------------------------------------------------------------------------------------------------------

**像素处理阶段 （ Pixel Processing ）GPU**

**像素 (片元) 着色器：**可编程部分，它的工作主要是根据顶点的插值属性，进行逐像素计算，因为它需要处理每一个像素，所以这也是最耗时的一个阶段。它的输入输出都是片元数据，输入的数据是 颜色 和纹理坐标，输出的则是计算后所得的每个像素的色彩值，像是逐像素光照、反射、阴影等等更为复杂的效果都是可以在这里实现的，这一步更像是再给我们的食材上色。

**合并：**只可配置不可编程部分，在一系列的测试后会进行合并，所谓的测试则是判断一个像素点最终是否应该被显示在屏幕上，通过测试的颜色会和缓冲区的颜色叠加混合。

\-------------------------------------------------------------------------------------------------------------

**坐标空间**

**模型空间** 以物体本身为原点的坐标空间，**世界空间**以世界的(0,0)为原点的坐标空间，**视图空间** 以相机为原点的坐标空间，描述的物体在相机的哪个位置，**裁剪空间**  顶点坐标乘以MVP矩阵之后所在的空间，**屏幕空间** 窗口屏幕上的二维像素坐标空间。

![image-20230317164308152](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171643181.png)

# Shader

**Shader**比较学术的百科回答就是用来实现图像渲染的，用来替代固定渲染管线的可编辑程序。其中Vertex Shader（顶点着色器）主要负责顶点的几何关系等的运算，Pixel Shader（像素着色器）主要负责片元颜色等的计算。

## Unity中的Shader类型

**Standard Surface Shader**

标准表面着色器，它是一种基于物理的着色系统，可以理解为 它是通过对物理现象的简单模拟，可以实现生活中各种物品的效果，比如石头、木材、玻璃、塑料和金属等等。

**Unlit Shader**

它是最简单的着色器，与 Standard Surface Shader 相比，它去除了冗长的光照公式以及阴影解算，因此得名 Unlit，翻译过来就是无光照，也正因如此，它只由最基础的 Vertex Shader 和 Fragment Shader 组成，最为基础易懂。

**Image Effect Shader**

它其实也是也是顶点片元着色器，不过它主要针对实现各种屏幕后处理效果，那后处理是什么呢？一般像是泛光、调色、景深、模糊等基于最终整个屏幕画面而进行再次处理的就是后处理，这里做个简单的了解即可。

**Compute Shader**

计算着色器，它是在GPU中运行的一段程序，独立于常规渲染管线之外的，它可以直接将GPU作为并行处理器加以利用，从而使GPU不仅具有3D渲染能力，还具有其他的运算能力。一般会在需要大量并行计算的时候使用。

**Ray Tracing Shader**

光线追踪着色器，光线追踪是指从摄像机出发的若干条光线，每条光线会和场景里的物体求交，根据交点位置获取表面的材质、纹理等信息，并结合光源信息计算光照。相对于传统的光栅化渲染，光线追踪可以轻松模拟各种光学效果，如反射、折射、散射、色散等。但由于在进行求交计算时需要知道整个场景的信息，它的计算成本也是非常高的。

![image-20230317164459408](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171644436.png)

## ShaderLab

Unity为我们封装的着色器语言，而目前主流的着色器语言有3种，基于OpenGL的**GLSL** / 基于DX的**HLSL**  /  NVIDIA公司的**CG**。

GLSL与HLSL分别是基于OpenGL和Direct3D的接口，两者不能混用。而CG则是为了使图形硬件的编程变得和 C语言编程一样方便自由，它本身基于C语言。如果你之前使用过C系语言其中的任意一个，那CG的语法也是比较容易掌握的。但其实由于Microsoft和NVIDIA的相互协作，他们在标准硬件光照语言的语法和语义上达成了一致，所以HLSL和Cg其实可以看为是同一种语言。

而**ShaderLab**则是Unity在HLSL和CG的基础之上封装的只属于Unity的着色器语言，它的灵活性更高，而且不再需要将 Shader 的配置 硬写在引擎代码中，本质是在底层着色语言的基础上，额外提供了声明信息，以数据驱动的方式使我们在渲染管线内自由发挥。

### **ShaderLab语法详细解析**

```glsl
// Shader 的路径名称  默认为文件名,也可以与文件名不同
Shader "Unlit/MyShader"
{
    // 属性 
    // Material Inspector显示的所有参数都在需要在这里进行声明
    Properties
    {
        // 通常所有属性名都以下划线字符开头 _MainTex
        _MainTex ("Texture", 2D) = "white" {}

        // 比较常见的属性类型
        // ————————————————————————————————————————————————
        _Integer ("整数(新版)", Integer) = 1
        _Int ("整数(旧版)", Int) = 1
        _Float ("浮点数", Float) = 0.5
        _FloatRange ("浮点数滑动条", Range(0.0, 1.0)) = 0.5
        // Unity包含以下内置纹理, 可以直接填充
        // “white”（RGBA：1,1,1,1）
        // “black”（RGBA：0,0,0,1）
        // “gray”（RGBA：0.5,0.5,0.5,1）
        // “bump”（RGBA：0.5,0.5,1,0.5）
        // “red”（RGBA：1,0,0,1）
        _Texture2D ("2D纹理贴图", 2D) = "red" {}
        // 字符串留空或输入无效值，则它默认为 “gray”
        _DefaultTexture2D ("2D纹理贴图", 2D) = "" {}
        // 默认值为 “gray”（RGBA：0.5,0.5,0.5,1）
        _Texture3D ("3D纹理贴图", 3D) = "" {}
        _Cubemap ("立方体贴图", Cube) = "" {}
        // Inspector会显示四个单独的浮点数字段
        _Vector ("Example vector", Vector) = (0.25, 0.5, 0.5, 1)
        // Inspector会显示拾色器拾取色彩RGBA值
        _Color("色彩", Color) = (0.25, 0.5, 0.5, 1)
        // ————————————————————————————————————————————————

        // 除此之外 属性声明还可以具有一个可选特性 用来告知Unity如何处理它们
        // HDR可以使色彩亮度的值超过1
        [HDR]_HDRColor("HDR色彩", Color) = (1,1,1,1)
        // Inspector隐藏此属性
        [HideInInspector]_Hide("看不见我~", Color) = (1,1,1,1)
        // Inspector隐藏此纹理属性的Scale Offset字段
        [NoScaleOffset]_HideScaleOffset("隐藏ScaleOffset", 2D) = "" {}
        // 指示纹理属性为法线贴图，如果分配了不兼容的纹理，编辑器则会显示警告。
        [Normal]_Normal("法线贴图", 2D) = "" {}
    }

    // 子着色器 
    // 一个Shader至少有一个或者多个子着色器SubShader，这些子着色器互不干扰，且只有一个会运行
    // 在加载shader时Unity会遍历所有SubShader列表，并最终选择用户机器支持的第一个
    SubShader
    {
        // 可以通过Tags来向子着色器分配标签
        // 只可以写在SubShader语块内,不可写在Pass内
        /* 以键值对的形式存在,可以出现多个键值对
        Tags { 
            "TagName1" = "Value1"
            "TagName2" = "Value2"
            "TagName3" = "Value3"
            ...
            }
        */

        // RenderPipeline: 声明子着色器是否与通用渲染管线 (URP) 或高清渲染管线 (HDRP) 兼容
        // 仅与 URP 兼容
        // Tags { "RenderPipeline"="UniversalRenderPipeline" }
        // 仅与 HDRP 兼容
        // Tags { "RenderPipeline"="HighDefinitionRenderPipeline" }
        // RenderPipeline不声明或任何其他值表示与 URP 和 HDRP 不兼容
        // ————————————————————————————————————————————————

        // Queue: 声明渲染队列
        // Tags { "Queue"="Background" } // 最早被调用的渲染，用来渲染天空盒或者背景
        // Tags { "Queue"="Geometry" }   // 这是默认值，用来渲染非透明物体（普通情况下，场景中的绝大多数物体应该是非透明的）
        // Tags { "Queue"="AlphaTest" }  // 用来渲染经过Alpha Test的像素，单独为AlphaTest设定一个Queue是出于对效率的考虑
        // Tags { "Queue"="Transparent" }// 以从后往前的顺序渲染透明物体
        // Tags { "Queue"="Overlay" }    // 用来渲染叠加的效果，是渲染的最后阶段（比如镜头光晕等特效）
        // ————————————————————————————————————————————————

        // RenderType: 用来区别这个Shader要渲染的对象是属于什么类别的。
        // 设置渲染类型 用一种称为着色器替换的技术在运行时交换子着色器,用来区别这个Shader要渲染的对象是属于什么类别的
        // 这里表示非透明物体渲染
        Tags
        {
            "RenderType"="Opaque"
        }
        // 更多详细内容可参考官网文档 https://docs.unity.cn/cn/2021.3/Manual/SL-SubShaderTags.html

        // LOD (Level of Detail)
        LOD 100

        // 每个子着色器由多个通道组成，许多简单的着色器只使用一个通道，但想要一些更复杂的效果，着色器可能需要更多通道
        // 一个Pass就是一次绘制，可以看成是一个Draw Call而Pass的意义在于多次渲染，
        // 如果你有一个Pass，那么着色器只会被调用一次，如果你有多个Pass的话，
        // 那么就相当于执行多次SubShader了，这就叫双通道或者多通道。

        // Draw Call：其实就是CPU调用图像编程接口的渲染命令，CPU每次调用DrawCall，都需要向GPU发送许多数据啊、渲染状态等等，
        // 一旦CPU执行完应用阶段，GPU就会开始执行这次的渲染流程。而GPU渲染的速度比CPU提交命令的速度要快的多，
        // 所以如果DrawCall数量过多的情况下，CPU需要进行大量的计算，进而就会导致CPU过载，影响游戏的运行效率。
        Pass
        {
            CGPROGRAM
            // 声明顶点着色器
            #pragma vertex vert
            // 声明像素着色器
            #pragma fragment frag
            // 使雾生效
            #pragma multi_compile_fog

            // 引用CG的核心代码库
            #include "UnityCG.cginc"

            // 应用程序阶段结构体
            struct appdata
            {
                // 参考：https://docs.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl-semantics
                // POSITION 着色器语言的语义，用来限定着色器的输入输出值的类型
                // 模型空间的顶点坐标
                float4 vertex : POSITION;
                // 模型的第一套UV坐标
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                // UV
                float2 uv : TEXCOORD0;
                UNITY_FOG_COORDS(1)
                // SV_POSITION 当这个值需要作为输出值输出给系统用的时候 前面需要加SV_前缀
                // 当然因为有向下兼容的机制 不加也没啥太大问题
                float4 vertex : SV_POSITION;
            };

            // 在Properties中声明的参数要在这里相对应的定义后才可以使用
            sampler2D _MainTex;
            float4 _MainTex_ST;

            // 定义顶点着色器函数 函数名要与声明顶点着色器名称相同
            v2f vert(appdata v)
            {
                v2f o;
                // 将顶点坐标从模型空间变换到裁剪空间
                o.vertex = UnityObjectToClipPos(v.vertex);
                // Transforms 2D UV by scale/bias property
                // #define TRANSFORM_TEX(tex,name) (tex.xy * name##_ST.xy + name##_ST.zw)
                // 等价于v.uv.xy * _MainTex_ST.xy + _MainTex_ST.zw;
                // 简单来说，TRANSFORM_TEX主要作用是拿顶点的uv去和材质球的tiling和offset作运算，
                // 确保材质球里的缩放和偏移设置是正确的
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                UNITY_TRANSFER_FOG(o, o.vertex);
                return o;
            }

            // SV_Target可以视为COLOR ，虽说他也是作为输出值输出给系统的
            // 但它其实是告诉系统把输出的颜色值存储到RenderTarget中
            // 所以这里我们用SV_Target
            fixed4 frag(v2f i) : SV_Target
            {
                // 采样2D纹理贴图
                fixed4 col = tex2D(_MainTex, i.uv);
                // 应用雾
                UNITY_APPLY_FOG(i.fogCoord, col);
                // 返回经过处理后的最终色彩
                return col;
            }
            ENDCG
        }
    }
}
```

### 使用结构体简化Shader代码

#### 无返回值（in,out）

```glsl
Shader"Custom/Struct"
{
    Properties
    {
        //通过材质面板的颜色和纹理来修改fragment
        _MainColor("MainColor",Color) = (1,1,1,1)
        _MainTex("MainTex",2D) = "white"{}
    }
    SubShader
    {
        Pass
        {
            CGPROGRAM
            //在CG中调用属性变量
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            //定义顶点着色器的输入结构体
            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };
            //定义顶点着色器的输出结构体
            struct v2f
            {
                float4 position : SV_POSITION;
                float2 texcoord : TEXCOORD0;
            };
            fixed4 _MainColor;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            void vert (in appdata v, out v2f o)
            {
                o.position = UnityObjectToClipPos(v.vertex);
                o.texcoord = v.uv * _MainTex_ST.xy + _MainTex_ST.zw;
            }

            void frag(in v2f i ,out fixed4 color : SV_TARGET)
            {
                color = tex2D(_MainTex,i.texcoord) * _MainColor;
            }
            ENDCG
        }
    }
    Fallback "Diffuse"
}
```

#### 有返回值

```glsl
  Pass
        {
            CGPROGRAM
            //在CG中调用属性变量
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            //定义顶点着色器的输入结构体
            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };
            //定义顶点着色器的输出结构体
            struct v2f
            {
                float4 position : SV_POSITION;
                float2 texcoord : TEXCOORD0;
            };
            fixed4 _MainColor;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            v2f vert (appdata v)
            {
                v2f o;
                o.position = UnityObjectToClipPos(v.vertex);
                o.texcoord = v.uv * _MainTex_ST.xy + _MainTex_ST.zw;
                return o;
            }

            fixed4 frag(v2f i) : SV_TARGET
            {
                return tex2D(_MainTex,i.texcoord) * _MainColor;               
            }
            ENDCG
        }
```

### 使用Include"UnityCG.cginc"简化Shader

使用预制结构体appdata_img（vertex和texcoord）和v2f_img（pos和uv）,以及纹理坐标计算宏TRANSFORM_TEX()。

```glsl
Shader "Custom/IncludeShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _MainColor ("MainColor",Color) = (1,1,1,1)
    }
    SubShader
    {
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"
            
            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed4 _MainColor;

            v2f_img vert (appdata_img v)
            {
                v2f_img o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
                return o;
            }

            fixed4 frag (v2f_img i) : SV_TARGET
            {
                return tex2D(_MainTex, i.uv) * _MainColor;   
            }
            ENDCG
        }
    }
}
```

### normalize

返回输入向量的归一化，输出向量将与输入向量具有相同的方向，但长度为 1。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303172248157.png)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303172250147.png)

## 光照

Unlit 默认是不受光材质，纹理什么颜色它就直接显示出来了，但通常来说，光照是三维世界不可或缺的部分，现实世界中，当光照射到物体表面时, 物体对光会发生反射、透射、吸收、折射等被物体反射的光进入视觉系统，使看见物体的表面有明暗之分，为了模拟这一现象，科研家建立了一些数学模型来替代复杂的物理模型，统称为光照模型。

![image-20230317172053669](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171720724.png)

比较常见的光照模型有漫反射的 Half Lambert 模型 ，以及镜面反射的 Blinn-Phong 模型。 Half Lambert 能够较好地表现粗糙表面上的光照现象，像如石灰墙，纸张等等，但是在渲染金属材质制成的物体时，则会显得呆板，表现不出光泽。主要原因是其没有考虑到镜面反射效果，所以 Blinn-Phong 对其进行了很好的补充。

![image-20230317172443528](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171724563.png)

### Lambert模型

Lambert光照模型计算公式为：

![alt](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171746970.png)

使用入射光颜色和漫反射颜色相乘再乘以saturate裁剪后的光线方向（从入射点到光源方向）和法线方向夹角大小（点乘cos值）。

```glsl
Shader "Custom/Lambert"
{
    Properties
    {
        _MainColor("MainColor",Color) = (1, 1, 1, 1)
    }
    SubShader
    {
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "UnityLightingCommon.cginc"

            struct v2f
            {
                float4 pos : SV_POSITION;
                fixed4 dif : COLOR0;
            };

            fixed4 _MainColor;

            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                //法线向量 由模型空间变换到世界空间并标准化
                float3 n = UnityObjectToWorldNormal(v.normal);
                n = normalize(n);

                //灯光法向向量
                fixed3 l = normalize(_WorldSpaceLightPos0.xyz);
                
                //按照公式计算漫反射
                fixed ndotl = dot(n,l);
                o.dif = _LightColor0 * _MainColor * saturate(ndotl);

                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                return i.dif;
            }
            ENDCG
        }
    }
}
```

### HalfLambert模型

![alt](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171748305.png)

修改公式将夹角值映射到[0.5,1]避免背面全黑。

```glsl
            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                //法线向量 由模型空间变换到世界空间并标准化
                float3 n = UnityObjectToWorldNormal(v.normal);
                n = normalize(n);

                //灯光法向向量
                fixed3 l = normalize(_WorldSpaceLightPos0.xyz);
                
                //按照公式计算漫反射
                fixed ndotl = dot(n,l);
                o.dif = _LightColor0 * _MainColor * (0.5 * ndotl+0.5);

                return o;
            }
```

### Phong光照模型

Phong光照理论：物体表面反射光由三部分组成：环境光，漫反射，镜面反射（高光）。

![alt](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171750792.png)

漫反射的计算即Lambert光照模型，镜面反射的计算如下。

![alt](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171750731.png)

与Lambert公式类似，不同之处在于，这里和法线点乘的向量有入射光方向变成了视线方向（人直接看到的地方才高亮），并加以光泽度约束（实际约束了高光部分的范围，越小高光区域越大）。

环境光ambient、漫反射diffuse、高光specular三个光照计算都是用颜色乘法计算的，但是最后返回的颜色值，确实把三个光照颜色相加，这是为什么呢？以下是我的理解：

**加法是没有关系的颜色之间的叠加，而乘法是模拟光的照射过程**

```glsl
Shader "Custom/Phong"
{
    Properties
    {
        _MainColor("MainColor",Color) = (1, 1, 1, 1)
        _Shininess("Shininess",Range(1,100)) = 1
        _SpecularColor("Specular Color",Color) = (0, 0, 0, 0)
    }
    SubShader
    {
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "UnityLightingCommon.cginc"

            struct v2f
            {
                float4 pos : SV_POSITION;
                fixed4 color : COLOR0;
            };

            fixed4 _MainColor;
            fixed4 _SpecularColor;
            half _Shininess;

            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                //法线向量 由模型空间变换到世界空间并标准化
                float3 n = UnityObjectToWorldNormal(v.normal);
                n = normalize(n);

                //灯光法向向量
                fixed3 l = normalize(_WorldSpaceLightPos0.xyz);
                
                //视线方向向量
                fixed3 view = normalize(WorldSpaceViewDir(v.vertex));

                //漫反射
                fixed ndotl = dot(n,l);
                fixed4 dif = _LightColor0 * _MainColor * saturate(ndotl);

                //镜面反射 光线方向取负，输入Reflect函数为光源到顶点的方向
                float3 ref = reflect(-l, n);
                ref = normalize(ref);
                fixed rdotv = saturate(dot(ref,view));
                fixed4 spec = _LightColor0 * _SpecularColor * pow(rdotv, _Shininess);

                //环境光 + 漫反射 +镜面反射
                o.color = unity_AmbientSky + dif + spec;
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                return i.color;
            }
            ENDCG
        }
    }
}
```

### Blinn-Phong

为减少计算量，将Phong光照模型中的v替换成半角向量h（视角方向和灯光方向角平分方向），视觉效果差距不大，但当观察者和灯光离物体很远时镜面反射近似为常数。

半角向量计算公式：

![alt](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171751741.png)

Blinn-Phong 镜面反射计算公式：

![alt](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171751523.png)

修改镜面反射部分代码。

```glsl
 fixed4 frag (v2f i) : SV_Target
            {
                 //法线向量 由模型空间变换到世界空间并标准化
                float3 n = UnityObjectToWorldNormal(i.normal);
                n = normalize(n);

                //灯光法向向量
                fixed3 l = normalize(_WorldSpaceLightPos0.xyz);
                
                //视线方向向量
                fixed3 view = normalize(WorldSpaceViewDir(i.vertex));

                //漫反射
                fixed ndotl = dot(n,l);
                fixed4 dif = _LightColor0 * _MainColor * saturate(ndotl);

                //镜面反射 光线方向取负，输入Reflect函数为光源到顶点的方向
                float3 h = normalize(l + view);
                fixed ndoth = saturate(dot(h, n));
                fixed4 spec = _LightColor0 * _SpecularColor * pow(ndoth, _Shininess);

                //环境光 + 漫反射 +镜面反射
                return unity_AmbientSky + dif + spec;
            }
```

### 逐像素光照

对于顶点数量较少的Sphere类型，可以很明显的看到光照范围边缘并不光滑，可以通过增加曲面细分等级或者改由片段着色器逐像素计算光照。光照计算交由frag进行，顶点着色器只负责传递发现和顶点（pos是裁切空间的顶点，vertex是模型空间的顶点用于计算方向向量），在frag里面texcoord语义可以用于存任意的float4类型，不局限于UV。

```glsl
Shader "Custom/Phong_Pixel"
{
    Properties
    {
        _MainColor("MainColor",Color) = (1, 1, 1, 1)
        _Shininess("Shininess",Range(1,100)) = 1
        _SpecularColor("Specular Color",Color) = (0, 0, 0, 0)
    }
    SubShader
    {
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
            #include "UnityLightingCommon.cginc"

            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 normal : TEXCOORD0;
                float4 vertex : TEXCOORD1;
            };

            fixed4 _MainColor;
            fixed4 _SpecularColor;
            half _Shininess;

            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.normal = v.normal;
                o.vertex = v.vertex;              
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                 //法线向量 由模型空间变换到世界空间并标准化
                float3 n = UnityObjectToWorldNormal(i.normal);
                n = normalize(n);

                //灯光法向向量
                fixed3 l = normalize(_WorldSpaceLightPos0.xyz);
                
                //视线方向向量
                fixed3 view = normalize(WorldSpaceViewDir(i.vertex));

                //漫反射
                fixed ndotl = dot(n,l);
                fixed4 dif = _LightColor0 * _MainColor * saturate(ndotl);

                //镜面反射 光线方向取负，输入Reflect函数为光源到顶点的方向
                float3 ref = reflect(-l, n);
                ref = normalize(ref);
                fixed rdotv = saturate(dot(ref,view));
                fixed4 spec = _LightColor0 * _SpecularColor * pow(rdotv, _Shininess);

                //环境光 + 漫反射 +镜面反射
                return unity_AmbientSky + dif + spec;
            }
            ENDCG
        }
    }
}
```

<img src="https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171757748.png" alt="alt" style="zoom: 200%;" />



## 阴影

### Unity的渲染路径

在Unity中，渲染路径（Rendering Path）决定了**光照**是如何应用到Unity Shader中的。如果要和光源打交道，就需要为每个pass指定使用的渲染路径。这样，才能让Unity为我们提供正确的数据信息。

在pass中，通过设置LightMode标签来指定使用的渲染路径。

> Tags { "LightMode" = "ForwardBase" }

下表给出Pass的LightMode标签支持的主要渲染路径设置：

| 标签名       | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| Always       | 不管使用哪种渲染路径，该Pass总会被渲染，但不会计算任何光照   |
| ForwardBase  | 用于前向渲染。该Pass会计算环境光，最重要的平行光、逐顶点/SH光源和Lightmaps |
| ForwardAdd   | 用于前向渲染。该Pass会计算额外的逐像素光源，每个Pass对应一个光源 |
| Deffered     | 用于延迟渲染。该Pass会渲染G缓冲（G-buffer）                  |
| ShadowCaster | 把物体的深度信息渲染到阴影映射纹理（shadowmap）或一张深度纹理中 |

官方LightMode介绍见链接：[ShaderLab: Predefined Pass tags in the Built-in Render Pipeline](https://link.zhihu.com/?target=https%3A//docs.unity3d.com/2019.4/Documentation/Manual/shader-predefined-pass-tags-built-in.html)

为Pass指定渲染路径标签可以使Unity提供正确的（对于shader编写者来说）内置光照变量。

### 延迟着色渲染路径

先裁切再计算光照，因此光照在屏幕空间计算，所有灯光都是逐像素计算，计算量与场景复杂度无关，避免计算深度测试未通过的遮挡部分，提升性能，但不支持抗锯齿，半透明，正交相机且对硬件有要求。

### 前向渲染路径

先计算光照再裁切，支持所有Unity图形功能，一部分最亮的灯光（离物体近）用逐像素光照渲染，然后最多4个灯光用逐顶点光照渲染，剩余用SH光照渲染，不同灯光组之间会重叠熏染，基础Pass包括一个逐像素平行光和所有逐顶点/SH的灯光，每多一个逐像素灯光会额外产生一个Pass，可以设置每个灯光的渲染模式Important来决定用顶点/像素光照渲染。

### PassFlags标签

用于更改渲染流水线传递数据给Pass的方式，目前仅可使用的值为OnlyDirectional，使得前向渲染时，SH和逐顶点灯光不能传数据。

### 内置的multi_compile

默认情况前向渲染只支持一个投射阴影的平行光，可以通过multi_compile_fwdbase/fwdadd/fwaddshadows添加多重编译指令编译出不同的Shader变体来处理不同类型的灯光、阴影和灯光贴图。

### 实现阴影效果

（使用Lambert光照模型） 第一个Pass用于渲染平行光、逐顶点、SH的灯光，为平行光产生阴影投射。第二个Pass用于渲染其他逐像素的灯光并为其产生阴影投射。

```glsl
Shader "Custom/Shadow"
{
    Properties
    {
        _MainColor ("Main Color", Color) = (1, 1, 1, 1)
    }
    SubShader
    {
        //平行光投影（还包括逐顶点或SH的灯光渲染）
        Pass
        {   
            Tags{"LightMode" = "ForwardBase"}
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma multi_compile_fwdbase
            #include "UnityCG.cginc"
            #include "Lighting.cginc"
            #include "AutoLight.cginc"

            struct v2f
            {   
                float4 pos : SV_POSITION;
                float3 normal : TEXCOORD0;
                float4 vertex : TEXCOORD1;
                SHADOW_COORDS(2) //使用预定义宏保存阴影坐标
            };

            fixed4 _MainColor;
            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.normal = v.normal;
                o.vertex = v.vertex;
                TRANSFER_SHADOW(o) //将阴影纹理坐标装入结构体
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {   
                //世界空间法向量、光照方向、顶点坐标
                float3 n = UnityObjectToWorldNormal(i.normal);
                n = normalize(n);
                float3 l = WorldSpaceLightDir(i.vertex); //使用WorldSpaceLightDir计算光照方向而不用WorldSpaceLightPos（只计算平行光）
                l = normalize(l);
                float4 worldPos = mul(unity_ObjectToWorld,i.vertex);

                //Lambert光照
                fixed ndotl = saturate(dot(n, l));
                fixed4 color = _LightColor0 * _MainColor * ndotl;

                //点光源的光照
                color.rgb += Shade4PointLights(
                unity_4LightPosX0,unity_4LightPosY0,unity_4LightPosZ0,
                unity_LightColor[0].rgb, unity_LightColor[1].rgb,
                unity_LightColor[2].rgb, unity_LightColor[3].rgb,
                unity_4LightAtten0, worldPos.rgb, n) * _MainColor;
                
                //环境光照
                color += unity_AmbientSky;

                //计算阴影系数
                UNITY_LIGHT_ATTENUATION(shadowmask,i,worldPos.rgb);

                //阴影合成
                color.rgb *= shadowmask;
                return color;
            }
            ENDCG
        }
        // 其他逐像素灯光投影
        Pass
        {
            Tags{"LightMode" = "ForwardAdd"}
            //使用相加混合，与上个Pass融合
            Blend One One 
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma multi_compile_fwdadd_fullshadows
            #include "UnityCG.cginc"
            #include "Lighting.cginc"
            #include "AutoLight.cginc"

            struct v2f
            {   
                
                float4 pos : SV_POSITION;
                float3 normal : TEXCOORD0;
                float4 vertex : TEXCOORD1;
                SHADOW_COORDS(2) //使用预定义宏保存阴影坐标
            };

            fixed4 _MainColor;
            v2f vert (appdata_base v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.normal = v.normal;
                o.vertex = v.vertex;
                TRANSFER_SHADOW(o) //将阴影纹理坐标装入结构体
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {   
                //世界空间法向量、光照方向、顶点坐标
                float3 n = UnityObjectToWorldNormal(i.normal);
                n = normalize(n);
                float3 l = WorldSpaceLightDir(i.vertex); //使用WorldSpaceLightDir计算光照方向而不用WorldSpaceLightPos（只计算平行光）
                l = normalize(l);
                float4 worldPos = mul(unity_ObjectToWorld,i.vertex);

                //Lambert光照
                fixed ndotl = saturate(dot(n, l));
                fixed4 color = _LightColor0 * _MainColor * ndotl;

                //点光源的光照
                color.rgb += Shade4PointLights(
                    unity_4LightPosX0,unity_4LightPosY0,unity_4LightPosZ0,
                    unity_LightColor[0].rgb, unity_LightColor[1].rgb,
                    unity_LightColor[2].rgb, unity_LightColor[3].rgb,
                    unity_4LightAtten0, worldPos.rgb,n) * _MainColor;
                
                //环境光照不再计算 上个Pass已做
                //color += unity_AmbientSky;

                //计算阴影系数
                UNITY_LIGHT_ATTENUATION(shadowmask,i,worldPos.rgb);

                //阴影合成
                color.rgb *= shadowmask;
                return color;
            }

            ENDCG
        }
    }
    FallBack "Diffuse" //使用Difuuse中的阴影投射Pass
}
```

![image-20230317230457449](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303172304490.png)



## 透明效果

透明是游戏中经常要用的一种效果，在实时渲染中要实现透明效果，通常会在渲染模型时控制它的透明通道(Alpha Channel)。当开启透明混合后，当一个物体被渲染到屏幕上时，每个片元除了颜色值和深度值外，他还有另外一个属性——`透明度`。当透明度为1时。表示该像素是完全不透明的，而当其为0时，则表示改像素完全不会显示。
在Unity中，我们通常使用`透明度测试`（这种方法其实无法得到真正的的半透明效果）和`透明度混合`实现透明效果。
对于不透明物体，不考虑他们渲染顺序也能得到正确的排序效果，这是由于强大深度缓冲（depth buffer也被称为z-buffer）。
在实时渲染中，深度缓冲是用于解决可见性问题的，核心思想：根据深度缓冲中的值来判断该片元距离摄像机的距离，当渲染一个片元时，需要把它的深度值和已经存在与深度缓冲中的值进行比较（如果开启了深度测试），`如果它的值距离摄像机更远，则不需要渲染到屏幕（有物体挡住了它），否则这个片元将会覆盖掉此时颜色缓冲中的像素值，并把它的深度值更新到深度缓冲中（如果开启深度写入）。`
但是如果要实现透明效果，当使用透明度混合时，我们关闭了深度写入（ZWrite）。
简单来说，透明度测试和透明度混合的基本原理如下。
**透明度测试**
只要一个片元的透明度不满足条件，那么它对应的片元就会被舍弃，并且不会再做任何处理，也不会影响颜色缓冲。`透明度测试不需要关闭深度写入。`只会产生两个结果，要么完全透明看不到，要么完全不透明。
**透明度混合**
使用当前片元的透明度作为混合因子，与已经存储在颜色缓冲中的颜色值进行混合，得到新的颜色。但是`透明度混合需要关闭深度写入。这是因为，如果不关闭深度写入，一个半透明表面背后的表面本来是可以透过它被我们看到的，但是由于深度测试时判断结果是该半透明表面距离摄像机更近，导致后面的表面将会被剔除，我们也无法通过半透明表面看到后面的物体了。`
透明度混合只关闭了深度写入，没有关闭深度测试，即`当使用透明度混合渲染一个片元时，还是会比较它的深度值与当前深度缓冲中的深度值，如果它的深度值距离摄像机更远，就不会进行混合操作`。所以，当一个不透明物体出现在一个透明物体前面，而我们先渲染了不透明物体，他仍然可以正常遮挡住透明物体。
`总结：对于透明度混合来说，深度缓冲是只读的。`

### 为什么渲染顺序很重要

因为透明度关闭了深度写入，如果不保证渲染顺序的话，会导致错误的渲染结果。
比如半透明A（离摄像机近），不透明B（离摄相机远），如果先渲染A，不会在深度缓冲区写入数据，但会写入颜色缓冲区。再渲染B，会在深度缓冲区写入深度值，然后写入颜色缓冲区，即覆盖A的颜色，看起来物体B在A前面了。
再比如半透明A（离摄像机近），半透明B（离摄像机远），如果先渲染A，不会在深度缓冲区写入数据，但会写入颜色缓冲区。再渲染B，不会在深度缓冲区写入深度值，会与颜色缓冲区中的A进行颜色混合，看起来物体B在A前面了。
因此，渲染引擎一般会对物体进行排序，再渲染，常用方法是：
先渲染所有不透明物体，并开启他们深度测试和深度写入。
把半透明物体按他们距离摄像机远近进行排序，然后按照从后往前的顺序渲染这些半透明物体，并开启他们的深度测试，关闭深度写入。
但是如果仅此而已，还是不够的，因为会有一些特殊情况。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182314829.png!webp)

而我们是根据他们离摄像机远近来排序的，而距离摄像机远近依靠的是深度值（像素级，即每个像素都有一个深度值)，但我们现在是对单个物体级进行排序，所以要么A全在B前面，要么A全在B后面。

### Unity Shader的渲染顺序

Unity为了解决渲染顺序的问题，提供了渲染队列（render queue）这一解决方案、我们可以使用SubShader的Queue标签来决定我们模型将归于哪个渲染队列。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308604.png!webp)



### 透明度测试

```glsl
Shader "Custom/AlphaTest"
{
    Properties
    {
        _Color("Main Tint",Color) = (1,1,1,1)
        _MainTex ("Texture", 2D) = "white" {}
        //决定我们调用clip进行透明度测试时使用的判断条件。
        //它的范围是[0,1]这是因为纹理像素的透明度就是在此范围内。
        _Cutoff("Alpha Cutoff",Range(0,1)) = 0.5
    }
    SubShader
    {
        //Queue定义归属渲染队列
        //IgnoreProjector为true表示这个Shader不会受到投影器影响
        //RenderType可以让Unity把这个Shader归入到提前定义的组
        Tags { "Queue" = "AlphaTest" "IgnoreProjector" = "True" "RenderType" = "TransparentCutout"}
        
        //关闭剔除，可以看到背面
        Cull Off
 
        Pass
        {
            //定义光照模式
            Tags { "LightMode" = "ForwardBase"}
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "Lighting.cginc"

            fixed4 _Color;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed _Cutoff;

            struct a2v
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldPos : TEXCOORD1;
                float2 uv : TEXCOORD2;
            };


            v2f vert (a2v v)
            {
                v2f o;
                //把顶点位置从模型空间转换到裁剪空间中
                o.pos = UnityObjectToClipPos(v.vertex);
                //计算出世界空间法线方向
                o.worldNormal = UnityObjectToWorldNormal (v.normal);
                //计算出世界空间顶点位置
                o.worldPos = mul(unity_ObjectToWorld,v.vertex).xyz;
                //计算经过平铺和偏移后的纹理坐标
                o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                fixed3 worldNormal = normalize(i.worldNormal);
                fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
                //对纹理进行采样
                fixed4 texColor = tex2D(_MainTex,i.uv);
                //会判断texColor.a - _Cutoff是否为负，如果是就会舍弃该片元的输出
                //当texColor.a小于材质参数_Cutoff时，该片元就会产生完全透明的效果
                clip(texColor.a - _Cutoff);
                //得到环境光
                fixed3 albedo = texColor.rgb * _Color.rgb;
                //计算高光反射
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
                //计算漫反射
                fixed3 diffuse = _LightColor0.rgb * albedo*max(0,dot(worldNormal,worldLightDir));
                
                return fixed4(ambient + diffuse,1.0);
            }
            ENDCG
        }

    }
    Fallback "Transparent/Cutout/VertexLit"
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308713.png!webp)



### 透明度混合

为了进行混合，我们需要使用Unity提供的混合命令——Blend。Blend是Unity提供的设置混合模式的命令。想要实现半透明效果就需要把当前自身的颜色和已经存在于颜色缓冲中的颜色值进行混合。混合时使用的函数就是由该指令决定的。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308808.png!webp)



```glsl
Shader "Custom/AlphaBlend"
{
    Properties
    {
        _Color ("Color Tint", Color) = (1, 1, 1, 1)
        _MainTex ("Main Tex", 2D) = "white" {}
        //_AlphaScale用于在透明纹理的基础上控制整体的透明度
        _AlphaScale ("Alpha Scale", Range(0, 1)) = 1
    }
    SubShader
    {
        //Queue定义归属渲染队列，因为是透明度混合，所以是Transparent队列
        //IgnoreProjector为true表示这个Shader不会受到投影器影响
        //RenderType可以让Unity把这个Shader归入到提前定义的组
        Tags {"Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Transparent"}
        
        //关闭剔除，可以看到背面
        Cull Off
 
        Pass
        {
            //定义光照模式
            Tags { "LightMode" = "ForwardBase"}
            //关闭深度写入
            ZWrite Off
            //将该片元着色器产生的颜色混合因子设置为SrcAlpha
            //将已经存在与颜色缓冲中的颜色混合因子设置为OneMinusSrcAlpha
            Blend SrcAlpha OneMinusSrcAlpha
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "Lighting.cginc"

            fixed4 _Color;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed _AlphaScale;

            struct a2v
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldPos : TEXCOORD1;
                float2 uv : TEXCOORD2;
            };


            v2f vert (a2v v)
            {
                v2f o;
                //把顶点位置从模型空间转换到裁剪空间中
                o.pos = UnityObjectToClipPos(v.vertex);
                //计算出世界空间法线方向
                o.worldNormal = UnityObjectToWorldNormal (v.normal);
                //计算出世界空间顶点位置
                o.worldPos = mul(unity_ObjectToWorld,v.vertex).xyz;
                //计算经过平铺和偏移后的纹理坐标
                o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                fixed3 worldNormal = normalize(i.worldNormal);
                fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
                //对纹理进行采样
                fixed4 texColor = tex2D(_MainTex,i.uv);
                //得到环境光
                fixed3 albedo = texColor.rgb * _Color.rgb;
                //计算高光反射
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
                //计算漫反射
                fixed3 diffuse = _LightColor0.rgb * albedo*max(0,dot(worldNormal,worldLightDir));
                //设置片元着色器返回值的透明通道
                return fixed4(ambient + diffuse,texColor.a*_AlphaScale);
            }
            ENDCG
        }

    }
    Fallback "Transparent/VertexLit"
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308888.png!webp)


但是会有一种情况，得到错误的渲染结果

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308973.png!webp)



### 开启深度写入的半透明效果

为了解决上述问题，我们通常使用两个Pass进行渲染，第一个Pass开启深度写入，但不输出颜色，它的目的仅仅是为了把模型的深度值写入深度缓冲中，第二个Pass进行正常的透明度混合，由于上一个Pass已经得到了逐像素的正确的深度信息，该Pass就可以按照像素级别的深度排序结果进行透明渲染。但是`这样会消耗一定的额外性能`。

### ShaderLab的混合命令

混合的实现原理：当片元着色器产生一个颜色时，可以选择与颜色缓存中的颜色进行混合，然后重新写入颜色缓冲。（注意，我们平时谈论混合中的颜色都是RGBA四个通道的）。
在Unity中，当我们使用Blend（Blend Off命令除外）时，除了设置混合状态外，也开启了混合，但是`在其他图形API中我们是需要手动开启的`。

#### 混合等式和参数

混合是一个逐片元操作，而且他是不可编程的，但却是高度可配置的。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308221.png!webp)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308248.png!webp)



#### 混合操作

当把原颜色和目标颜色与他们对应的混合因子相乘后，我们都是把他们的结果加起来作为输出颜色的。我们也可以使用别的混合操作：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308272.png!webp)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308425.png!webp)


混合操作命令通常是与混合因子命令一起工作的。但需要注意的是，当使用Min或Max混合操作时，混合因子实际上是不起任何作用的，他们仅仅会判断原始的源颜色和目的颜色之间的比较结果。



#### 常见的混合类型

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303182308504.png!webp)



### 双面渲染的透明效果

和透明度测试相比。想让透明度混合实现双面渲染会更加复杂一点，这是因为关闭了深度写入。如果我们仍然直接关闭剔除功能，那么我们就无法保证同一个物体的正面和背面图元的渲染顺序。`就有可能得到错误的半透明效果。`
为此我们把双面渲染工作分成两个Pass——第一个Pass只渲染背面，第二个Pass只渲染正面，我们可以保证背面总是在正面被渲染前渲染，从而保证正确的深度渲染关系。

# PBR

**PBR**是 **Physically Based Rendering**的简称，也就是图形渲染中的真实感渲染，简单理解就是基于物理的渲染技术，但它只是对现实世界物理的一种近似，并非完全的物理渲染，与之前学习的NPR属于完全不同的两种渲染理念，PBR是以基于**微平面理论**，**能量守恒**，以及物理的**BRDF**为核心的渲染理念。

## 微平面理论

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171807512.png)

简单来说就是物体表面持续放大至微观尺度后，任何平面都是由 无数微小的无序随机朝向的镜面组成。

平面越是粗糙，这个平面上的微平面的排列就越混乱，平面越是光滑，光线大体上会更趋向于同一个方向反射，形成更小更锐利的反射。

其实在微观尺度下，没有任何平面是完全光滑的。但由于这些微平面已经微小到无法逐像素继续进行区分，因此会假设一个粗糙度参数，然后用统计学的方法来估算微平面的粗糙程度，通过这个粗糙度计算出某个向量的方向，与微平面平均取向的一致性。

一般粗糙度越高的显示出来的镜面反射的轮廓要更大一些，相反则会更小更锐利。在实际的PBR 中，这种物体表面的不规则性一般会用粗糙度贴图或者高光度贴图来表示~

## 能量守恒

说白了就是反射光的能量永远不能超过入射光的能量，为了遵守能量守恒定律，通常我们会对漫反射光和镜面反射光之间做出明确的区分。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171808677.png)

在PBR中，镜面光指的就是光线射入物体表面时所反射的那部分能量，而漫反射光则是指光线进入物体后再均匀的发散出来的那部分能量。当光线碰撞到一个表面的时候，一部分能量被反射，另一部分能量进入物体内部。

不过不同的材质表面它的反射细节也会有所不同，通常会分为金属和非金属材质，其中金属表面由于有自由电子，所以进入表面的能量会被完全吸收。而非金属表面由于没有自由电子，进入表面的能量有一部分会被均匀的释放出来，所以金属度低，但是完全光滑的物体，不会呈现金属那样的镜面效果，而是带有自身的颜色晕染。

## 菲涅尔现象

一般来说漫反射光和镜面反射光之间都是受菲涅尔参数所控制，通过菲涅尔我们可以根据观察的角度得到被反射的能量所占的百分比。利用这个反射比率和能量守恒原则，可以直接得出光线进入物体的部分以及光线剩余的能量。

菲涅尔表示的是看到的光线的反射率与视角相关的现象，视线垂直于表面时反射较弱，而当视线与表面夹角越小时，反射越明显。

这就好比我们平时拿吸管插奶茶杯一样，物体的表面就可以看作是奶茶杯的那层膜，光线的光子可以看作是吸管，当你越是斜着插的时候 吸管就越容易被弹开 ， 很难插进去，而当你越是竖着插的时候，吸管也就越容易进去。这也就是所谓的菲涅尔现象。

而光线的能量进入物体之后会有不同的释放形式

- 对于金属表面来说，能量进去后，会全部吸收掉。
- 而对于非金属表面来说，能量进去后，只会吸收一部分，余下的部分则会被释放。
- 对于玉石这类 次表面 散射的 材质来说，能量进去后，会吸收一部分，而余下的能量会从正面出来一部分，从侧面也漏出来一部分。
- 对于玻璃这类透明的材质来说，能量进去后，吸收的部分很少，正面出来也很少，基本都会从另一面出来。

## 真实感光照

PBR的光照不止是直接光，如果只是直接光，那PBR渲染出来的画面，其实和以前并没有多少明显的进步。通常我们会通过叠加计算直接光和间接光得到最终的光照结果，这个结果被称为全局光照，也就是常听到的GI。

说白点就是通过模拟光线的传播路径，将物体反射的间接光纳入计算，从而提高画面的真实感。直接光就是光线从光源直接照射到物体表面上的光，而间接光则是光线经过多次弹射到物体表面上的光。不过因为我们无法完全模拟现实中如此复杂的物理环境，像如阳光的大气折射与场景弹射次数和质量等，所以通常会加入环境光去做近似模拟。

这也是PBR中比较重要的光照部分 - IBL 也就是基于图像的光照。它会把周围的环境颜色完整的保存到一张贴图上，PBR材质会把这个贴图当作光源来进行采样，通过一系列的公式计算得到最终的环境光。如果是金属材质，并且粗糙度比较低，他就能够近乎完整的映射出周围的环境的镜像倒影。不过IBL不处理多次反射，它只是单纯的把环境贴图当成了光源的预积分，这里不做过多赘述，有个大致概念即可。

## BRDF

学术一点的叫法就是双向反射分布函数，通过入射光方向，反射光方向，平面法线以及微平面粗糙程度作为函数的输入参数，求出最终反射光的强度。

其实我们之前做的Blinn-Phone也可以看作是一个BRDF，但由于Blinn-Phong并没有遵循能量守恒的定律，所以它不被认为是基于物理的渲染。

在早期 PBR模型包含了众多复杂的物理属性，学习成本高、不易于理解，后来在SIGGRAPH 2012，迪士尼提出了Disney BRDF [s2012_pbs_disney_brdf_notes_v3.pdf (disneyanimation.com)](https://media.disneyanimation.com/uploads/production/publication_asset/48/asset/s2012_pbs_disney_brdf_notes_v3.pdf) ，它把众多复杂的物理属性简化成更少更为直观的属性，以极高的易用性、便捷性正式进入了大众的视野，逐渐在业界内被广泛应用，可以说是PBR史上的里程碑。

Unity基于Disney BRDF的启发在GDC 2014 推出了Physically Based Shading in Unity [201403-GDC_UnityPhysicallyBasedShading.key (aras-p.info)](https://aras-p.info/texts/files/201403-GDC_UnityPhysicallyBasedShading_notes.pdf) ，将传统的渲染工作流升级为基于物理的渲染工作流，美术同学只需要调节Unity内置的Standard Shader，就可以实现绝大多数常见的材质。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171814378.png)

那最终Disney BRDF得到了1个颜色参数以及10个标量参数，颜色参数就是 **BaseColor** 表面颜色，通常由纹理贴图提供

**Subsurface**：使用 次表面近似 控制 漫反射的形状

**Metallic**：金属与非金属两种不同模型之间的线性混合。金属模型没有漫反射的成分

**Specular**：一般用来控制镜面反射的强度

**SpecularTint**：镜面反射的颜色啦，不过这个相对过时，通常很少有对美术控制的让步，一般会用于对基础色的入射镜面反射，进行颜色的控制。

**Roughness**：用来控制表面的粗糙度啦

**Anisotropic**：各向异性的强度，主要用于头发啊金属之类的材质

**Sheen & SheenTint**：控制的是光泽的强度和颜色，一般会用于布料

**Clearcoat & ClearcoatGloss:** 则是清漆强度和光泽度，平时用到的很少，知道有这么个东西就好啦

## Standard Shader

![image-20230317181247011](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202303171812041.png)

**RenderingMode**：控制的是物体的渲染模式，简单理解就是设定物体是透明的渲染模式还是不透明的渲染模式

**Albedo**：其实就是材质的基础固有色，你可以给它一张纹理贴图也可以选择单色，当你选择纹理贴图的时候这个调色板则为纹理之上的叠加色

**Metallic & Smoothness**：这里则是用来控制材质的金属度和光滑度，金属度只是决定了物体最终渲染的结果是以高光为主，还是漫反射为主。而光滑度则是决定了物体的高光部分是否清晰。通常来说不使用贴图的话材质只能具有单一的金属度，这并不理想，毕竟同一个物体表面 可能会具有不同的表面特性，那这里如果你选择了金属度贴图，这里的滑动条会直接隐藏掉，通过贴图所存储的 金属度信息来进行采样计算。光滑度的概念其实和我们之前所提的的粗糙度大同小异，反过来了而已。

**Source**：可以理解为就是选择光滑贴图，它的数据可以来源于金属贴图，也可以是色彩贴图，这就根据美术来定啦~

**NormalMap**：法线贴图，它属于凹凸贴图的一种，通常用来给模型增加一些微小的凸起、凹槽和划痕等细节，在逐像素计算光照时，每个像素都会根据该点的法向量来计算最终该点的光照结果，可以通过法线贴图，改变这个点法线方向，影响它的光照结果，进而影响模型表面凹凸感。

**HeightMap**：视差贴图，又可以称之为高度贴图，是法线贴图的改进版，属于一个经常被忽略的高级功能。大家都知道法线，可以将一个平面做成凹凸不平的效果，但是当视角方向水平于该平面的时候，理论上凸起的部分会遮挡住后面的部分，而法线贴图却没有这个效果，但是高度贴图，就可以。那原理呢，就是根据该点的高度以及该点指向摄像机的向量，计算出一个UV偏移，来影响之后的采样。

**Occlusion**：环境光遮蔽贴图，通常又会叫AO贴图，在PBR中计算光照的时候，一般直接通过采样IBL来得到环境光，这个环境光是该点上一个半球上的积分。但是因为自身的之间会有凹凸，在凹陷的地方，环境光会被周围给遮挡，所以看起来并不是那么亮，通过AO贴图我们可以让调整环境光的大小，从而达到更真实的效果。

**Emission**：材质的自发光属性，通常用于控制从表面发出的光的颜色和强度，用的比较多的地方像如霓虹灯、LED屏幕等等。

**Tiling & Offset**：控制的则是以上所有贴图的Tiling Offset
