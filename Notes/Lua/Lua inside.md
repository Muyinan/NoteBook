### 1. [table源码解析](https://blog.csdn.net/y1196645376/article/details/94348873)

- 由array和hashtable两部分组成（hash底层用链表实现，array才是用的C数组）。当数组长度或者hash部分数组的长度不够时会rehash，重新申请一个大小为原本数组2倍大小的数组，并重新建立hash关系。所以当要创建非常多的小sizetable时，应该预先填充好表的大小，否则会对性能造成非常大的影响。

  ![image-20231128113156152](..\..\Resource\image-20231128113156152.png)

  ![image-20231128113218546](..\..\Resource\image-20231128113218546.png)

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

