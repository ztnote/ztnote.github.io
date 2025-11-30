---
title: Toml
description: Toml learning notes.
slug: 002-toml
date: 2025-11-30 14:30:00+0800
#image: cover.png
categories:
    - daily
tags:
    - site
weight: 1       
draft: true
---
## toml 学习笔记

### 什么是toml
toml:Tom's Obvious, Minimal Language  
[toml](https://toml.io/cn/v1.0.0)
Tom 的（语义）明显、（配置）最小化的语言。  

TOML的宗旨
TOML 旨在成为一个语义明显且易于阅读的最小化配置文件格式。  
TOML 被设计成可以无歧义地映射为哈希表。  
TOML 应该能很容易地被解析成各种语言中的数据结构。  

规范  
大小写敏感  
UTF-8 编码  

注释  
单行注释以 # 开头  

### 在线转换工具
[toml to json](https://transform.tools/toml-to-json)

### toml 语法
两个核心概念：键值对和表

#### 键值对
TOML 文档最基本的构成区块是键值对。  
键名和键值周围的空白会被忽略。  
键、等号和值必须在同一行（不过有些值可以跨多行）。  

```toml
# 这是一个普普通通没有奇奇怪怪操作的例子

# 普通定义
key = "value"
key = # 非法

# 键名
# 键名可以是裸露的，引号引起来的，或点分隔的。
# 裸的就够了
key = "value"
bare_key = "value"
bare-key = "value"

# 点分隔键是一系列通过点相连的裸键或引号键。
name = "Orange"
physical.color = "orange"
physical.shape = "round"
# 此时是不能对physical进行赋值的


# 等价于
# {
#   "name": "Orange",
#   "physical": {
#     "color": "orange",
#     "shape": "round"
#   }
# }


# 多次定义同一个键是非法的
key = "value"
key = "other value"

# 字符串
str1 = "string"

# 整数
int1 = 12345
big_number = 123_456_789

# 浮点数
float1 = 1.234
float2 = 1e10

# 布尔
bool1 = true
bool2 = false

# 还可以日期和时间
dt1 = 1979-05-27T07:32:00Z

# 数组
integers = [ 1, 2, 3 ]
colors = [ "红", "黄", "绿" ]
nested_array_of_ints = [ [ 1, 2 ], [3, 4, 5] ]
nested_mixed_array = [ [ 1, 2 ], ["a", "b", "c"] ]
string_array = [ "所有的", '字符串', """是相同的""", '''类型''' ]

# 允许混合类型的数组
numbers = [ 0.1, 0.2, 0.5, 1, 2, 5 ]
contributors = [
  "Foo Bar <foo@example.com>",
  { name = "Baz Qux", email = "bazqux@example.com", url = "https://example.com/bazqux" }
]
```


#### 表
表（也被称为哈希表或字典）是键值对的集合。  
它们由表头定义，连同方括号作为单独的行出现。  
看得出表头不同于数组，因为数组只有值。  

```toml
[table-1]
key1 = "some string"
key2 = 123

[table-2]
key1 = "another string"
key2 = 456
```

顶层表，又被称为根表，于文档开始处开始并在第一个表头（或文件结束处）前结束。
不同于其它表，它没有名字且无法后置。

```toml
# 顶层表开始。
name = "Fido"
breed = "pug"

# 顶层表结束。
[owner]
name = "Regina Dogman"
member_since = 1999-08-04
```

内联表就不考虑了，标准表更好看。  


数组表
```toml
[[products]]
name = "Hammer"
sku = 738594937

[[products]]  # 数组里的空表

[[products]]
name = "Nail"
sku = 284758393

color = "gray"

# 等价于
# {
#   "products": [
#     { "name": "Hammer", "sku": 738594937 },
#     { },
#     { "name": "Nail", "sku": 284758393, "color": "gray" }
#   ]
# }
```


### 一个完整例子
以ztnote为例尝试写一个toml文件
```toml
# 顶层表（根表）
site = 'ztnote.github.io'
author = 'ztnote'
generator = 'hugo'
# 顶层表结束

[category]
description = 'category'

[category.daily]
description = 'daily post'

[category.code]
description = 'coding post'

[tag]
description = 'tag'

[tag.daily]
description = 'daily tag'

[tag.code]
description = 'coding tag'

[[post]]
title = 'Start'
description = 'A new Start.'
slug = '000-start'

[[post]]
title = 'hugo'
description = 'hugo'
slug = '001-hugo'

[[post]]
title = 'toml'
description = 'toml'
slug = '002-toml'
```

```json
{
  "site": "ztnote.github.io",
  "author": "ztnote",
  "generator": "hugo",
  "category": {
    "description": "category",
    "daily": {
      "description": "daily post"
    },
    "code": {
      "description": "coding post"
    }
  },
  "tag": {
    "description": "tag",
    "daily": {
      "description": "daily tag"
    },
    "code": {
      "description": "coding tag"
    }
  },
  "post": [
    {
      "title": "Start",
      "description": "A new Start.",
      "slug": "000-start"
    },
    {
      "title": "hugo",
      "description": "hugo",
      "slug": "001-hugo"
    },
    {
      "title": "toml",
      "description": "toml",
      "slug": "002-toml"
    }
  ]
}
```