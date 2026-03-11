---
title: "Unity-移动端屏幕适配"
draft: true

tags:
  - Unity
  - 移动平台
  - Android

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242151718.jpg
---

# 开始之前的重要信息

本教程的标签之⼀是初学者，但这不是完全初学者的教程。

虽然您不必是专业的 C# 编码⼈员，但我希望您熟悉 C#，以便您可以根据⾃⼰的需要编辑脚本。



# 如何修复不同屏幕尺⼨的差距

移动游戏开发的⼀个⼤问题是游戏中的空⽩空间和资产之间的差距。在⼤多数情况下，这是游戏背景和空⽩区域的问题。

例如，让我们看⼀下这个场景：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301902387.jpg) 

如您所⻅，这是“游戏”选项卡的屏幕截图，该场景使⽤的参考分辨率是全⾼清，例如

1920×1080。

从上图中我们可以看到设置没有任何问题，⼀切都在它应该在的地⽅。但是，这是 Unity

编辑器的预览，让我们来看看游戏在真实设备上的样⼦。

这是在分辨率为 1920×1080 的 iPhone 6 Plus 上预览游戏的效果：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301902958.jpg) 

这是游戏在分辨率为 1334×750 的 iPhone 7 上的样⼦：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301902310.jpg)

 

这是游戏在分辨率为 1280×720 的三星 Galaxy A5 上的样⼦：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301903830.jpg)



 

如您所⻅，我们的游戏在 Unity 编辑器和上述设备上看起来是⼀样的。这⾥的问题是我们只在分辨率为1920×1080或以下的设备上测试了游戏，让我们来看看我们的游戏在分辨率更⾼的设备上的表现。

这是游戏在分辨率为 2436×1125 的 iPhone 11 Pro 上的样⼦：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301903901.jpg)

 

⻢上，您会注意到⼿机的左右两侧有缝隙。背景不够⻓，⽆法填补这些空⽩，这就是为什么您会看到空⽩区域或 Unity 相机的蓝⾊。

接下来，这就是游戏在分辨率为 3120×1440 的华为 Mate 20 Pro 上的样⼦：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301903242.jpg)



这就是游戏在分辨率为 3040×1440 的三星 Galaxy Note 10+ 上的样⼦：

 

![](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301903136.jpg)



我们在 iPhone 11 Pro 上遇到了同样的问题，在华为 Mate 20 Pro 和三星 Galaxy Note 10+ 上也遇到了同样的问题。当然，在其他⼿机上我们也会遇到同样的问题。

那么解决⽅法是什么？

# 缩放背景以适合所有屏幕尺⼨

正如我在本文开头所说的那样，在创建移动游戏时，最困难的任务是让您的游戏在所有屏幕尺⼨上看起来都⼀样。

因为有如此多的设备具有不同的屏幕尺⼨和纵横比，所以很难让您的游戏在所有这些屏幕和纵横比上看起来都⼀样，但当然，解决这个问题并非不可能。

我们解决此问题的⽅法之⼀是使⽤⼀个脚本，该脚本将根据设备的屏幕宽度和⾼度缩放背景：

 

```c#
using UnityEngine;
public class ScaleToFitScreen : MonoBehaviour{
    private SpriteRenderer sr;
    private void Start()
    {
        sr = GetComponent<SpriteRenderer>();
        // world height is always camera's orthographicSize * 2
        float worldScreenHeight = Camera.main.orthographicSize * 2;
        // world width is calculated by diving world height with screen heigh
        // then multiplying it with screen width
        float worldScreenWidth = worldScreenHeight / Screen.height * Screen.width;
        // to scale the game object we divide the world screen width with the
        // size x of the sprite, and we divide the world screen height with the
        // size y of the sprite
        transform.localScale = new Vector3(
        }
 } // class
```

要了解脚本在做什么，我们需要了解屏幕宽度和⾼度以及世界宽度和⾼度之间的差异。

当我们使⽤ Screen.width 或 Screen.height 时，我们会得到设备屏幕的宽度和⾼度信息。因此，如果游戏在分辨率为 1920×1080 的设备上运⾏，Screen.width 将返回 1920， Screen.height 将返回 1080。

但是，如果您尝试将这些数字⽤作游戏中精灵的比例值，那么精灵看起来会很奇怪，因为它会异常调整⼤⼩。

所以我们需要做的是将屏幕分辨率的宽度和⾼度值转换为 Unity 的屏幕宽度和⾼度的单位值。我们在上述课程的第 12 ⾏和第 16 ⾏执⾏的计算就是这样做的。

