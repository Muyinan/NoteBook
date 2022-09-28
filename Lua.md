### 1.入门知识

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

