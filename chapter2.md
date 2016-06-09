# 标量类型

标量消息字段类型如下——表中展示了可以在.proto文件中声明的标量类型，以及在自动生成相应语言类中的类型
| .proto 类型        | 说明           | C++ 类型  |  Java 类型  | Python 类型 |  Go        |
| ------------------ | -------------- |  -------- |  ---------- |  ---------- |  --------- |
| double             |                | double    |  double     |    float    |   *float64 |
| float              |                |   float   |   float     |    float    |   *float32 |
| int32              | 使用变长编码，负值编码效率很低，建议使用sint32     |    int32 |   int  |  int  |  *int32|
| int64              | 使用变长编码，负值编码效率很低，建议使用sint64     |    int64 |   int  |  int/long  |  *int64|
| uint32             | 使用变长编码   |   uint32  |  int        |    int/long |  *uint32   |
| uint64             | 使用变长编码   |   uint64  |  long       |    int/long |  *uint64   |
| sint32             | 使用变长编码，带符号整形 |  int32   |   int   |  int    |  *int32   |
| sint64             | 使用变长编码，带符号整形 |  int64   |   long  |  int/long| *int64   |
| fixed32            | 四个字节，数值总是大于2^28时，编码比uint32 更效率 | uint32  | int |  int  |  *uint32  |
| fixed64            | 八个字节，数值总是大于2^56时，编码比uint64 更效率 | uint64  | long|  int/long | *uint64 |
| sfixed32           | 四个字节       |   int32   |  int        |     int      |   *int32  |
| sfixed64           | 八个字节       |   int64   |  long       |    int/long  |   *int64  |
| bool               |                |   bool    |  boolean    |    bool      |   *bool   |
| string             |                |   string  |  String     |   str/unicode|  string   |
| bytes              | 可包含任意顺序的字节 |  string | ByteString |  str      |  []byte   |

* java中，无符号32位和64位整形，实际存储与带符号整形相同，区别是带符号整形最高位用来标识符号
* 在任何情况下，给一个字段负值会执行类型检测来保证类型效性
* python中，64位或者无符号32位整形解码时总是表示为long，但如果设置字段为int时也可以解析为int。在任何情况下，字段值必须在所设置类型允许的范围内。
* python strings 在解码时表示为unicode ，但如果是一个 ASCII string 也可以解析为str（视情况改变）。