#### 客户端 `Lua` 编码规范

1.文件名: 匈牙利命名(因为 `windows` 上不区分大小写)

例如:

	main_scene.lua
	pve_scene.lua
			

2.类名：驼峰式，每个单词都以首字母大写开头，其它字母小写。

例如：

```
BattleEffectLayer = class("BattleEffectLayer", function()
    return display.newLayer()
end)

LoginLayer = class("LoginLayer", function()
    return display.newLayer()
end)
```

使用示例:

```
local BattleEffectLayer = require("path.battle_effect_layer")     --require类文件之后的返回值，如示例中的“local BattleEffectLayer ”，仍然是表示一个类，所以此处local的变量也要遵循类名的名命规范。

local battleEffectLayerObj = BattleEffectLayer.new()            --使用类名来声明类对象,如示例中的“local battleEffectLayerObj ”，此处表示的是一个对象，则遵循第3条变量的名命规范。
```

3.函数名字：驼峰式，第一个单词以小写开头，后面的其它单词以大写开头，其它字母小写。

例如：

```
function moveScaleCamera()  end
function showStome（） end
function LoginLayer:setVisible(enable) end
```

为提高代码的统一性，所以类，模块的函数定议都采用：

```
funcion ClsA:fun()
end
```

这种方式。


不使用如下方式：

```
ClsA.fun = function(self)
end
```


4.变量名：驼峰式

例如：

```
local accessToken = value
```

特殊情况，文件内部私有成员以及类私有成员可以以'\_'开头，

例如：

```
local _totalNum = 0
self._totalNum  = 0
```

5.宏写义: 下划线式，全部大写。

例如:

```
local STR_NAME="loginName"
local STR_PASSWORLD="passWord"
```

6.所有 `require` 必须是require("xxx.xxx")


7.玩法开发过程中，对于 `table` 的使用，不能使用中文来做 `key`!!!

例如：

```
local t = {
    ["中文"] = {...}
}
```

8. `json` 中控件命名统一驼峰式，并标明控件的类型。

例如:

```
txtDesc(TextArea)
btnLogin(Button)
lblName(Label)
listRank(ListView)
pnlNode(Panel)
imgAvatar(ImageView)
checkboxGender(CheckBox)

//scrollRank(ScrollView)
```

9.`if` 语句的规范

错误示范：

```
if numA > 10 then
else if numB > 10 then
end
```

正确：

```
if numA > 10 then
else
    if numB > 10 then
    end
end
```

10.文件格式都保存为 `utf-8`，所有的 `tab` 都使用4个空格替代。

11.其他规范请参考[Lua风格指引](http://gitlab.rd.175game.com/rd/lua--style-guide)，如果本文档与《Lua风格指引》冲突请以本文档为准。
