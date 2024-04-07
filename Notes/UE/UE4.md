#### 1. 常用接口

```C++
// 创建组件
FPCameraComponent = CreateDefaultSubobject<UCameraComponent>(TEXT("FPCamera"));
// 创建widget
MyUserWidget* myWidget = CreateWidget<UUserWidget>(GetWorld(), MyUserWidget);
// 组件之间设置父子关系
FPMesh->SetupAttachment(FPCameraComponent);
// 获取当前的UWorld
GetWorld()
// 获取UGameInstance
GetWorld()->GetGameInstance()
// 获取UEnum的值对应的string
UEnum::GetValueAsString(GetLocalRole())
```

#### 2. 关于`StaticClass`、`GetClass`和`ClassDefaultObject`

​		首先贴两行C#代码([参考文章](https://blog.csdn.net/j756915370/article/details/117913118))

```c#
string s = "Muyinan的学习笔记";
Type t = s.GetType();
```

​		`GetClass`类似于GetType，而UE的`UClass`则类似于C#的Type，这其实是一种**反射机制**(反射的作用就是在不知道这个类是什么类的情况下获取到它的一些信息)。由于C++中没有反射机制，所以UE底层实现了自己的一套反射机制，`GetClass`便是UE反射的一个具体运用。

- **`StaticClass`和`GetClass`的区别在于：**

​	`GetClass`由一个`UObject`的实例调用`AMyActor->GetClass()`返回值是`UClass`，当无法获得类的实例但又想获得这个类的`UClass`时便可以调用`AMyActorClass::StaticClass()`

- **`ClassDefaultObject`，得到`UClass`的类默认对象，具体用法如下：**

```C++
AMyActor->GetClass()->GetDefaultObject<AMyActor>()			// 从对象构造默认对象
AMyActorClass::StaticClass()->GetDefaultObject<AMyActor>()	// 从类名构造默认对象
```

#### 3. UMG与C++的交互

​		首先创建继承自`UUserWidget`的C++类，然后在编辑器里创建一个UserWidget的蓝图类，并在该蓝图类的ClassSettings里设置其父类为之前创建的C++类，这样就绑定了C++类与蓝图类，接下来只需要在C++里获取到UMG控件即可。

- 将C++变量对应的UMG控件绑定

```C++
// 获取控件方法一：GetWidgetFromName，优点是控件不需要设置为成员变量，随时用随时声明
	UButton* Button1 = (UButton*)GetWidgetFromName(TEXT("Button1"));
// 获取控件方法二: 反射绑定, 变量名和UMG里的控件名要一致，优势在哪我也不知道。。。
	UPROPERTY(Meta = (BindWidget))
		UButton* Button1;
// 获取控件方法三：强转子集，优点在于不需要知道控件名字，能降低C++与UMG的耦合度
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

​		**注意上述两种方法绑定的函数必须带`UFUNCTION`宏修饰**，否则UE无法找到函数。具体原因应该与UE的反射机制有关。只有带`UFUNCTION`修饰的函数才会在编译时被注册进入Module(dll)里，而普通C++函数是不会进行注册的，具体细节不多赘述，详见[UE的反射机制](https://blog.csdn.net/mohuak/article/details/81913532?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)。

#### 4. UE4创建不同类的对象

- 在世界里生成`Actor`对象

```C++
FVector pos(0, 0, 0);  
AMyActor* MyActor = GetWorld()->SpawnActor<AMyActor>(pos, FRotator::ZeroRotator);
```

- 创建`Component`组件

```C++
// 创建component对象，该函数只能在构造函数中调用
UMyActorComponent* MyComponent = CreateDefaultSubobject<UMyActorComponent>(TEXT("MyComponent"));  
```

- 创建`UserWidget`界面

```C++
MyUserWidget* myWidget = CreateWidget<UUserWidget>(GetWorld(), MyUserWidget);
```

- 加载资源对象

```C++
// 加载模型、贴图等对象，使用StaticLoadObject函数 
// 在UE4中，项目中的所有资源文件，与其说是文件，更像是“静态对象”
UStaticMesh* Test_Mesh = Cast<UStaticMesh>(
    StaticLoadObject(UStaticMesh::StaticClass(), NULL, TEXT("/Game/Assets/StaticMeshes/Test_Mesh"))
    );  