要使此脚本起作⽤，您需要将其附加到场景中的背景游戏对象。让我们来看看我们的游戏现在在我们在第⼀次初始测试中有差距的相同设备上的样⼦，从分辨率为 2436×1125 的



iPhone 11 Pro 开始：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301901158.jpg)

您会注意到我们两侧的蓝⾊间隙现在消失了，因为背景正在缩放以适应设备的屏幕尺⼨，同时保持像素清晰的分辨率。

现在让我们看看我们的游戏在分辨率为3120×1440的华为Mate 20 Pro上的效果：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301907790.jpg)



同样，我们⼀开始的差距现在已经消失了。最后，这是我们的游戏在分辨率为

3040×1440 的三星 Galaxy Note 10+ 上的样⼦：



[![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301901230.jpg)](https://awesometuts.com/wp-content/uploads/2021/11/Img-10.jpg.webp)

 

不出所料，我们之前的差距现在已经消失了。但是，我们现在有⼀个不同的问题。

# 我的游戏对象在哪⾥？

为了说明我们现在遇到的问题，我将使⽤ 3040×1440 作为参考分辨率重新设计游戏。

当我说重新设计游戏时，我的意思是我将使⽤上述分辨率作为参考分辨率来安排游戏中的游戏元素。

这是游戏现在在“游戏”选项卡预览中的样⼦：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301907490.jpg)



如果我们在分辨率为 3040×1440 的 Samsung Galaxy Note 10+ 上运⾏游戏，我们将看不到游戏有什么不同：



![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301907241.jpg)

 

但是如果我们在分辨率较低的设备上运⾏游戏会发⽣什么，比如 iPhone 6 Plus，分辨率为 1920×1080？

好吧，让我们测试⼀下，看看结果：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301908509.jpg)



 

现在你会注意到侧⾯的游戏元素被剪掉了。您可以在左侧部分看到⼀个游戏元素，⽽在右侧您⼏乎看不到另⼀个游戏元素剩下的部分：



![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301908772.jpg)

 

这就是为什么你不能只使⽤参考分辨率设计你的游戏并保持原样，因为⼀旦你的游戏在比你设计游戏的分辨率更⾼或更低的分辨率下玩，⼀些游戏元素就会被从屏幕上切掉，有时屏幕的两侧会有空隙，正如我们在⽬前的⽰例中看到的那样。

那么解决⽅法是什么？

# 在屏幕上锚定游戏对象

正如您可以假设的那样，与我们解决背景差距问题的⽅式相同，我们将解决我们游戏元素的锚定问题，那就是使⽤⼀个脚本，具体来说是使⽤两个脚本。

第⼀个脚本称为 CameraViewportHandler：

 

