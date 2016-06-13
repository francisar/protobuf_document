# 枚举
在定义一个消息类型的时候，其中的某个字段的值可能需要限定在预先定义好的一个列表中的某个值。假设我们需要在SearchRequest消息中添加一个corpus 字段，corpus的值可以是UNIVERSAL, WEB, IMAGES, LOCAL, NEWS, PRODUCTS 或者VIDEO中的一个。在SearchRequest消息中添加一个enum类型的字段，该字段的值在一个常量的集合中（如果对该字段设置一个集合以外的值，解释器将其作为未知字段对待），在接下来的示例中，我们定义一个Corpus 的枚举类型，为消息添加一个Corpus类型的字段：

<!--language:c-->
    message SearchRequest {
        required string query = 1;
        optional int32 page_number = 2;
        optional int32 result_per_page = 3 [default = 10];
        enum Corpus {
            UNIVERSAL = 0;
            WEB = 1;
            IMAGES = 2;
            LOCAL = 3;
            NEWS = 4;
            PRODUCTS = 5;
            VIDEO = 6;
        }
        optional Corpus corpus = 4 [default = UNIVERSAL];
    }

在一个枚举类型的定义中，可以为不同常量设置相同的值。不过需要在类型的定义中设置allow_alias 选项为true，否则编译器遇到别名会报错。

<!--language:c-->

    enum EnumAllowingAlias {
        option allow_alias = true;
        UNKNOWN = 0;
        STARTED = 1;
        RUNNING = 1;
    }
    enum EnumNotAllowingAlias {
        UNKNOWN = 0;
        STARTED = 1;
        // RUNNING = 1;  // 如果不注释这行，编译其会发出一个警告信息
    }

枚举常量值必须是32位整形范围内的值，由于枚举类型的值在通信时使用varint编码，负值效率低因此不推荐。枚举类型可以像上面的例子一样在消息内定义，也可以在消息外部定义，定义在外部的枚举类型可以在.proto文件里其他消息定义中使用。定义在消息内的枚举类型，也可以通过MessageType.EnumType语法在其他消息定义中使用。

含有枚举类型的.proto，在使用编译器编译时，在java和c++代码中会生成相应的enum类型，在python中会生成一个特殊的EnumDescriptor元类从而在运行时创建一个符号常量与整形值对应的运行时类。

更多关于枚举类型的使用，请查看 generated code guide中对应语言的指引。