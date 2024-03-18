首先是关于Lua如何调用C++函数：

1、在C++里开启一个Lua虚拟栈， 并将C++函数注册进Lua环境的全局表中

2、lua里通过全局表找到对应的函数，进行调用



但是我们项目里调用C++函数却是这样：

```lua
local ECSHexMapWorldWrapper = ECSHexMapWorldWrapper.get()	-- 1
ECSHexMapWorldWrapper:UpdateArmyLine(lineid, l_tempArmyLine)	-- 2
```

疑问：为什么lua的局部变量能够直接调用UpdateArmyLine函数。按理来说使用方法应该是：

```lua
-- 先查找全局表ECSHexMapWorldWrapper，然后调用全局表的UpdateArmyLine函数
local ECSHexMapWorldWrapper = ECSHexMapWorldWrapper.get()	-- 1
ECSHexMapWorldWrapper.UpdateArmyLine(ECSHexMapWorldWrapper, lineid, l_tempArmyLine)	-- 3
```



带着疑问看代码后简单总结：公司的wlua在C++与lua之间进行了进一步的封装。

**1. 封装作为返回值的C++类**

代码1中的get函数(以及所有返回值为类的C++函数)的返回值其实并不是一个类，而是统一的wlua里的结构体LuaUObjectUserData的指针，如下：

![image-20230918114918028](..\Resource\image-20230918114918028.png)

该结构体里保存的指针uobj才是真正的C++类指针：

![image-20230918115019980](..\Resource\image-20230918115019980.png)

**2. 第二个封装**

在注册C++函数时，wlua将具有继承关系的C++类对应的函数库串联了起来，比如UserWidget函数库的元表是Widget函数库(因为二者对应的C++类分别为UserWidget和Widget，为继承关系)。然后在返回userdata的时候查找该C++类对应的函数库，将该函数库设置为userdata的元表。因此在lua中便可以直接通过userdata来调用C++中的函数。表现上给人的感觉像是lua保存了C++的类。