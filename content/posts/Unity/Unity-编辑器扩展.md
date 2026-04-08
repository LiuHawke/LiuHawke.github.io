---
title: "Unity-编辑器扩展"
date: 2022-01-29T23:13:39+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192048985.png
---

## **一、菜单栏拓展**

### Menultem

```
[Menultem("Test/CreatePanel/func1 %_Q"),true,1]
```

1、路径+快捷键，是否启用，排列顺序

2、配合Selection.Object选中物体文件夹时启用

3、方法必须是静态方法

```
[MenuItem("Test/OpenPanel %_Q",true)]
public static bool IsSelected()
{
 if (Selection.objects.Length > 0)
 return  true;
 else 
 return false;
}
[MenuItem("Test/OpenPanel %_Q",false)]
public static void OpenPanel()
{
Undo.DestroyObjectImmediate(object);
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049785.png)

![img](https://cdn.jsdelivr.net/gh/liuyingbor/imgHosting/img/2522637-20210918100911449-1331448070.png)

4、快捷键

|        符号        |  字符   |
| :----------------: | :-----: |
|         %          |  Ctrl   |
|         #          |  Shift  |
|         &          |   Alt   |
| LEFT/RIGHT/UP/DOWN | 方向键  |
|       F1-F12       | F功能键 |
|         _g         |  字母g  |

------

## **二、鼠标右键拓展**

### CONTEXT

```
[MenuItem("CONTEXT/组件名/方法名（按钮名）")]
```

方法前加这句特性，可以在组件的鼠标右键菜单中添加相应的按钮方法，方法必须有参数MenuCommand

```
[MenuItem("CONTEXT/Rigidbody/Clear")]
static void ClearMassAndGravity(MenuCommand cmd)
{
    Rigidbody rgd = cmd.context as Rigidbody;
    rgd.mass = 0.1f;
    rgd.useGravity = false;
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049579.png)

### Assets

```
[MenuItem("Assets/一级目录/二级目录",default/fasle, 显示优先级)]
```

Assets面板中添加鼠标右键快捷操作；

```
[MenuItem("Assets/aaa/bbb",default,1)]
public static void TestFunc2()
{
    Debug.Log("aaa");
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049522.png)

### ContextMenu

1.ContextMenu("选项名")——脚本添加右键菜单按钮；

2.ContextMenuItem（"字段名"，"方法名"）+字段+方法——脚本属性添加右键按钮；

自行尝试，不赘述了，都可给脚本添加鼠标右键菜单栏

------

## **三、创建面板**

1、固定大小

```
EditorWindow.GetWindow<T>(面板类型，是否可浮动,"Title",聚焦窗口)
```

2、自定义大小

```
EditorWindow.GetWindowWithRect<T>(rect,可浮动,"Title",聚焦窗口)
```

T需要继承EditorWindow

```
[MenuItem("Test/OpenPanel %_Q")]
public static void OpenPanel()
{
    Rect wr =new Rect(0,0,300,700);
    //TestPanel win = EditorWindow.GetWindow<TestPanel>(false, "UGUIConfig", false);
    TestPanel win = EditorWindow.GetWindowWithRect<TestPanel>(wr, false, "UGUIConfig", false);
    win.Show();
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049308.png)

------

## **四、Insptector界面拓展**

在Example脚本的insptector界面添加执行按钮

