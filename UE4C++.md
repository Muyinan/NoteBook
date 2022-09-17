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
GetGameInstance

```

