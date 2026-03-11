---
title: "Unity-Camera"
date: 2022-03-29T23:17:58+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---

## 摄像机组件

照相机是玩家观察世界的装置，屏幕空间点按像素定义，屏幕的左下为（0,0）；右上是（pixelwidth，pixelHeight），z位置在照相机的世界单位中。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192033981)

相机组件



```bash
Clear Flags ：
清除标识：确定了屏幕哪些部分将被清除，方便多个摄像机画不同的游戏元素
Background:背景色
Culling Mask:包含或省略要由相机呈现的对象层。在检查器中将图层分配给您的对象。
Projection:  切换相机的功能来模拟透视。
    Perspective(透视):  相机将完整地呈现透视物体。拍摄角度为0-180°（最高）
Field of View: 设置为“正交”时，“相机”的视口大小。
Orthographic(正交):  相机将统一渲染对象，没有视角。注：正交模式下不支持延迟渲染。正向渲染总是被使用。
Size:设置为“正交”时，“相机”的视口大小。
Cliping Planes:从相机到开始和停止渲染的距离。
     Near ：相对于相机的最近点将出现绘图。
     Far  ：相对于相机的最远点将出现绘图。
ViewportRect：视口矩形 四个值指示屏幕上的相机视图将被绘制的位置。在视口坐标中测量（值为0-1）。
Depth:相机的位置按照画图顺序。具有较大值的相机将被绘制在具有较小值的相机之上。
Rendering Path:用于定义相机将使用什么渲染方法的选项
    渲染路径 ：定义什么绘制方法被用于相机的选项
    Use Graphics Settings 使用玩家设置：在玩家设置（Player Settings.）相机使用哪个渲染路径。 
     Forward 正向渲染：所有对象每材质渲染只渲染一次,快速渲染
     Deferred 延迟照明：所有物体将在无光照的环境渲染一次，然后在渲染队列尾部将物体的光照一起渲染出来。
    Legacy Vertex Lit 顶点光照 ：所有被这个相机渲染的物体都将渲染成Vertex-Lit物体。
    Legacy Deferred : 旧的延迟光照 
Target Texture : 目标纹理:渲染纹理 （Render Texture）包含相机视图输出。这会使相机渲染在屏幕上的能力被禁止。可用于实现画中画或者画面特效。
Occlusion Culling : 是否剔除物体背向摄像机的部分
Allow HDR:高动态光照渲染，启动相机高动态范围渲染功能。让场景更真实。
Allow MSAA: 这台相机应该使用MSAA渲染目标吗？如果当前质量设置MSAA级别支持，将只使用MSAA。
Allow Dynamic Resolution：动态分辨率缩放。
如果相机使用动态分辨率渲染，则为true，否则为false。即使此属性为true，动态分辨率也只能在当前图形设备支持的情况下使用。
Target Display:设置此摄像机的目标显示。
此设置使摄像机呈现在指定的显示中。显示器（例如监视器）支持的最大数目是8. 
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192033445)

### Clear Flags

```rust
Clear Flags ：
清除标识：确定了屏幕哪些部分将被清除，方便多个摄像机画不同的游戏元素
Skybox  ：
天空盒：这是默认设置。屏幕上的任何空的部分将显示当前相机的天空盒。
如果当前的相机没有设置天空盒，它会默认在渲染设置（Render Settings ）选择天空盒
Solid Color ：
纯色，屏幕上的空白部分将显示当前摄像机的背景色
Depth Only ：
深度相机，只渲染采集到的画面
如果你想绘制一个玩家的枪，又不让它内部环境被裁剪，你会设置深度为0的相机绘制环境，
和另一个深度为1的相机单独绘制武器。武器相机的清除标志（Clear Flags ）应设置 为depth only。
Don't Clear ：
不清除，此模式不清除颜色或深度缓存。每帧的渲染画面叠加在上一帧画面之上。
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192032367)

### Culling Mask

```kotlin
Culling Mask:包含或省略要由相机呈现的对象层。在检查器中将图层分配给您的对象。
Nothing:什么层都不剔除
Everything:什么层都剔除
Default:默认层剔除
TransparentFX:隐形层,系统不会渲染贴图和模型
Ignore Raycast:射线层剔除
Water:水层剔除
UI：UI层剔除
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192032199)

### Rendering Path

```bash
Rendering Path:用于定义相机将使用什么渲染方法的选项
渲染路径 ：定义什么绘制方法被用于相机的选项
Use Graphics Settings 使用玩家设置：在玩家设置（Player Settings.）相机使用哪个渲染路径。 
  Forward 正向渲染：所有对象每材质渲染只渲染一次,快速渲染
  Deferred 延迟照明：所有物体将在无光照的环境渲染一次，然后在渲染队列尾部将物体的光照一起渲染出来。