```c#
using UnityEngine;
[ExecuteInEditMode]
[RequireComponent(typeof(Camera))]
public class CameraViewportHandler : MonoBehaviour
{ 
    public enum Constraint { Landscape, Portrait } 
    #region FIELDS 
    public Color wireColor = Color.white; 
    public float UnitsSize = 1; // size of your scene in unity units
    public Constraint constraint = Constraint.Portrait; 
    public static CameraViewportHandler Instance; 
    public new Camera camera; public bool executeInUpdate; 
    private float _width; private float _height; 
    //*** bottom screen private Vector3 _bl;
    private Vector3 _bc; private Vector3 _br;
    ////*** middle screen private Vector3 _ml;
    private Vector3 _mc; private Vector3 _mr; 
    //*** top screen private Vector3 _tl;
    private Vector3 _tc; 
    private Vector3 _tr; 
    #endregion
    #region PROPERTIES 
    public float Width { get { return _width; } } 
    public float Height { get { return _height; } } // helper points:
    public Vector3 BottomLeft { get { return _bl; } } 
    public Vector3 BottomCenter { get { return _bc; } } 
    public Vector3 BottomRight { get { return _br; } } 
    public Vector3 MiddleLeft { get { return _ml; } } 
    public Vector3 MiddleCenter { get { return _mc; } } 
    public Vector3 MiddleRight { get { return _mr; } } 
    public Vector3 TopLeft { get { return _tl; } } 
    public Vector3 TopCenter { get { return _tc; } } 
    public Vector3 TopRight { get { return _tr; } } 
    #endregion
    #region METHODS

    private void Awake()
    {
        camera = GetComponent<Camera>(); Instance = this; ComputeResolution();
    } 
    private void ComputeResolution() 
    {
        float leftX, rightX, topY, bottomY;
        if (constraint == Constraint.Landscape)
        {
            camera.orthographicSize = 1f / camera.aspect * UnitsSize / 2f;
        }
        else
        {
            camera.orthographicSize = UnitsSize / 2f;
        } 
        _height = 2f * camera.orthographicSize; 
        _width = _height * camera.aspect; 
        float cameraX, cameraY; cameraX = camera.transform.position.x; 
        cameraY = camera.transform.position.y; 
        leftX = cameraX - _width / 2; rightX = cameraX + _width / 2; 
        topY = cameraY + _height / 2; bottomY = cameraY - _height / 2; 
        //*** bottom
        _bl = new Vector3(leftX, bottomY, 0); 
        _bc = new Vector3(cameraX, bottomY, 0); 
        _br = new Vector3(rightX, bottomY, 0); 
        //*** middle
        _ml = new Vector3(leftX, cameraY, 0); 
        _mc = new Vector3(cameraX, cameraY, 0); 
        _mr = new Vector3(rightX, cameraY, 0); 
        //*** top
        _tl = new Vector3(leftX, topY, 0); 
        _tc = new Vector3(cameraX, topY, 0); 
        _tr = new Vector3(rightX, topY, 0); 
    }

    private void Update()
    {
#if UNITY_EDITOR 
if (executeInUpdate) ComputeResolution();
#endif
    }

    void OnDrawGizmos()
    {
        Gizmos.color = wireColor; 
        Matrix4x4 temp = Gizmos.matrix; 
        Gizmos.matrix = Matrix4x4.TRS(transform.position, transform.rotation, Vector3.one);
        if (camera.orthographic)
        {
            float spread = camera.farClipPlane - camera.nearClipPlane; 
            float center = (camera.farClipPlane + camera.nearClipPlane) * 0.5f; 
            Gizmos.DrawWireCube(new Vector3(0, 0, center), new Vector3(camera.orthographicSize * 2 * camera.aspect, camera.orthographicSize * 2, spread));
        }
        else
        {
            Gizmos.DrawFrustum(Vector3.zero, camera.fieldOfView, camera.farClipPlane, camera.nearClipPlane, camera.aspect);
        } 
        Gizmos.matrix = temp;
    } 
    #endregion
} // class
```

该脚本将根据游戏是横向模式还是纵向模式以及单位⼤⼩变量来计算相机的正交⼤⼩。您可以在类的检查器选项卡中设置所有这些值：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301925585.jpg)



 

单位⼤⼩将根据您的游戏⽽有所不同，因此请务必检查从 10 到 20 之间的范围开始的不同值。

您可以选中 Execute In Update 复选框以使脚本在 Update 中执⾏，同时您在 Unity 编辑器中以立即查看您所做的更改，但您将在运⾏游戏时看到真实结果。

如果我再次在 iPhone 6 Plus 上测试游戏，我们会看到同样的结果：



![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301925406.jpg)

 

这样做的原因是因为 CameraViewportHandler 将确保相机的 Orthographic Size 是有序的，⽆论我们在哪种屏幕尺⼨上玩游戏，但我们需要结合 CameraViewportHandler 使⽤另⼀个脚本来将我们的游戏对象定位在屏幕上正确。

该脚本称为 AnchorGameObject：

