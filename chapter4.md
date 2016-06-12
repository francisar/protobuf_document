# 枚举
在定义一个消息类型的时候，其中的某个字段的值可能需要限定在预先定义好的一个列表中的某个值。假设我们需要在SearchRequest消息中添加一个corpus 字段，corpus的值可以是UNIVERSAL, WEB, IMAGES, LOCAL, NEWS, PRODUCTS 或者VIDEO中的一个。在SearchRequest消息中添加一个enum