UStaticMeshComponent* StaticMeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("StaticMeshComponent"));  
StaticMeshComponent->SetStaticMesh(Test_Mesh);
```

- 创建`UObject`对象

```C++
UTestObject* test = NewObject<UTestObject>();
```

#### 5. [在C++中生成蓝图对象](https://zhuanlan.zhihu.com/p/343208300)



#### 6. [C++构造函数与蓝图的构造脚本](https://blog.csdn.net/qq_43760344/article/details/121524724)

蓝图的构造脚本等同于C++里的函数OnConstruction()，在编辑器里被调用，而不是运行时调用。而C++构造函数在编译和运行时都会执行一次。

```
It is the most interesting point of the Construction Script: it is called in the editor, and not at runtime
```

#### 7. UE4C++阻止编译器优化

如果发现某段代码无法断点调试，或者调试过程中某个函数的局部变量无法显示，可能是该段逻辑被编译器优化掉了。

可以用以下命令取消对该段代码的编译优化。

```C++
PRAGMA_DISABLE_OPTIMIZATION
//code
PRAGMA_ENABLE_OPTIMIZATION
```

Ref： https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/DevelopmentSetup/VisualStudioSetup/VisualStudioTipsAndTricks/

#### 8. [软引用、同步/异步加载](https://blog.csdn.net/qq_29523119/article/details/84455486)

```C++
// 异步加载
UAssetManager::GetStreamableManager().RequestAsyncLoad(FSoftObjectPath, FStreamableDelegate) 
// 同步加载
UAssetManager::GetStreamableManager().RequestSyncLoad()
LoadObject<>() // 或者使用全局函数
```

#### 9. 获取该类的所有子类

![image-20240104120935315](..\..\Resource\image-20240104120935315.png)

#### 10. UFUNCTION宏

`BlueprintImplementableEvent`和`BlueprintNativeEvent`使用时一般都是C++中调用蓝图函数。前者无法在C++中实现函数内容。

`BlueprintCallable`是使蓝图中可调用C++函数

#### 11. [Drawcall为什么会影响性能](https://blog.csdn.net/weixin_42358083/article/details/122750617)

- **CPU和GPU并行工作的原理**

​		通过命令缓冲区使得CPU和GPU可以并行工作，命令缓冲区包含了一个命令[队列](https://so.csdn.net/so/search?q=队列&spm=1001.2101.3001.7020)，由CPU向其中添加命令，而由GPU从中读取命名，这其中添加和读取的过程是相互独立的。

- **为什么Draw Call多了会影响帧率**

​		在每次调用Draw Call之前，CPU需要向GPU发送很多内容，包括数据、状态和命令等。在这一阶段，CPU需要完成很多工作，例如检查渲染状态等。而一旦CPU完成了这些准备工作，GPU就可以开始本次的渲染。GPU的渲染能力是非常强的，往往渲染速度快于CPU提交命令的速度。也就是说，如果Draw Call的数量太多，CPU就会把大量时间花费在提交Draw Call上，造成CPU的过载，CPU都把时间花费在准备Draw Call的工作上。

#### 12. [Inside蓝图](https://blog.csdn.net/j756915370/article/details/121556800)

#### 13.  `LoadLevelInstance()`与`LoadStreamLevel()`

- `LoadLevelInstance`在`LevelStreamingDynamic.h`里，会有一个ULevelStreamingDynamic*的返回值，可以获取到加载的Level实例，从而对其进行操作。

- `LoadStreamLevel`在`GameplayStatics.h`里，是一个静态库函数，设计目的是优化游戏性能和内存。

  个人理解，前者可以生成多个level的实例，每个实例都拥有唯一id(由于生成的时候会加上`_Instance_uid`后缀，因此用`GetStreamingLevel`接口获取不到)，可以方便的对每一个level进行管理；而后者重心在于提高性能，适用于加载后就放在那不管的场景。

#### 14. rpc函数应该尽量写在需要同步的Actor内部

![image-20240328103109343](..\..\Resource\image-20240328103109343.png)