```
public override void OnInspectorGUI()
{
    base.OnInspectorGUI();
    Example _example = target as Example;
    if (GUILayout.Button("执行Example方法"))
    {
        _example.LogError();
    }
}
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049312.png)

------

## **五、GUILayout**

**1.布局**

GUILayout.BeginVertical

GUILayout.EndVertical

GUILayout.BeginHorizontal

GUILayout.EndHorizontal

GUILayout.Option[]——以下都属于该分类，自定义布局大小

GUILayout.Width,GUILayout.Height,GUILayout.MaxWidth/MinWidth,GUILayout.ExpandWidth...

**2.控件**

GUILayout.Button("name",option)

GUILayout.SelectionGrid(gridId,new []{"","","",""},每行个数)

GUILayout.PasswordField(string,'*')

------

## 六、**EditorGUILayout**

**1.控件**

ObjectField

IntField

FloatField

LabelField

TextField

TextArea

ColorField

CurveField

EnumFlagsField

LayerField

TagField

BoundsField

Vector2Field

**2.滑条**

Slider

MinMaxSlider

**3.布局**

BeginFadeGroup(float) 百分比

EndFadeGroup

BeginToggleGroup("name",bool) 开关

EndToggleGroup

BeginScrollView(vector2,option) 位置，滚动区域大小

EndScrollView

**4.下拉框**

EnumPopup(EnumIndex)

Popup(index,string[])

```
e.g:
GUILayout.BeginVertical();

GUILayout.BeginHorizontal();
font = EditorGUILayout.ObjectField(font, typeof(Font), false) as Font;
fontSize = EditorGUILayout.IntField(fontSize);
GUILayout.EndHorizontal();

EditorGUILayout.LabelField("计时: ",EditorApplication.timeSinceStartup.ToString());

floatValue = EditorGUILayout.FloatField("浮点数", floatValue);

num = EditorGUILayout.Slider ("Slider", num, -3, 3);
//IntSlider
EditorGUILayout.BeginFadeGroup(1f); // 组开始
GUILayout.Button("Button0"); // 组中的内容，我这写了3个按钮
GUILayout.Button("Button1");
GUILayout.Button("Button2");
EditorGUILayout.EndFadeGroup(); // 组结束

toggleOpen = EditorGUILayout.BeginToggleGroup("Toggle", toggleOpen); // 组开始
_color= EditorGUILayout.ColorField("Color", _color); // 组中的内容
str = EditorGUILayout.TextField("Text", str);
EditorGUILayout.EndToggleGroup();

_scrollPos = EditorGUILayout.BeginScrollView(_scrollPos,GUILayout.MaxHeight(60)); // 组开始
EditorGUILayout.LabelField("1212 12 312 1244124"); // 组中的内容
EditorGUILayout.LabelField("123123123123123");
EditorGUILayout.TextArea("12312312\n 3123123");
EditorGUILayout.LabelField("123123123123123");
EditorGUILayout.LabelField("123123123123123");
EditorGUILayout.LabelField("123123123123123");
EditorGUILayout.LabelField("123123123123123");
EditorGUILayout.EndScrollView(); // 组结束

_animationCurve = EditorGUILayout.CurveField("AnimationCurve", _animationCurve);

gridId = GUILayout.SelectionGrid(gridId, new[] {"1", "2", "3","4","5","6"}, 3);

GUILayout.BeginHorizontal();
EditorGUILayout.LabelField("密码：",GUILayout.Width(50));
password = GUILayout.PasswordField(password, '*');
GUILayout.EndHorizontal();

EditorGUILayout.MinMaxSlider("MinMaxSlider",ref sliderValue2,ref sliderValue3,10,20);

flagtype=(flagTestType)EditorGUILayout.EnumFlagsField(flagtype);

popupindex=(flagTestType)EditorGUILayout.EnumPopup(popupindex);

layer = EditorGUILayout.LayerField("LayerField", layer);
tag = EditorGUILayout.TagField("TagField", tag);
go = EditorGUILayout.ObjectField("ObjectField", go, typeof(GameObject), true);
EditorGUILayout.Space();

boundsv=EditorGUILayout.BoundsField("BoundsField",boundsv);

//EditorGUILayout.PropertyField();

index =EditorGUILayout.Popup(index, options);
if(GUILayout.Button("Create"))
    InstantiatePrimitive();

p1 =EditorGUILayout.Vector2Field("Point 1:", p1);

GUILayout.EndVertical();

 复制 全屏
```

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049875.png)