```c#
using UnityEngine;
using System.Collections;
[ExecuteInEditMode]
public class AnchorGameObject : MonoBehaviour
{
    public enum AnchorType
    {
        BottomLeft, 
        BottomCenter, 
        BottomRight, 
        MiddleLeft, 
        MiddleCenter, 
        MiddleRight, 
        TopLeft, 
        TopCenter, 
        TopRight,
    }; 
    public bool executeInUpdate; 
    public AnchorType anchorType; 
    public Vector3 anchorOffset; 
    IEnumerator updateAnchorRoutine; //Coroutine handle so we don't start it if it's already running
                                     
    // Use this for initialization
    void Start()
    {
        updateAnchorRoutine = UpdateAnchorAsync(); 
        StartCoroutine(updateAnchorRoutine);
    }

    /// <summary>
    /// Coroutine to update the anchor only once CameraFit.Instance is not null.
    /// </summary>
    IEnumerator UpdateAnchorAsync()
    {
        uint cameraWaitCycles = 0;
        while (CameraViewportHandler.Instance == null)
        {
            ++cameraWaitCycles; yield return new WaitForEndOfFrame();
        }

        if (cameraWaitCycles > 0)
        {
            print(string.Format("CameraAnchor found CameraFit instance after waiting {0} frame(s). " + "You might want to check that CameraFit has an earlie execution order.", cameraWaitCycles));
        } 
        UpdateAnchor(); 
        updateAnchorRoutine = null;
    }

    void UpdateAnchor()
    {
        switch (anchorType)
        {
            case AnchorType.BottomLeft: SetAnchor(CameraViewportHandler.Instance.BottomLeft); 
                break; 
            case AnchorType.BottomCenter: SetAnchor(CameraViewportHandler.Instance.BottomCenter); 
                break; 
            case AnchorType.BottomRight: SetAnchor(CameraViewportHandler.Instance.BottomRight); 
                break; 
            case AnchorType.MiddleLeft: SetAnchor(CameraViewportHandler.Instance.MiddleLeft); 
                break; 
            case AnchorType.MiddleCenter: SetAnchor(CameraViewportHandler.Instance.MiddleCenter); 
                break; 
            case AnchorType.MiddleRight: SetAnchor(CameraViewportHandler.Instance.MiddleRight); 
                break; 
            case AnchorType.TopLeft: SetAnchor(CameraViewportHandler.Instance.TopLeft); 
                break; 
            case AnchorType.TopCenter: SetAnchor(CameraViewportHandler.Instance.TopCenter); 
                break; 
            case AnchorType.TopRight: SetAnchor(CameraViewportHandler.Instance.TopRight); 
                break;
        }
    } 
    
    void SetAnchor(Vector3 anchor) {
        Vector3 newPos = anchor + anchorOffset; if (!transform.position.Equals(newPos)) { transform.position = newPos; }
    }
#if UNITY_EDITOR 
// Update is called once per frame
void Update() 
    {
        if (updateAnchorRoutine == null && executeInUpdate) {
            updateAnchorRoutine = UpdateAnchorAsync(); 
            StartCoroutine(updateAnchorRoutine);
        } 
    }
#endif
}
```

AnchorGameObject 脚本的⼯作⽅式是使⽤第 7-18 ⾏声明的 AnchorType 以及第 59-91

⾏的 CameraViewportHandler 变量来设置锚点，例如游戏对象的中⼼原点。

然后您可以使⽤第 23 ⾏声明的 anchorOffset 变量来确定特定游戏对象相对于其锚点（例如中⼼原点）的偏移量。

AnchorGameObject  脚本应附加到您要定位或锚定在屏幕上的游戏对象上。

需要记住的⼀件事是，将此组合与这 3 个脚本结合使⽤时，您需要使⽤⼀个参考分辨率作为基本分辨率来设计游戏，您将使⽤该参考分辨率来锚定游戏对象。

我将使⽤与三星 Galaxy 10+ 相同的分辨率，即 3040×1440 作为参考分辨率。

⾸先，我要将脚本附加到右侧的 Altar 1 游戏对象上，并对 AnchorGameObject 脚本使⽤以下设置：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301901078.jpg)



 

这会将对象的锚点设置为右中，并将其在 X 轴上偏移 -1.5，在 Y 轴上偏移 1，这会将游戏对象定位在此处：



![](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301934468.jpg)

 

接下来，我要将脚本附加到我的 Altar 2 游戏对象上，并为 AnchorGameObject 脚本设置以下设置：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301934529.jpg)



 

这将把游戏对象的锚点设置在左中，并在 X 轴上偏移 2，在 Y 轴上偏移 0.53，这将把游戏对象定位在这⾥：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301934696.jpg)





现在⽆论我们以何种分辨率玩游戏，这两个游戏对象都将停留在我们刚刚放置它们的位置。

在我们测试我们的新设置之前，让我们快速看⼀下在不使⽤我们的两个脚本进⾏测试时，相同设置在分辨率为 1920×1080 的 iPhone 6 Plus 上的样⼦：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301934299.jpg)

现在让我们使⽤刚刚使⽤两个新脚本创建的新设置在 iPhone 6 Plus 上测试我们的游戏：

 

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202301301934674.jpg)



现在我们的两个游戏对象并没有从屏幕上切下来，它们在游戏中清晰可⻅。

⽽且⽆论您的游戏在何种分辨率下运⾏，当您将此组合与 CameraViewportHandler 和

AnchorGameObject  脚本结合使⽤时，您的游戏对象将位于您最初放置它们的相同位置。

现在，不⾔⽽喻，这种⽅法适⽤于静态游戏，例如所有游戏玩法都发⽣在⼀个地⽅并且屏幕（例如相机）在游戏中没有移动的游戏。

如果您的游戏是动态的，例如平台游戏或塔防游戏等，您将不会遇到需要锚定游戏对象的这些问题，因为您将创建更⼤的关卡，因此⽆论摄像机在游戏中的哪个位置，它将渲染游戏那部分的关卡。
