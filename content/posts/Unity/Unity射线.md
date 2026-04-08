---
title: "Unity-射线"
date: 2022-03-01T22:48:15+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---

# Unity射线系统

## Demo展示

UI+Physical射线测试：

![12123123](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192023267.gif)

FPS自定义射线测试：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192023524.gif)

## UGUI射线工具

实现功能，鼠标点击UI,返回鼠标点击的UI对象；

需要使用到鼠标点击事件-PointerEventData；

关键API:EventSystem.current.RaycastAll();

参数为鼠标点击事件，和接受射线返回结果集合；

```c#
public static GameObject RaycastUI()
{
    if (EventSystem.current == null)
        return null;
    //鼠标点击事件
    PointerEventData pointerEventData = new PointerEventData(EventSystem.current);
    //设置鼠标位置
    pointerEventData.position = Input.mousePosition;
    //射线检测返回结果
    List<RaycastResult> results = new List<RaycastResult>();
    //检测UI
    EventSystem.current.RaycastAll(pointerEventData, results);
    
    //返回最上层ui
    if (results.Count > 0)
        return results[0].gameObject;
    else
        return null;
}
```

## Physcial射线工具

从摄像机发射射线，方向为，摄像机——鼠标位置；

可以获取射线碰撞到的3D物品的大部分信息：

可以活着hit.collider；意味着可以获取碰撞点的位置，物体等信息；

用来做鼠标点击地面控制人物位移；

```c#
public static GameObject RaycastPhysical()
{
    Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
    RaycastHit hit;
    bool isHit = Physics.Raycast((Ray) ray, out hit);
   
    if (isHit)
    {
        Debug.Log(hit.collider.name);
        return hit.collider.gameObject; //检测到碰撞，就把检测到的点记录下来
    }

    return null;
}
```

测试代码：

```c#
public class Test : MonoBehaviour
{
    void Update()
    {
        if (Input.GetMouseButtonUp(0))
        {
            GameObject temp = RayCastTool.RaycastUI();
  
            if (temp.CompareTag("Pic"))
            {
                temp.GetComponent<Image>().color = Color.red;
            }
        }

        if (Input.GetMouseButtonUp(1))
        {
            GameObject temp = RayCastTool.RaycastPhysical();
            
            temp.GetComponent<Renderer>().material.color = Color.red;
        }
    }
}
```

## FPS射线测试

自定义射线的起始点Origin，方向，以及射线长度；

获取射线碰撞点的位置物体信息;

用来做第三人称FPS的射击判定，或者RPG远程技能判定；

第一人称FPS，射线起始点和方向，替换成相机——屏幕中心瞄准心；

```c#
public class TestRayCast : MonoBehaviour
{
    private Transform player;
    void Start()
    {
        player = GameObject.FindGameObjectWithTag("Player").transform;
    }
    
    void Update()
    {
        Ray ray = new Ray(player.position, player.forward);
        RaycastHit hit;
        bool isHit = Physics.Raycast((Ray) ray, out hit,10);

        Debug.DrawRay(player.position, player.forward*10, Color.blue);
        if (isHit)
        {
            if (hit.collider.CompareTag("Enemy"))
                hit.collider.GetComponent<Renderer>().material.color = Color.red;
        }
    }
}
```