Legacy Vertex Lit 顶点光照 ：所有被这个相机渲染的物体都将渲染成Vertex-Lit物体。
Legacy Deferred : 旧的延迟光照 
Traget Texture 目标纹理:渲染纹理 （Render Texture）包含相机视图输出。
这会使相机渲染在屏幕上的能力被禁止。可用于实现画中画或者画面特效与
Occlusion Culling : 是否剔除物体背向摄像机的部分
Allow HDR:高动态光照渲染，启动相机高动态范围渲染功能。让场景更真实。
Allow MSAA: 这台相机应该使用MSAA渲染目标吗？如果当前质量设置MSAA级别支持，将只使用MSAA。
Allow Dynamic Resolution：动态分辨率缩放。
如果相机使用动态分辨率渲染，则为true，否则为false。即使此属性为true，动态分辨率也只能在当前图形设备支持的情况下使用。
Target Display:设置此摄像机的目标显示。
此设置使摄像机呈现在指定的显示中。显示器（例如监视器）支持的最大数目是8. 
```

## Camera实例

- aspect ： 获取或者设置Camera视口的宽高比例值。例如：camera.aspect =2.0f,则视口的宽度、高度 = 2.0f，当硬件显示器屏幕的宽度与高度比例不为2.0f时,视图的显示将会发生变形。aspect只处理摄像机camera可以看到的视图的宽高比例，而硬件显示屏的作用只是把摄像机camera看到的内容显示出来，当硬件显示屏的宽高比例与aspect的比例值不同时，视图将发生变形。



```cpp
using UnityEngine;

public class aspect : MonoBehaviour {

    void Start () {
        Debug.Log("默认值" + Camera.main.aspect);
    }
    private void OnGUI()
    {
        if (GUI.Button(new Rect(10f, 10f, 200f, 45f), "aspect = 1.0"))
        {
            Camera.main.ResetAspect();
            Camera.main.aspect = 1.0f;
        }

        if (GUI.Button(new Rect(10f, 60f, 200f, 45f), "aspect = 2.0"))
        {
            Camera.main.ResetAspect();
            Camera.main.aspect = 2.0f;
        }
        if (GUI.Button(new Rect(10f, 110f, 200f, 45f), "aspect = 3.0"))
        {
            Camera.main.ResetAspect();
            Camera.main.aspect = 3.0f;
        }
        if (GUI.Button(new Rect(10f, 160f, 200f, 45f), "aspect = 0.0"))
        {
            Camera.main.ResetAspect();
        }
    }
}
```

- cameraToWorldMatrix : 变换矩阵

```cpp
using UnityEngine;

public class cameratoworldmatrix : MonoBehaviour {

    // Use this for initialization
    void Start () {
        Debug.Log("Camera旋转前位置" + transform.position);
        Matrix4x4 m = Camera.main.cameraToWorldMatrix;
        // 向量的位置转换为世界坐标中的位置
        //v3 的值为沿着Camera局部坐标系的-z轴方向前移5个单位的位置在世界坐标系中的位置
        Vector3 v3 = m.MultiplyPoint(Vector3.forward * 5.0f);
        //v4 的值为沿着Camera世界坐标系的-z轴方向前移5个单位的位置在世界坐标系中的位置
        Vector3 v4 = m.MultiplyPoint(transform.forward * 5.0f);
        Debug.Log("旋转前，V3坐标值："+v3);
        Debug.Log("旋转前，V4坐标值："+v4);
        // 将摄像机沿着Y轴正向旋转90度（此时摄像机局部坐标系的z轴方向和世界坐标的X轴方向一致），
        transform.Rotate(Vector3.up * 90f);
        m = Camera.main.cameraToWorldMatrix;
        v3 = m.MultiplyPoint(Vector3.forward * 5.0f);
        v4 = m.MultiplyPoint(transform.forward * 5.0f);
        Debug.Log("旋转后， v3坐标值"+v3);
        Debug.Log("旋转后， v4坐标值"+v4);
    }
}
```

- CullingMask 按层渲染，此属性用于按层（GameObject.layer）有选择性地渲染场景中的物体。通过cullingMask可以使得当前摄像机有选择性地渲染场景中的部分物体，默认cullingMask =-1即渲染场景中的任何物体，cullingMask = 0时不渲染场景中的任何物体。若只渲染2,3,4，可以使用cullingMask = （1<<2）+ (1<<3)+(1<<4)来进行。

```cpp
using UnityEngine;

public class cullingMask : MonoBehaviour {

