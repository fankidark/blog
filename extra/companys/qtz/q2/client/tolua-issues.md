# c与lua交互原理简述

## 一、lua栈

lua的栈类似于以下的定义:

    TValue stack[max_stack_len] 

它是在创建lua_State的时候创建的:

    // 创建一个state  
    lua_State *L = luaL_newstate(); 

    // 关闭state  
    lua_close(L); 

存入栈的数据类型包括数值, 字符串, 指针, talbe, 闭包等, 下面是一个栈的例子:

![lua栈](http://images.cnblogs.com/cnblogs_com/ringofthec/WindowsLiveWriter/Lua2_F1FC/lua%E6%A0%88.png)

## 二、基础类型交互

### 1、C API简介

c和lua的基础类型交互主要涉及到四组API：
    
    -- 创建lua类型的值插入栈顶，有内存分配
    lua_newtable
    lua_newuserdata
    lua_newthread

    -- 将C值转换为相应lua类型的值并插入栈顶，有内存分配
    lua_pushboolean
    lua_pushcfunction
    lua_pushnil
    lua_pushnumber
    lua_pushstring
    lua_pushthread
    lua_pushlightuserdata
    ... 

    -- 判断栈顶元素是否指定类型，与push对应
    lua_isXXX 

    -- 将栈顶元素转换为相应C类型返回，与push对应
    lua_toXXX 

另外C API的辅助库还提供了一些函数可以简化代码，比如：
    
    -- 检测函数的第arg个参数是否是数字，并返回这个数字
    luaL_checknumber(lua_State *L, int arg)

这个函数其实就是调用了lua_isnumber和lua_tonumber函数实现的。辅助库的函数（luaL_XXX）就相当于对C API另外做了一层封装，用于简化操作。

基础类型交互的示例代码如下：
    
    // 创建一个state  
    lua_State *L = luaL_newstate(); 
    luaL_openlibs(L); 

    // 入栈操作  
    lua_pushstring(L, "I am so cool~");   
    lua_pushnumber(L,20);  
   
    // 取值操作  
    if( lua_isstring(L,1)){             //判断是否可以转为string  
        cout<<lua_tostring(L,1)<<endl;  //转为string并返回  
    }  
    if( lua_isnumber(L,2)){  
        cout<<lua_tonumber(L,2)<<endl;  
    }  
   
    // 关闭state  
    lua_close(L);

### 2、C函数传入lua

#### 2.1 C函数声明

需要导入lua的C函数必须声明为lua_CFunction，其声明形式为：
    
    typedef int (*lua_CFunction)(lua_State *L);

lua调用该函数时会将传入的参数按顺序从左到右依次入栈，而该函数将需要返回给lua的值也依次压栈，并返回压入栈中的返回值个数。

因为lua中的函数都是闭包，所以C函数传入lua也是以闭包的形式:
    
    -- 将一个C函数入栈
    void lua_pushcfunction (lua_State *L, lua_CFunction f) 

    -- 将一个C函数关联n个栈上的值作为闭包入栈
    void lua_pushcclosure (lua_State *L, lua_CFunction fn, int n)

而其实lua_pushcfunction是作为一个宏定义出现的：

     #define lua_pushcfunction(L,f)  lua_pushcclosure(L,f,0)

#### 2.2 注册C函数作为lua的全局函数
    
    int l_cfunc(lua_State *L){...};
    lua_register(L, "g_cfunc", l_cfunc);

其实lua_register也是一个宏：

    #define lua_register(L,n,f) (lua_pushcfunction(L, f), lua_setglobal(L, n))

lua_setglobal的作用是弹出栈顶元素并将其设置为全局变量n的新值。

### 3、C对象传入lua

因为lua中没有类和对象的概念，所以C中的对象是以userdata的形式传入lua的。大致原理是将类的方法都注册到一个表中，然后将该表设置为userdata的元表，userdata的值指向C对象，具体方法见下文。

## 三、类和对象交互

### 1、注册C类到lua

lua实现面对对象可以有多种方式，但基本思路都是围绕元表来实现，以下先以一种较为简单的方式来阐述基本原理，最后再分析tolua++的实现方式。

以下是一个person类及其子类student的实现思路：
(*为方便阅读，后文都会用lua语法来描述实现思路。*)

![lua面对对象](http://pic002.cnblogs.com/images/2012/174192/2012111822181273.jpg)

代码描述如下：

    -- lua初始化时构建一个全局的注册表，_G表也是其中的一个元素
    registry = {}

    -- person对象的元表
    local person_obj_mt = {}
    registry.person = person_obj_mt
    person_obj_mt.__gc = function()
        -- do gc
    end

    -- person类原型
    local person_cls = {}
    _G.person = person_cls

    person_obj_mt.__index = person_cls

    -- 设置lua对象的元表，person_ud是一个指向person对象的userdata
    setmetatable(person_ud, person_obj_mt)

***

    -- student对象的元表
    local student_obj_mt = {}
    registry.student = student_obj_mt
    student_obj_mt.__gc = function()
        -- do gc
    end

    -- student类原型
    local student_cls = {}
    _G.stuent = student_cls

    student_obj_mt.__index = student_cls

    -- 设置student原型的元表为person对象的元表
    setmetatable(student_cls, person_obj_mt)

    -- 设置lua对象的元表，student_ud是一个指向person对象的userdata
    setmetatable(student_ud, person_obj_mt)

### 2、创建对象的方式及其生命周期管理

用userdata表示对象有两种方式：一种是创建一个userdata并让它指向一个由C创建的对象;一种是用userdata本身表示对象。前者的对象内存由C分配也由C释放，后者的由lua的分配器分配和释放。第一种方式适用于将C对象传递给lua的情况，而第二种更适用于完全由lua来管理对象生命周期的情况。示例代码如下：
    
    // class Person

    // 第一种方式
    Person **p = (Person**)lua_newuserdata(L, sizeof(Person*));
    *p = new Person();
    ...
    // 销毁对象
    delete *p;

    // 第二种方式
    void *pBuff = lua_newuserdata(L, sizeof(Person));
    p = new(pBuff) Person();
    ...
    // userdata内存由lua的gc释放，所以这里只调用对象的析构函数
    p->~Person();

## 四、tolua++基本原理

### 1、几个重要的全局表
    
    -- c对象指针在lua中一般用lightuserdata表示，而C对象是用userdata

    -- key: C对象指针, value: 表示C对象的userdata
    tolua_ubox = {}

    -- key: 类元表mt, value: {{父类名, true},{父类名，true}}
    tolua_super = {}

    -- key: C对象指针, value: 类元表mt
    tolua_gc = {}

    -- key: 表示C对象的userdata, value: {lua的扩展属性}
    tolua_peers = {}

### 2、注册C类到lua

tolua++中注册C类的方式与上文所述略有不同，它没有新建原型表，而是直接将类的方法注册到对象的元表中。并且对于类的public成员变量，在元表中提供了保存有访问器函数的get和set表来访问。代码思路如下：

    local student_mt = {}
    registry.student = student_mt

    student_mt.get = {}
    student_mt.set = {}

    student_mt.__index = function(t, key)
        local value = rawget(student_mt, key)
        if value then
            return value
        end
        if student_mt.get then
            local func = student_mt.get[key]
            if func then
                return func()
            end
        end
        -- 父类的元表
        return student_mt.__metatable[key]
    end

    student_mt.__newindex = function(t, key, value)
        ...
    end

    student_mt.__gc = function(ud)
        -- 伪代码
        local lud = (ud to lightuserdata)
        if tolua_gc[lud] == getmetatable(ud) then
            tolua_gc[lud] = nil
            -- do gc
        end
    end

### 3、自动化代码生成

除了上文所述的这套注册C类到lua的解决方案，tolua++还提供了一套用于将C类转换为指定格式的C代码文件的自动化脚本工具。将转换后的C文件编译成动态库后就可以供lua调用。

## 五、实践中的问题

1. 注册到lua中的函数必须按C的调用约定声明；
2. lua的解释器和调用的C库必须用动态链接的方式调用同一份lua源码。


