# 定义一个消息类型
首先，我们看一个很简单的示例。假设你想要定义一个搜索请求的消息格式，在这个消息格式中，包含一个搜索请求字符串，你想要获取的搜索结果页中某页的具体信息（页数），每页中的结果数量。那么你可以用以下.proto 文件来定义该消息类型。

<!--language:c-->

    message SearchRequest {
        required string query = 1;
        optional int32 page_number = 2;
        optional int32 result_per_page = 3;
    }
SearchRequest消息定义中声明了三个字段（键值对），来标识你在这个消息中包含的每一个数据块，每个字段有一个类型和一个名字。
## 字段类型
在上述例子中，所有字段都是标量类型，两个整形(page_number和result_per_page)和一个字符串（query） 。当然，你也可以给消息中的字段声明成复杂类型，包括枚举和其他的消息类型。
## 分配标签
正如上述例子中，消息定义中的每个字段都有一个唯一的数字标签。这些标签是用来在消息的二进制格式中标识你的字段。在编码的时候，1到15的标签，字段类型加标签只需要占用一个字节，从16到2047的标签，字段类型加标签的编码需要占用两个字节 (you can find out more about this in Protocol Buffer Encoding).。所以建议把1-15的标签保留给在消息中使用频繁的字段，在1-15的范围里可以保留一些空间给将来可能需要添加的频繁使用的字段。
##字段限制
你要用以下限制中的一个声明你的字段：
* required: 一个结构良好的消息中，该字段有且只有一个
* optional: 一个结构良好的消息中，该字段可以可以没有，也可以有，但不能多于一个
* repeated: 一个结构良好的消息中，该字段可以出现多次（也可以是0次），重复字段的顺序会被保留下来
  因为一些历史原因，repeated声明的标量数值字段无法高效的编码，通过使用[packed=true]选项来启用新代码对其进行高效编码。
  repeated int32 samples = 4 [packed=true];
 You can find out more about packed encoding in Protocol Buffer Encoding.
 
 注意：required是永远的
 在设置字段为required时你需要非常小心。如果在某些情况下你不再想要发送一个required的字段，将其更改为optional会出现问题，之前版本的消息readers会认为修改后的消息是不完整的而将其拒绝或丢弃。你可以考虑制定一些面向具体应用的定制消息格式的验证规范。一些google的工程师总结使用required弊大于利，他们倾向于只使用optional。当然，这个观点不具有普适性。
##添加多个消息类型
一个单独的.proto文件中可以定义多个消息类型。在定义多个相关的消息类型时，这样是很有用的。例如，你想为上述的SearchResponse消息定义一个对应的回复消息，你可以在同一个.proto中添加以下内容：

<!--language:c-->

    message SearchRequest {
        required string query = 1;
        optional int32 page_number = 2;
        optional int32 result_per_page = 3;
    }

    message SearchResponse {
    ...
    }
##添加注释
使用 C/C++风格的注释//：

<!--language:c-->

    message SearchRequest {
        required string query = 1;
        optional int32 page_number = 2;// Which page number do we want?
        optional int32 result_per_page = 3;// Number of results to return per page.
    }
##保留字段
在更新消息格式的时候，如果直接将原有字段直接删除或者注释，后续的使用者在更新这个消息时可能会再次使用这个字段的标签，这样在加载老版本的消息会导致很多问题，包括数据损坏，隐私漏洞等等。一种避免上述问题出现的方式是将需要删除的字段标签（或者名字）设置为reserved，后续如果使用者在更新消息格式时用到这些标签，protocol buffer 编译器会报错。

<!--language:c-->

    message Foo {
        reserved 2, 15, 9 to 11;
        reserved "foo", "bar";
    }
不可以在一个reserved声明中混合使用名字和标签。
##.proto文件的输出
当对一个.proto文件执行protocol buffer编译器时，protocol buffer编译器会生成操作该文件中描述的消息类型的指定语言的代码，包括获取，设置指定字段的值，序列化消息到输出流，从输入流解析消息。
* C++：编译器从每个.proto生成一个.h和.cc文件，文件中为.proto描述的每个消息类型定义一个类
* java：编译器生成一个.java文件，每个消息一个类和一个对应的创建消息类实例的Builder类
* Python:python有一点差异，编译器生成一个对每个消息类型静态描述的module，通过与元类的结合在运行时来创建一个方位数据的类
* GO:编译器生成一个.pb.go文件，
在接下来的指引中，你可以看到更多你选择的语言使用的api，对于更详细的api介绍，可以查看 API reference。