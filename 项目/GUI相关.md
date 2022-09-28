#### 1. 工具函数记录：

```lua
--ECItemTools：	装备相关接口，根据装备id获取图标等


--ECGUITools：
	setImgIcon(obj, pathid, gray)	-- gray为true时，得到灰度图
	setVisible_NoClick(bool)		-- true：可见不可点，false：不可见不可点

StringTable.Get(id)					--根据id的值返回string_table.lua里对应的string

FlashTipMan.FlashTip(string)		--客户端弹出string的提醒

--ECGeneralMan： 
	GetInfoById(id)					-- 根据id获取武将的信息，如等级等

```

#### 2. 迷之代码:

```lua

	_G.lua_pairs = _G.lua_pairs or pairs
	function _G.pairs (t)
		if type(t) == "userdata" then
			local mt = getmetatable(t)
			return mt.inext, t, nil
		else
			return _G.lua_pairs(t)
		end
	end	
```

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220831110306184.png" alt="image-20220831110306184"  />

#### 3. 获取时间、设置定时器：

![image-20220908110643293](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220908110643293.png)

#### 4. 事件的广播、监听

![image-20220908111323342](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220908111323342.png)