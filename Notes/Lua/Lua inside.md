### 1. [table源码解析](https://blog.csdn.net/y1196645376/article/details/94348873)

- 由array和hash两部分组成（hash和array都是C数组）。当hash部分满了lastfree不能获取空节点）之后（会触发rehash，此时会根据table里存储的整形key的个数确定array的大小，然后根据array大小和key的总个数重新计算hash的大小。所以当要创建非常多的小sizetable时，应该预先填充好表的大小，否则会对性能造成非常大的影响。（注意：rehash后两者的大小不一定会改变，有可能rehash后部分元素从hash部分移到array部分）

  ![image-20231128113156152](..\..\Resource\image-20231128113156152.png)

  ![image-20231128113218546](..\..\Resource\image-20231128113218546.png)

- 为什么不直接用hash表呢，为什么要用hash+array？看完源码的数据结构后，我总结有这样的实现有以下优势：数组部分的内存开销比hash部分小，因为hash的node里要存储key的类型、key的值、next(int型)以及TValuefields（存储value），而数组部分只存储TValue。

![image-20240329115640521](..\..\Resource\image-20240329115640521.png)

- table元素的删除是通过table[key] = nil来实现的，然而通过我们上面对luaH_set介绍我们可以知道，仅仅是把把key对应的val设置为nil而已，并没有真正的从table中删除这个key，只有当插入新的key或者rehash的时候才可能会被覆盖或者清除。


### 2. [性能优化](https://blog.51cto.com/u_6871414/5896881)

```lua
--比如
point={x=0,y=0}
--而不是
point={}, point.x = posx, point.y = posy
```

```
1. 在写Lua代码时，应该尽量使用local变量。比如，使用local变量sin来保存 math.sin
2. 当需要创建非常多的小size的表时，应预先填充好表的大小，以避免rehash
3. 在字符串连接中，应避免使用..。应用table来模拟buffer，然后concat得到最终字符串。
4. 在循环中，要注意实例的创建，把在循环中不变的东西放到循环外来创建
5. 在存储数据到table中时，尽量使用数组的数据结构，可以减少内存占用。
6. 如果无法避免创建新对象，优先考虑重用旧对象来节省创建的开销。
7. 将性能瓶颈部分用C/C++来写。
```

### 3. #取table的长度

从一个很大的数开始二分查找，判断是不是nil，是nil就向下二分，不是就向上二分。因此当table里全是map时也是能取出长度的

![image-20240328171621784](..\..\Resource\image-20240328171621784.png)

#### 4. iparis与pairs

看源码，ipairs是从i = 0开始，每次查找t[i+1]的值，如果值为nil则停止遍历。而pairs会调用next函数，根据next函数的返回值去遍历整个table（包括数组部分和hash部分的所有内存）。因此ipairs效率会比pairs高。