    void Start () {
        Debug.Log("默认值" + Camera.main.aspect);
    }
    private void OnGUI()
    {
        if (GUI.Button(new Rect(10f, 10f, 200f, 45f), "CullingMask = -1"))
        {
            Camera.main.cullingMask = -1;
        }

        if (GUI.Button(new Rect(10f, 60f, 200f, 45f), "CullingMask = 0"))
        {
           Camera.main.cullingMask = 0;
        }
        if (GUI.Button(new Rect(10f, 110f, 200f, 45f), "aspect = 3.0"))
        {
            Camera.main.cullingMask =1 << 0;
        }
        if (GUI.Button(new Rect(10f, 160f, 200f, 45f), "aspect = 1<<8"))
        {
            Camera.main.cullingMask =1 << 8;
        }
         //渲染第8层与第0层
       if (GUI.Button(new Rect(10f, 160f, 200f, 45f), "aspect = 0&&8"))
        {
            // 注意运算符优先排序
            Camera.main.cullingMask =1+（1 << 8）;
        }
    }
}
```

- eventMask属性：按层响应事件，选择哪个层（layer）的物体可以响应鼠标事件

> 1.必须满足两个条件：
>
> - 1.物体在摄像机的视野范围内。
> - 2.在2的layer次方的值与eventMask进行运算（&）后结果仍为2的layer次方的值，如：defalult ,layer值为0，2的0次方=1，如果1与eventMask进行与运算后扔为1，则此物体响应鼠标事件。由于EventMask为奇数时，与1的与运算结果都为1，所以若物体层为defalut并且eventMask为奇数时物体会响应鼠标事件。

> 2.如果想要多个不同层的物体响应鼠标事件，则需要把所有层的2的layer次方值相加，再与eventMask做与运算。例如，2个物体，layer值分贝为1,3，当event与9进行与运算后结果仍为9，则这两个物体都会响应鼠标事件。

> 3.此属性有一个特殊情况，但固体layer选择IgnoreRaycast(其为系统内置，值为2)时，无论EventMask值为多少，物体都无法响应鼠标事件。

```csharp
using UnityEngine;
using System.Collections;

public class EventMask_ts : MonoBehaviour
{
    bool is_rotate = false;//控制物体旋转
    public Camera c;//指向场景中摄像机
    //记录摄像机的eventMask值，可以在程序运行时在Inspector面板中修改其值的大小
    public int eventMask_now = -1;
    //记录当前物体的层
    int layer_now;
    int layerTemp;//记录2的layer次方的值
    int ad;//记录与运算（&）的结果
    string str = null;

    void Update()
    {
        //记录当前对象的层，可以在程序运行时在Inspector面板中选择不同的层
        layer_now = gameObject.layer;
        //求2的layer_now次方的值
        layerTemp= (int)Mathf.Pow(2.0f, layer_now);
        //与运算（&）
        ad = eventMask_now & layerTemp;
        c.eventMask = eventMask_now;
        //当is_rotate为true时旋转物体
        if (is_rotate)
        {
            transform.Rotate(Vector3.up * 15.0f * Time.deltaTime);
        }
    }
    //当鼠标左键按下时，物体开始旋转
    void OnMouseDown()
    {
        is_rotate = true;
    }
    //当鼠标左键抬起时，物体结束旋转
    void OnMouseUp()
    {
        is_rotate = false;
    }
    void OnGUI()
    {
        GUI.Label(new Rect(10.0f, 10.0f, 300.0f, 45.0f), "当前对象的layer值为：" + layer_now + " , 2的layer次方的值为" + layerTemp);
        GUI.Label(new Rect(10.0f, 60.0f, 300.0f, 45.0f), "当前摄像机eventMask的值为：" + eventMask_now);
        GUI.Label(new Rect(10.0f, 110.0f, 500.0f, 45.0f), "根据算法，当eventMask的值与" + layerTemp+ "进行与运算（&）后， 若结果为" + tp + "，则物体相应OnMousexxx方法，否则不响应！");

        if (ad == tp)
        {
            str = " ,所以物体会相应OnMouseDown方法！";
        }
        else
        {
            str = " ,所以物体不会相应OnMouseDown方法！";
        }
        GUI.Label(new Rect(10.0f, 160.0f, 500.0f, 45.0f), "而当前eventMask与" + layerTemp+ "进行与运算（&）的结果为" + ad + str);
    }
}
```

- LayerCullDistances:层消隐的距离

> 摄像机可以通过基于层（GameObject.layer）的方式来设置不同层物体的消隐距离，但这个距离必须小于或者等于摄像机的farClipPlane才有效。

```cpp
public class LayerCullDistances : MonoBehaviour
{
// 拿到要消除的物体
    public Transform obj;
    void Start()
    {
        //定义大小为32的一维数组，用来存储所有层的剔除距离
        float[] distances = new float[32];
        //设置第12层的剔除距离
        distances[11] = Vector3.Distance(transform.position,obj.position);
        //将数组赋给摄像机的layerCullDistances，首先这个获取的是layer所有的层距离
        Camera.main.layerCullDistances = distances;
    }
    void Update()
    {
        //摄像机远离物体,会发现设置了蹭的
        transform.Translate(transform.right * Time.deltaTime);
    }
}
```

- layerCullSpherical:基于球面距离剔除

> 基于球面距离的剔除方式。属性默认为false，即不使用球面剔除，表示只要有一点没有超出物体所在层的远视口平面，物体就是可见的。当设置此属性为True时，只要物体的世界坐标点Position与摄像机的距离大于所在层的剔除距离，物体就不可见。

```cpp
public class layerCullSpherical : MonoBehaviour
{
    public Transform cb1, cb2, cb3;
    void Start()
    {
        //定义大小为32的一维数组，用来存储所有层的剔除距离
        float[] distances = new float[32];
        //设置第9层的剔除距离
        distances[8] = Vector3.Distance(transform.position, cb1.position);
        //将数组赋给摄像机的layerCullDistances
        Camera.main.layerCullDistances = distances;
        //打印出三个物体距离摄像机的距离
        Debug.Log("Cube1距离摄像机的距离：" + Vector3.Distance(transform.position, cb1.position));
        Debug.Log("Cube2距离摄像机的距离：" + Vector3.Distance(transform.position, cb2.position));
        Debug.Log("Cube3距离摄像机的距离：" + Vector3.Distance(transform.position, cb3.position));
    }

