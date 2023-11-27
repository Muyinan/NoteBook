### 1. 入门知识

- **一些常用库函数**

```lua
> string.format("%s", "meng")		--> meng
-- 获取number具体的类型
> math.type(3)						--> integer
> math.type(3.0)					--> float
-- 向下取整
> math.floor(-3.3)		--> -4
-- 向上取整
> math.ceil(-3.3)		--> -3			
-- 向零取整，额外返回小数部分作为第二个结果
> math.modf(3.3)		--> 3	0.3
> math.modf(-3.3)		--> -3	-0.3
-- 随机数发生器
math.random()				-- 返回[0,1)均匀分布的伪随机实数
math.random(n)				-- 返回[1,n]内均匀分布的伪随机整数,n为整数
math.random(l, r)			-- 返回[l,r]内均匀分布的伪随机整数,lr为整数
math.randomseed(os.time())	-- 设置随机种子
```

- **Lua将除Boolen值false和nil外的所有值视为真**

- **and优先级高于or**

- **not运算符永远返回Boolen类型的值**


```lua
> not nil 		--> true
> not false 	--> true
> not 0			--> false
> not not 1 	--> true
> not not nil 	--> false
```

- **整数与浮点值相同时Lua显示其相等，支持16进制**


```lua
> 1 == 1.0			--> true
> 0.2e3 == 200		--> true
> 0xff				--> 255
-- 加0.0将整型转为浮点型
> 3 + 0.0			--> 3.0
```

- **当想在函数中间直接`return`时，可以加上`do return end`语句，这样就不会报错了**

```lua
function foo ()
    return			-- 语法错误
    do return end	-- 正确用法
    other statements
end
```

- **`goto`语句可以代替`continue`、`redo`等语句**

```lua
while condition do
    ::redo::
    if condition1 then goto continue
    else goto redo 
    end
    -- other code
    ::continue::
end
```

- **`for`实现无限循环可以用`math.huge`**

```lua
for i = 1, math.huge do
	--something
end
```

- **自己写的遍历table的函数**

```lua
local function dump(o)
	if type(o) == 'table' then
		local s = '{'
		for k, v in pairs(o) do
			if type(k) ~= 'number' then k = '"' .. k .. '"' end
			s = s .. '[' .. k .. ']' .. '=' .. dump(v) .. ','
		end
		return s .. '}'
	else
		return tostring(o)
	end
end
```

- **and or短路原则**

对于and来说,是逻辑"假”的短路规则.即如果第1个操作数是假的，则返第1个操作数,否则返回第2个操作数
对于or来说，是逻辑"真"的短路规则,即如果第1个操作数是真的，则返回第1个操作数,否则返回第2个操作数

### 2. 其它

- **`require`函数**

```lua
对于路径"package.path = ./?.lua;/usr/local/lua/?.lua;/usr/local/lua/?/init.lua"
调用require "a.b"会尝试打开以下文件
./a/b.lua
/usr/local/lua/a/b.lua
/usr/local/lua/a/b/init.lua

require只对路径里的分号";"和问号"?"进行操作，用"a.b"替换掉"?"，同时用"/"替换掉"a.b"里的"."，然后在处理过后的路径里寻找该文件

若require一个文件夹，则相当于require该文件夹下的init.lua文件
```

- **局部递归函数的定义**

```lua
-- 这种书写是错误的，当Lua语言编译函数中的add(n-1)时，局部的add尚未定义。因此这个表达式会尝试调用全局的add函数
local add = function (n)
    if n == 0 then return 0
    else return n + add(n-1)
    end
end
-- 这种书写正确
local add
add = function (n)
    if n == 0 then return 0
    else return n + add(n-1)
    end
end
-- 或者使用Lua里提供的语法糖也是正确的
local function foo(params) body end
-- 上面的语法糖表达式等价于：
local foo; foo = funciton (params) body end
```

- **可以把表当成是一个指针**

```lua
local tableA = {}
local tableB = tableA
local a = tableB
print(tableA)
print(tableB)
print(a)

-- 打印结果，地址相同
table: 0000015D569D7A40
table: 0000015D569D7A40
table: 0000015D569D7A40
```

- **pcall与xpcall的用法**

```lua
--[[
    (flag, ret) pcall(func, args...)
    参数:
    func:调用函数
    args:func所需参数，为不定长
    返回值:
    flag:调用成功与否，bool值
    ret:当flag为true时，ret为func的返回值。当flag为false时，ret为errmsg
]]

--[[
    (flag, ret) xpcall(func, errfunc, args...)
    参数:
    func:调用函数
    errfunc:错误处理函数
    args:func所需参数，为不定长
    返回值:
    flag:调用成功与否，bool值
    ret:当flag为true时，ret为func的返回值。当flag为false时，ret为errfunc的返回值
]]

```

[参考文章](https://blog.csdn.net/bleachpingzi/article/details/118445859?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EOPENSEARCH%7ERate-1-118445859-blog-121930545.pc_relevant_3mothn_strategy_recovery&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EOPENSEARCH%7ERate-1-118445859-blog-121930545.pc_relevant_3mothn_strategy_recovery&utm_relevant_index=1)

### 3. 元表metatable

- **\_\_index:** 用于查询。当访问表中不存在的字段时，解释器会去访问元表的***\_\_index表***或者调用***\_\_index方法***。

  可以用`rawget(t,i)`方法限制只访问表，而不访问元表。

- **\_\_newindex:** 用于更新。同\_\_index一样，不同的是该元方法用于赋值操作。

  可以用`rawget(t, k, v)`限制不访问元表

### 4. table.sort()

自定义比较函数cmp(a,b)返回值为false时，交换顺序，返回true不交换顺序。

注意：自定义比较函数时，如果cmp(a,b) == true，而且cmp(b,a)==ture时会报错。要么保证两者都为false，要么保证两者值不相等。

### 5. [性能优化](https://blog.51cto.com/u_6871414/5896881)

- table：是一个数组和哈希共用key的数组。当数组长度不够时会rehash，重新申请一个大小为原本数组2倍大小的数组，重新建立hash关系。所以当要创建非常多的小sizetable时，应该预先填充好表的大小，否则会对性能造成非常大的影响。

```lua
--比如
point={x=0,y=0}
--而不是
point={}, point.x = posx, point.y = posy
```

```
1. 在写Lua代码时，你应该尽量使用local变量。比如，使用local变量sin来保存 math.sin
2. 当需要创建非常多的小size的表时，应预先填充好表的大小，以避免rehash
3. 在字符串连接中，我们应避免使用..。应用table来模拟buffer，然后concat得到最终字符串。
4. 在循环中，我们更需要注意实例的创建。我们应该把在循环中不变的东西放到循环外来创建
5. 在存储数据到table中时，尽量使用数组的数据结构，可以减少内存占用。
6. 如果无法避免创建新对象，我们需要考虑重用旧对象。
7. 将性能瓶颈部分用CIC++来写。
```

