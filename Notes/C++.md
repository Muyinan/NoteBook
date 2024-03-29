#### 一、关于C++内存地址与指针

1. 输出指针的内容(即所指向的内容的地址)时需要先将指针转为`void*`型，才能`cout`打印

2. 两指针可以相减得到指针内存地址的差值，但是在这之前得先转换成同类型的指针，比如：

   ```C++
   char carr[5]{'a', 'b', 'c', 'd', 'e'};
   int iarr[5]{ 1,2,3,4,5 };
   
   // 先同时转换为char*类型再进行减法，由于char占一个字节，所以结果是4
   cout << (char*)(carr + 4) - (char*)carr << endl; -->		4
   
   // 也可以转换成别的指针类型，如下转为int*后结果变为1
   cout << (int*)(carr + 4) - (int*)carr << endl; 	 -->		1
   ```
   
   - 假设转换的类型为`T*`，那么`(T*)pa - (T*)pb`的结果为`pb`与`pa`指针所指向的内存地址之间所能存储的`T`的个数(***向下取整***)。
   - 不同数组指针之间进行这个操作没有太大意义，比如在上述代码之后执行`cout << (int*)iarr - (int*)arr << endl;`用会得到结果x86跑会得到结果-7，x64则会得到9，与编译器自己的内存管理有关。
   - 该技巧在鸿图协议发送二进制数据里有涉及，鸿图用base和high两个指针指向二进制数据的头和尾，使用`memcpy`等内存函数来修改二进制协议长度。
   
   #### 二、关于union的用法
   
   公司底层对union的用法，叹为观止，下面函数将x的比特位进行倒序操作，然后返回倒序后的数值

![image-20230116163617468](..\Resource\image-20230116163617468.png)

#### 三、[C++编译](https://blog.csdn.net/u012617944/article/details/78405686)

C++编译时只会编译.cpp和.cpp引用的.h文件，如果有.h文件没有被任何一个.cpp文件引用，那么该.h文件不会被编译，原因如下：

```
#include的作用是把它后面所写的那个文件的内容，完完整整地、 一字不改地包含到当
前的文件中来。简单的文本替换，别无其他。
```

```C++
extern int a；// 只是声明！没有赋值
int a;// 默认赋值0！并且缺省为extern的
```

#### 四、[C++引用和指针](https://www.zhihu.com/question/37608201/answer/1601079930)

- 引用只是c++语法糖，可以看作编译器自动完成取地址、解引用的常量指针。引用区别于指针的特性都是编译器约束完成的，一旦编译成汇编就和指针一样。

- **强弱智能指针的一个重要应用规则：定义对象时，用强智能指针shared_ptr，在其它地方引用对象时，使用弱智能指针weak_ptr。**
- [enable_shared_from_this<T>()模板类](https://blog.csdn.net/breadheart/article/details/112451022)

#### 五、[右值引用](https://www.cnblogs.com/qicosmos/p/4283455.html)

std::move只是将左值转化成了右值(转换了资源的所有权)，几乎没有性能消耗。当左值转化成了右值之后，便可以匹配到类的移动构造函数，以此来节省性能。

#### 六、[浮点数的存储方式](https://blog.csdn.net/qq_35904259/article/details/128330739)

一个浮点数由3部分组成：符号位s(1位)和、指数e(8位)、底数m(23位)，以17.625为例

1. 将整数部分17转为二进制（用2去除，取余数部分），得到10001
2. 将小数部分0.625转为二进制（用2去乘，取整数部分），得到101
3. 将10001.101右移直至小数点前只剩1位，得到1.0001101*2^4
4. 得到
   - 符号位：0
   - 底数：0001101（由于小数点前必定为1，所以只记录小数点后的数字即可）
   - 指数：131，即10000011（4 + 127，加上127是IEEE标准）

得到最终在内存中的存储内容是：01000001 10001101 00000000 00000000