    void OnGUI()
    {
        //使用球形距离剔除
        if (GUI.Button(new Rect(10.0f, 20.0f, 180.0f, 45.0f), "使用球形距离剔除"))
        {
            Camera.main.layerCullSpherical = true;
        }
        //取消球形距离剔除
        if (GUI.Button(new Rect(10.0f, 70.0f, 180.0f, 45.0f), "取消球形距离剔除"))
        {
            Camera.main.layerCullSpherical = false;
        }
    }
}
```

- orthographic :摄像机投影模式

> 用来设置相机投影模式， 模式有两种：正交投影（orthographic）,与透视投影模式（perspective）若值为true,正交模式，反之投影模式。正交模式下：物体在视口的代销至于正交视口的大小有关，与摄像机到物体的距离无关，主要呈现2D效果。透视模式下，有远小近大的效果。呈现3D效果

```go
public class orthographic : MonoBehaviour
{
    float len = 5.5f;
    void OnGUI()
    {
        if (GUI.Button(new Rect(10.0f, 10.0f, 120.0f, 45.0f), "正交投影"))
        {
            Camera.main.orthographic = true;
            len = 4.5f;
        }
        if (GUI.Button(new Rect(150.0f, 10.0f, 120.0f, 45.0f), "透视投影"))
        {
            Camera.main.orthographic = false;
            len = 60.0f;
        }
        if (Camera.main.orthographic)
        {
            //正交投影模式下，物体没有远大近小的效果，
            //orthographicSize的大小无限制，当orthographicSize为负数时视口的内容会颠倒，
            //orthographicSize的绝对值为摄像机视口的高度值，即上下两条边之间的距离
            len = GUI.HorizontalSlider(new Rect(10.0f, 60.0f, 300.0f, 45.0f), len, -20.0f, 20.0f);
            Camera.main.orthographicSize = len;
        }
        else
        {
            //透视投影模式下，物体有远大近小的效果，
            //fieldOfViewd的取值范围为1.0-179.0
            len = GUI.HorizontalSlider(new Rect(10.0f, 60.0f, 300.0f, 45.0f), len, 1.0f, 179.0f);
            Camera.main.fieldOfView = len;
        }
        //实时显示len大小
        GUI.Label(new Rect(320.0f, 60.0f, 120.0f, 45.0f), len.ToString());
    }
}
```

- pixelRect：摄像机渲染区间
   设置Camera被渲染到屏幕中的坐标位置。以实际像素大小来设置显示视口的位置。如下图：A为原始平面大小，B为变换后的视口大小，则X0的值为视口右移的像素大小，Y0的值为视口上移的像素大小，w为Camera.pixelWidth,h的值为Camera.pixelHeight。这里要注意：Screen.width和Screen.height为模拟硬件屏幕的宽高值，不随Camera.pixelWidth和Camera.pixelHeight的改变而改变。

  ![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192032327)

  pixelRect

```cpp
public class PixelRect : MonoBehaviour
{
    int which_change = -1;
    float temp_x = 0.0f, temp_y = 0.0f;
    void Update()
    {
        //Screen.width和Screen.height为模拟硬件屏幕的宽高值,
        //其返回值不随camera.pixelWidth和camera.pixelHeight的改变而改变
        Debug.Log("Screen.width:" + Screen.width);
        Debug.Log("Screen.height:" + Screen.height);
        Debug.Log("pixelWidth:" + Camera.main.pixelWidth);
        Debug.Log("pixelHeight:" + Camera.main.pixelHeight);
        //通过改变Camera的坐标位置而改变视口的区间
        if (which_change == 0)
        {
            if (Camera.main.pixelWidth > 1.0f)
            {
                temp_x += Time.deltaTime * 20.0f;
            }
            //取消以下注释察看平移状况
            //if (Camera.main.pixelHeight > 1.0f)
            //{
            //    temp_y += Time.deltaTime * 20.0f;
            //}
            Camera.main.pixelRect = new Rect(temp_x, temp_y, Camera.main.pixelWidth, Camera.main.pixelHeight);
        }
        //通过改变Camera的视口宽度和高度来改变视口的区间
        else if (which_change == 1)
        {
            if (Camera.main.pixelWidth > 1.0f)
            {
                temp_x = Camera.main.pixelWidth - Time.deltaTime * 20.0f;
            }
            //取消以下注释察看平移状况
            //if (camera.pixelHeight > 1.0f)
            //{
            //    temp_y = camera.pixelHeight - Time.deltaTime * 20.0f;
            //}
            Camera.main.pixelRect = new Rect(0, 0, temp_x, temp_y);
        }
    }
    void OnGUI()
    {
        if (GUI.Button(new Rect(10.0f, 10.0f, 200.0f, 45.0f), "视口改变方式1"))
        {
            Camera.main.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
            which_change = 0;
            temp_x = 0.0f;
            temp_y = 0.0f;
        }
        if (GUI.Button(new Rect(10.0f, 60.0f, 200.0f, 45.0f), "视口改变方式2"))
        {
            Camera.main.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
            which_change = 1;
            temp_x = 0.0f;
            temp_y = Camera.main.pixelHeight;
        }
        if (GUI.Button(new Rect(10.0f, 110.0f, 200.0f, 45.0f), "视口还原"))
        {
            Camera.main.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
            which_change = -1;
        }
    }
}
```

- projectionMatrix属性：自定义投影矩阵
   此属性的功能是设置摄像机的自定义投影矩阵。此属性常在一些特效场景下用到，在切换变换矩阵是通常需要先用Camera.ResetProjectionMatrix()重置Camera的变换矩阵。

```cpp
public class ProjectionMatrix : MonoBehaviour
{
    public Transform sp, cb;
    public Matrix4x4 originalProjection;
    float q=0.1f;//晃动振幅
    float p=1.5f;//晃动频率
    int which_change = -1;
    void Start()
    {
        //记录原始投影矩阵
        originalProjection = Camera.main.projectionMatrix;
    }
    void Update()
    {
        sp.RotateAround(cb.position, cb.up, 45.0f * Time.deltaTime);
        Matrix4x4 pr = originalProjection;
        switch (which_change)
        {
            case -1:
                break;
            case 0:
                //绕摄像机X轴晃动
                pr.m11 += Mathf.Sin(Time.time * p) * q;
                break;
            case 1:
                //绕摄像机Y轴晃动
                pr.m01 += Mathf.Sin(Time.time * p) * q;
                break;
            case 2:
                //绕摄像机Z轴晃动
                pr.m10 += Mathf.Sin(Time.time * p) * q;
                break;
            case 3:
                //绕摄像机左右移动
                pr.m02 += Mathf.Sin(Time.time * p) * q;
                break;
            case 4:
                //摄像机视口放缩运动
                pr.m00 += Mathf.Sin(Time.time * p) * q;
                break;
        }
        //设置Camera的变换矩阵
        Camera.main.projectionMatrix = pr;
    }
    void OnGUI()
    {
        if (GUI.Button(new Rect(10.0f, 10.0f, 200.0f, 45.0f), "绕摄像机X轴晃动"))
        {
            Camera.main.ResetProjectionMatrix();
            which_change = 0;
        }
        if (GUI.Button(new Rect(10.0f, 60.0f, 200.0f, 45.0f), "绕摄像机Y轴晃动"))
        {
            Camera.main.ResetProjectionMatrix();
            which_change = 1;
        }
        if (GUI.Button(new Rect(10.0f, 110.0f, 200.0f, 45.0f), "绕摄像机Z轴晃动"))
        {
            Camera.main.ResetProjectionMatrix();
            which_change = 2;
        }
        if (GUI.Button(new Rect(10.0f, 160.0f, 200.0f, 45.0f), "绕摄像机左右移动"))
        {
            Camera.main.ResetProjectionMatrix();
            which_change = 3;
        }
        if (GUI.Button(new Rect(10.0f, 210.0f, 200.0f, 45.0f), "视口放缩运动"))
        {
            Camera.main.ResetProjectionMatrix();
            which_change = 4;
        }
    }
}
```

- Rect属性：摄像机视图的位置和大小

```cpp
public class Rect : MonoBehaviour
{
    int which_change = -1;
    float temp_x = 0.0f, temp_y = 0.0f;
    void Update()
    {
        //视口平移
        if (which_change == 0)
        {
            if (Camera.main.rect.x < 1.0f)
            {
                //沿着X轴平移
                temp_x = Camera.main.rect.x + Time.deltaTime * 0.2f;
            }
            //取消下面注释察看平移的变化
            //if (camera.rect.y< 1.0f)
            //{
            //沿着Y轴平移
            //    temp_y = camera.rect.y + Time.deltaTime * 0.2f;
            //}
            Camera.main.rect = new Rect(temp_x, temp_y, Camera.main.rect.width, Camera.main.rect.height);
        }
         //视口放缩
        else if (which_change == 1)
        {
            if (Camera.main.rect.width > 0.0f)
            {
                //沿着X轴放缩
                temp_x = Camera.main.rect.width - Time.deltaTime * 0.2f;
            }
            if (Camera.main.rect.height > 0.0f)
            {
                //沿着Y轴放缩
                temp_y = Camera.main.rect.height - Time.deltaTime * 0.2f;
            }
            Camera.main.rect = new Rect(Camera.main.rect.x, Camera.main.rect.y, temp_x, temp_y);
        }
    }
    void OnGUI()
    {
        if (GUI.Button(new Rect(10.0f, 10.0f, 200.0f, 45.0f), "视口平移"))
        {
            //重置视口
            Camera.main.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
            which_change = 0;
            temp_y = 0.0f;
        }
        if (GUI.Button(new Rect(10.0f, 60.0f, 200.0f, 45.0f), "视口放缩"))
        {
            Camera.main.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
            which_change = 1;
        }
        if (GUI.Button(new Rect(10.0f, 110.0f, 200.0f, 45.0f), "视口还原"))
        {
            Camera.main.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
            which_change = -1;
        }
    }
}
```

- RenderingPath : 渲染路径

  ![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192033051)

  ### RenderingPath 

> 用于获取和设置摄像机渲染路径。Unity中渲染路径RenderingPath为枚举类型
>  VertexLit:使用顶点光照。最低消耗的渲染路径，不支持实时阴影，适用于移动及老式设备。
>  forward:使用正向光照，基于着色器的渲染路径。支持逐像素计算光照（包括法线贴图和灯光Cookies）和来自一个平行光的实时阴影。
>  DeferredLighting:使用延迟光照，支持实时阴影，计算消耗大，对硬件要求高，不支持移动设备，仅专业版可用。



```cpp
public class renderingPath: MonoBehaviour
{
    void OnGUI()
    {
        if (GUI.Button(new Rect(10.0f, 10.0f, 120.0f, 45.0f), "UsePlayerSettings"))
        {
            Camera.main.renderingPath = RenderingPath.UsePlayerSettings;
        }
        if (GUI.Button(new Rect(10.0f, 60.0f, 120.0f, 45.0f), "VertexLit"))
        {
            Camera.main.renderingPath = RenderingPath.VertexLit;
        }
        if (GUI.Button(new Rect(10.0f, 110.0f, 120.0f, 45.0f), "Forward"))
        {
            Camera.main.renderingPath = RenderingPath.Forward;
        }
        if (GUI.Button(new Rect(10.0f, 160.0f, 120.0f, 45.0f), "DeferredLighting"))
        {
            Camera.main.renderingPath = RenderingPath.DeferredLighting;
        }
    }
}
```

- WorldToCameramatrix：变换矩阵

> 返回或设置当前从世界坐标系到当前Camera自身坐标系的变换矩阵。重设矩阵时，摄像机的Transform组件数据不会同步更新。如果想回到Transform的可控状态，需要调用ResetWorldToCameraMatrix方法重置摄像机旋转矩阵。



```cpp
public class WorldToCameraMatrix : MonoBehaviour
{
    public Camera c_test;
    void OnGUI()
    {
        if (GUI.Button(new Rect(10.0f, 10.0f, 200.0f, 45.0f), "更改变换矩阵"))
        {
            //使用c_test的变换矩阵
            Camera.main.worldToCameraMatrix = c_test.worldToCameraMatrix;
            //也可使用如下代码实现同样功能
            // camera.CopyFrom(c_test);
        }
        if (GUI.Button(new Rect(10.0f, 60.0f, 200.0f, 45.0f), "重置变换矩阵"))
        {
            Camera.main.ResetWorldToCameraMatrix();
        }
    }
}
```

- RenderToCubMap：生成Cubemap静态贴图。
- RenderWithShader:使用其他shader渲染
   使用指定shader来代替当前物体的shader渲染一帧。当replacementTag为空时会替换视口中所有物体的shader



```cpp
public class RenderWithShader : MonoBehaviour
{
    bool is_use = false;
    void OnGUI()
    {
        if (is_use)
        {
            //使用高光shader：Specular来渲染Camera
            Camera.main.RenderWithShader(Shader.Find("Specular"), "RenderType");
        }
        if (GUI.Button(new Rect(10.0f, 10.0f, 300.0f, 45.0f), "使用RenderWithShader启用高光"))
        {
            //RenderWithShader每调用一次只渲染一帧，所以不可将其直接放到这儿
            //camera.RenderWithShader(Shader.Find("Specular"), "RenderType");
            is_use = true;
        }
        if (GUI.Button(new Rect(10.0f, 60.0f, 300.0f, 45.0f), "使用SetReplacementShader启用高光"))
        {
            //SetReplacementShader方法用来替换已有shader，调用一次即可
            Camera.main.SetReplacementShader(Shader.Find("Specular"), "RenderType");
            is_use = false;
        }
        if (GUI.Button(new Rect(10.0f, 110.0f, 300.0f, 45.0f), "关闭高光"))
        {
            Camera.main.ResetReplacementShader();
            is_use = false;
        }
    }
}
```

- SetReplacementShader 使用指定的shader来替换物体当前的shader,被替换后每一帧都会替换shader来渲染物体,与上面的方法刚好不同。
- ScreenPointToRay:近视口到屏幕的射线



```csharp
public class ScreenPointToRay_ts : MonoBehaviour
{
    Ray ray;
    RaycastHit hit;
    Vector3 v3 = new Vector3(Screen.width / 2.0f, Screen.height / 2.0f, 0.0f);
    Vector3 hitpoint = Vector3.zero;
    void Update()
    {
        //射线沿着屏幕X轴从左向右循环扫描
        v3.x = v3.x >= Screen.width ? 0.0f : v3.x + 1.0f;
        //生成射线
        ray = Camera.main.ScreenPointToRay(v3);
        if (Physics.Raycast(ray, out hit, 100.0f))
        {
            //绘制线，在Scene视图中可见
            Debug.DrawLine(ray.origin, hit.point, Color.green);
            //输出射线探测到的物体的名称
            Debug.Log("射线探测到的物体名称：" + hit.transform.name);
        }
    }
}
```

- ScreenToViewPortPoint 坐标系转换
   实现坐标点从屏幕坐标系向摄像机视口的单元化坐标系转换。但是是像素



```cpp
public class ScreenToViewportPoint_ts : MonoBehaviour
{
    void Start()
    {
        transform.position = new Vector3(0.0f, 0.0f, 1.0f);
        transform.rotation = Quaternion.identity;
        //从屏幕的实际坐标点向视口的单位化比例值转换
        Debug.Log("1:" + Camera.main.ScreenToViewportPoint(new Vector3(Screen.width / 2.0f, Screen.height / 2.0f, 100.0f)));
        //从视口的单位化比例值向屏幕的实际坐标点转换
        Debug.Log("2:" + Camera.main.ViewportToScreenPoint(new Vector3(0.5f, 0.5f, 100.0f)));
        Debug.Log("屏幕宽：" + Screen.width + "  屏幕高：" + Screen.height);
    }
}
```

- ScreenToworldPoint : 坐标系转换，从屏幕坐标系到世界坐标系，实际单位像素组值，而非比例值。



```cpp
public class ScreenToWorldPoint_ts : MonoBehaviour
{
    void Start()
    {
        transform.position = new Vector3(0.0f, 0.0f, 1.0f);
        Camera.main.fieldOfView = 60.0f;
        Camera.main.aspect = 16.0f / 10.0f;
        //Z轴前方100处对应的屏幕的左下角的世界坐标值
        Debug.Log("1:" + Camera.main.ScreenToWorldPoint(new Vector3(0.0f, 0.0f, 100.0f)));
        //Z轴前方100处对应的屏幕的中间的世界坐标值
        Debug.Log("2:" + Camera.main.ScreenToWorldPoint(new Vector3(Screen.width / 2.0f, Screen.height / 2.0f, 100.0f)));
        //Z轴前方100处对应的屏幕的右上角的世界坐标值
        Debug.Log("3:" + Camera.main.ScreenToWorldPoint(new Vector3(Screen.width, Screen.height, 100.0f)));
    }
}
```

- ViewPortToWorldpoint 视口坐标点向世界坐标点转换。

> 此方法的返回值受当前摄像机在世界坐标系的位置Camera的FieldOfView的值以及Position的共同影响



```cpp
ublic class ViewportToWorldPoint : MonoBehaviour
{
    void Start()
    {
        transform.position = new Vector3(1.0f, 0.0f, 1.0f);
        Camera.main.fieldOfView = 60.0f;
        Camera.main.aspect = 16.0f / 10.0f;
        //屏幕左下角
        Debug.Log("1:" + Camera.main.ViewportToWorldPoint(new Vector3(0.0f, 0.0f, 100.0f)));
        //屏幕中间
        Debug.Log("2:" + Camera.main.ViewportToWorldPoint(new Vector3(0.5f, 0.5f, 100.0f)));
        //屏幕右上角
        Debug.Log("3:" + Camera.main.ViewportToWorldPoint(new Vector3(1.0f, 1.0f, 100.0f)));
    }
}
```

- WorldToScreenPoint：世界坐标转成屏幕坐标。

```cpp
public class WorldToScreenPoint : MonoBehaviour
{
    public Transform cb, sp;
    public Texture2D t2;
    Vector3 v3 = Vector3.zero;
    float sg;
    void Start()
    {
        //记录屏幕高度
        sg = Screen.height;
    }
    void Update()
    {
        //sp绕着cb的Y轴旋转
        sp.RotateAround(cb.position, cb.up, 30.0f * Time.deltaTime);
        //获取sp在屏幕上的坐标点
        v3 = Camera.main.WorldToScreenPoint(sp.position);
    }
    void OnGUI()
    {
        //绘制纹理
        GUI.DrawTexture(new Rect(0.0f, sg - v3.y, v3.x, sg), t2);
    }
}
```

- WorldToViewPoint：从世界坐标转换屏幕单位化坐标中。

```cpp
public class WorldToViewportPoint : MonoBehaviour
{
    public Transform cb, sp;
    public Texture2D t2;
    Vector3 v3 = Vector3.zero;
    float sw, sh;
    void Start()
    {
        //记录屏幕的宽度和高度
        sw = Screen.width;
        sh = Screen.height;
    }
    void Update()
    {
        //物体sp始终绕cb的Y轴旋转
        sp.RotateAround(cb.position, cb.up, 30.0f * Time.deltaTime);
        //记录sp映射到屏幕上的比例值
        v3 = Camera.main.WorldToViewportPoint(sp.position);
    }
    void OnGUI()
    {
        //绘制纹理，由于方法WorldToViewportPoint的返回值的y分量是从屏幕下方向上方递增的，
        //所以需要先计算1.0f - v3.y的值，然后再和sh相乘。
        GUI.DrawTexture(new Rect(0.0f, sh * (1.0f - v3.y), sw * v3.x, sh), t2);
    }
}
```

- SetTargetBuffers:重设摄像机到TargetTexture的渲染

> SetTargetBuffers(RenderBuffer colorBuffer,RenderBuffer depthBuffer)其中参数ColorBuffer为纹理颜色缓存，depthBuffer为纹理的深度缓存。

> SetTargetBuffers(RenderBuffer [] colorBuffer,RenderBuffer depthBuffer)其中参数ColorBuffer为纹理颜色缓存，depthBuffer为纹理的深度缓存,这个方法可以将摄像机的渲染一次赋给多个colorBuffer.



```cpp
public class SetTargetBuffers : MonoBehaviour
{
    //声明两个RendererTexture变量
    public RenderTexture RT_1, RT_2;
    public Camera c;//指定Camera

    void OnGUI()
    {
        //设置RT_1的buffer为摄像机c的渲染
        if (GUI.Button(new Rect(10.0f, 10.0f, 180.0f, 45.0f), "set target buffers"))
        {
            c.SetTargetBuffers(RT_1.colorBuffer, RT_1.depthBuffer);
        }
        //设置RT_2的buffer为摄像机c的渲染，此时RT_1的buffer变为场景中Camera1的渲染
        if (GUI.Button(new Rect(10.0f, 60.0f, 180.0f, 45.0f), "Reset target buffers"))
        {
            c.SetTargetBuffers(RT_2.colorBuffer, RT_2.depthBuffer);
        }
    }
}
```

## 应用实例

![企业微信截图_f4c33210-6f3a-4c09-b711-e773ec9f2ba6](/Users/liuyingbo/Library/Containers/com.tencent.WeWorkMac/Data/Library/Application Support/WXWork/Temp/ScreenCapture/企业微信截图_f4c33210-6f3a-4c09-b711-e773ec9f2ba6.png)

![image-20220329232239750](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192034641.png)

通过两个相机组合，一个透视相机一个正交相机，设置遮挡剔除实现3D麻将牌桌效果。
