# 其他消息类型

其他消息类型也可以作为字段类型。例如，在SearchResponse消息中要添加Result消息类型，可以先在.proto文件中定义Result消息，再将Result声明为SearchResponse消息的一个字段：

<!--language:c-->

    message SearchResponse {
        repeated Result result = 1;
    }

    message Result {
        required string url = 1;
        optional string title = 2;
        repeated string snippets = 3;
    }
    
## 引入定义

在上面的例子中，Result 消息跟SearchResponse消息在同一个文件中定义。如果要将其他.proto文件中已经定义的消息作为一个字段要怎么办呢？

通过引入其他.proto文件可以使用其他文件中定义的消息。在文件的头部添加一个引入声明即可引入其他.proto文件:

<!--language:c-->

    import "myproject/other_protos.proto";
    
默认情况下，只有直接引入的.proto中的定义可以使用。然而，有时会移动.proto文件。这种情况下不需要把这个项目引入该文件的其他文件都更新，只需要在同一个地方创建一个同名文件通过import public语法将所有的引入操作转发到新文件。import public 可以传递依赖，将import public声明的文件中的内容传递给上层调用import的文件。

<!--language:c-->

    // new.proto
    // All definitions are moved here
    
    // old.proto
    // This is the proto that all clients are importing.
    import public "new.proto";
    import "other.proto";
    
    // client.proto
    import "old.proto";
    // You use definitions from old.proto and new.proto, but not other.proto
    
    
protocol 编译器会在protocol 编译器命令行-I/--proto_path中传递的路径集合中搜索被引入的文件。如果没有-I/--proto_path参数，编译器会在编译器被执行的路径进行搜索。通常建议将--proto_path指定为项目跟路径，引入文件时使用项目下的全路径。

## 使用proto3消息类型

在proto2 消息中可以引入和使用proto3消息类型，反之亦然。但是proto2的枚举类型不可以在proto3消息中使用。