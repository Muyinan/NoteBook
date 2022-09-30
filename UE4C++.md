#### 1. UE4基本函数

```C++
// 创建组件
FPCameraComponent = CreateDefaultSubobject<UCameraComponent>(TEXT("FPCamera"));
// 创建widget
UUserWidget* myWidget = CreateWidget<UUserWidget>(GetWorld(), widgetChoice);
// 组件之间设置父子关系
FPMesh->SetupAttachment(FPCameraComponent);
// 获取当前的UWorld
GetWorld()
// 获取UGameInstance
GetWorld()->GetGameInstance()

```

#### 2.关于`StaticClass`、`GetClass`和`ClassDefaultObject`

首先贴两行C#代码([参考文章](https://blog.csdn.net/j756915370/article/details/117913118))

```c#
string s = "Muyinan的学习笔记";
Type t = s.GetType();
```

`GetClass`类似于GetType，而UE的`UClass`则类似于C#的Type，这其实是一种**反射机制**(反射的作用就是在不知道这个类是什么类的情况下获取到它的一些信息)。由于C++中没有反射机制，所以UE底层实现了自己的一套反射机制，`GetClass`便是UE反射的一个具体运用。

- **`StaticClass`和`GetClass`的区别在于：**

​	`GetClass`由一个`UObject`的实例调用`AMyActor->GetClass()`返回值是`UClass`，当无法获得类的实例但又想获得这个类的`UClass`时便可以调用`AMyActorClass::StaticClass()`

- **`ClassDefaultObject`**，得到`UClass`的类默认对象，具体用法如下：

```C++
AMyActor->GetClass()->GetDefaultObject<AMyActor>()			// 从对象构造默认对象
AMyActorClass::StaticClass()->GetDefaultObject<AMyActor>()	// 从类名构造默认对象
```

#### 3.UMG与C++的交互

首先创建继承于`UUserWidget`类的C++类，然后在编辑器里创建一个UserWidget的蓝图类，并在ClassSettings里设置其父类为之前创建的C++类，这样C++类与蓝图类就绑定了，接下来只需要在C++里获取到UMG控件就行了。

- 将C++变量对应的UMG控件绑定

```C++
// 获取控件方法一：GetWidgetFromName，优点是控件不需要设置为成员变量，随时用随时声明
	UButton* Button1 = (UButton*)GetWidgetFromName(TEXT("Button1"));
// 获取控件方法二: 反射绑定, 变量名和UMG里的控件名要一致，优势在哪我也不知道。。。
	UPROPERTY(Meta = (BindWidget))
		UButton* Button1;
// 获取控件方法三：强转子集，优势在于不需要知道控件名字，能降低C++与UMG的耦合度
	UCanvasPanel*  RootPanel = Cast<UCanvasPanel>(GetRootWidget());
	if (RootPanel)
	{
		UButton* Button1 = Cast<UButton>(RootPanel->GetChildAt(0));
	}
```

- C++里绑定按钮事件

```C++
// 绑定按钮事件方法一 :  FScriptDelegate
	FScriptDelegate But1Del;
	But1Del.BindUFunction(this, "OnButton1Event");
	Button1->OnClicked.Add(But1Del);
// 绑定按钮事件方法二:	__Internal_AddDynamic
	Button1->OnClicked.__Internal_AddDynamic(this, &UMyUserWidget::OnButton1Event, FName("OnButton1Event"));
```

**注意上述两种方法绑定的函数必须带`UFUNCTION`宏修饰**，否则UE无法找到函数。具体原因应该与UE的反射机制有关。只有带`UFUNCTION`修饰的函数才会在编译时被注册进入Module(dll)里，而普通C++函数是不会进行注册的，具体细节不多赘述，详见[UE的反射机制](https://blog.csdn.net/mohuak/article/details/81913532?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)。



