---
title: "Unity-全局屏幕点击特效"
date: 2022-03-14T23:11:45+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192048985.png
---

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192050977.gif)

## 准备

首先canvas的设置调为camera模式

**Screen Space -OverLay**:只显示所创建的canvas中的内容，即只渲染画布。

**Screen Space-Camera**：只显示相机所渲染的区域，当保证camera的projection是perspective（透视模式）而不是（orthogonality）正交模式的时候通过调整canvas中image或其他物体的Rotation可以制作3D立体效果的UI，但是这种模式下不可以更改Canvas的ReactTransform,只能通过相机来调整canvas的显示位置和显示大小。

**World Space**：canvas的ReacTransform完全可以编辑并且把canvas完全当成一个普通的类似于Cube的物体在场景中进行渲染，仔细考虑一下这种模式，如果你把canvas赋给游戏人物，那么你会发现这种模式适合给人物做血条或者在场景中一直在游戏人物的头顶显示人物名称。

![image-20220314231710281](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192050747.png)

## Unity坐标

### 世界坐标：

场景中非子物体的transform组件的坐标。Unity中的通过transform.position获取到的坐标即为世界坐标，注意:Inspector上点击子物体显示的transform上的坐标值是局部坐标，通过transform.localposition获取。

### 屏幕坐标：

屏幕坐标即当前整个游戏画面分辨率为主创建的坐标系，画面左下角为原点(0,0)，宽高根据分辨率而定，如分辨率为1920X1080，则宽Screen.width = 1920，Screen.height = 1080.

### 视口坐标：

将屏幕坐标normalized化，x值 = 1/Screen.width,y值 = 1/Screen.height。，即原点变不变依然为(0,0),宽高则为(1,1)。

### UI坐标：

以UI上的RectTransform的width和height为宽高，根据锚点不同，原点也不一样。

### 转换：

> 1.屏幕转世界坐标
>  Vector3 Camera.main.ScreenToWorldPoint(newVector3(screenPos.x,screenPos.y,zInfo));

> 2.世界转屏幕坐标
>  Vector3 Camera.main.WorldToScreenPoint(newVector3(worldPos.x,worldPos.y,worldPos.z));

> 3.世界转视口坐标
>  Vector3 Camera.main.WorldToViewportPoint();

> 4.视口转世界坐标
>  Vector3 Camera.main.ViewportToWorldPoint(newVector3(viewPortPos.x,viewPortPos.y,zInfo));

> 5.视口转屏幕坐标
>  Vector3 Camera.main.ViewportToScreenPoint();

> 6.屏幕转视口坐标
>  Vector3 Camera.main.ScreenToViewportPoint();

> 7.屏幕转UI坐标:这个比较特殊，如果UI宽高和屏幕宽高一样，那么可以不用转换，直接通过屏幕坐标根据锚点赋值即可。其他情况下：
>  RectTransformUtility.ScreenPointToLocalPointInRectangle
>  (RectTransform rect,
>  Vector2 screenPoint,
>  Camera cam,
>  outVector2 localPoint);

rect表示该UI的父物体的组件，
 screenPoint表示屏幕坐标，
 cam表示当前使用的相机(如果Canvas是Screen Space-overlay模式，cam参数应为null),
 localPoint则是输出的UI的局部坐标。
 这个函数是根据父物体的坐标系来计算出当面屏幕坐标应该转换为的值，该值是相对父物体而言的局部坐标。

## 实现

 ```c#
 using UnityEngine;
 
 public class FXContainer : MonoBehaviour
 {
 
     Vector2 point;
     public Transform parent;
     public GameObject effect;
     public Canvas UICanvas; //你所使用的UICanvas
     [Range(0, 5.0f)]
     public float desTime = 1.0f;
 
     private void Update()
     {
         if (Input.GetMouseButtonDown(0))
         {
         	//将鼠标点击的屏幕坐标转换为UI坐标，最后一个输出参数为转换的点
             RectTransformUtility.ScreenPointToLocalPointInRectangle(UICanvas.transform as RectTransform,Input.mousePosition, UICanvas.worldCamera, out point);
 
             GameObject go = Instantiate(effect, parent);
             go.GetComponent<RectTransform>().anchoredPosition = point;
             Destroy(go, desTime);
         }
     }
 }
 ```

主要就是靠RectTransformUtility.ScreenPointToLocalPointInRectangle获取鼠标点击的UI坐标，如果想优化的话可以写个对象池，扩展可以实现粒子效果跟随鼠标,图片跟随鼠标等

