## 服务端新加协议基本流程
1. rc/rpc_decl/exmpale.decl 定义协议描述文件。格式如下：
``` 
imp module.exmpale.protocol{
    class exmpale_t{
        int uid;
        string name;
    }
    void rpc_server_exmpale_list(object user);
    void rpc_client_exmpale_list(int uid, exmpale_t * list, args...);
}
```
    -  第一行 imp 文档路径（相对logic）
     -       rpc_server_xxx(客户端上行协议只imp module/exmpale/protocol在这个文件中使用)
     - rpc_client_xxxxx 则不规定使用文件，只需要include头文件就行
    - class 定义结构体类似C语言struct。
        - 使用int 和string 类型（也可以使用其他协议的class）, 支持数组。
        - class数量最大不能超过32个
    - rpc_server 客户端处理的协议，上行给服务端
    - rpc_client 服务器端处理的协议，下行给客户端
    - rpc_gs global server处理的协议
    - rpc_ls logic server处理的协议
    - fs_ 我们自己扩展的efun

2. tools/makerpc.sh 运行生成协议脚本，会生成两个文件
    - rc/rpc/exmpale.h 和 rc/rpc_client/exmpale.h 当然还有其他文件会改到(rpc.cfg  rpc_id.h rpc_id_decl.h)可以了解一下。
    - 在主文件 module/exmpale/protocol.c 要include "rc/rpc/exmpale.h"。(如果在协议描述文件中使用到其他协议的结构体就需要在这之前include 另一个协议头文件)
    - rc/rpc_client/exmpale.h在生成的时候已经提交客户端。(所以这里貌似没什么用了)
3. 重启服务器，使用指令测试协议。
4. 提交协议的时候需要将 rc/rpc_decl/exmpale.decl   rc/rpc/exmpale.h 和 rc/rpc_client/exmpale.h 一起提交，当然其他改到的文件会另外提交