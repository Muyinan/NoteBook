### 1. 入门知识

- 一些常用库函数

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

- Lua将除Boolen值false和nil外的所有值视为真

- and优先级高于or

- not运算符永远返回Boolen类型的值


```lua
> not nil 		--> true
> not false 	--> true
> not 0			--> false
> not not 1 	--> true
> not not nil 	--> false
```

- 整数与浮点值相同时Lua显示其相等，支持16进制


```lua
> 1 == 1.0			--> true
> 0.2e3 == 200		--> true
> 0xff				--> 255
-- 加0.0将整型转为浮点型
> 3 + 0.0			--> 3.0
```

- 当想在函数中间直接`return`时，可以加上`do return end`语句，这样就不会报错了

```lua
function foo ()
    return			-- 语法错误
    do return end	-- 正确用法
    other statements
end
```

- `goto`语句可以代替`continue`、`redo`等语句

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

- `for`实现无限循环可以用`math.huge`

```lua
for i = 1, math.huge do
	--something
end
```

- 自己写的遍历table的函数

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

### 2. 其它

- `require`函数

```lua
对于路径"package.path = ./?.lua;/usr/local/lua/?.lua;/usr/local/lua/?/init.lua"
调用require "a.b"会尝试打开以下文件
./a/b.lua
/usr/local/lua/a/b.lua
/usr/local/lua/a/b/init.lua

require只对路径里的分号";"和问号"?"进行操作，将"a.b"替换掉"?"，同时将"a.b"里的"."用文件路径里的"/"替换掉，然后在处理过后的路径里寻找改文件

若require一个文件夹，则相当于require该文件夹下的init.lua文件
```
