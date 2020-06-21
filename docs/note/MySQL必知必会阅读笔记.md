- [第1章-了解SQL](#第1章-了解sql)
- [第2章-MySQL简介](#第2章-mysql简介)
- [第3章-使用MySQL](#第3章-使用mysql)
- [第4章:检索数据 select](#第4章检索数据-select)
- [第5章:排序检索数据 order by](#第5章排序检索数据-order-by)
- [第6章:过滤数据where](#第6章过滤数据where)
- [第7章 数据过滤 （and or in not）](#第7章-数据过滤-and-or-in-not)
- [第8章:用通配符进行过滤](#第8章用通配符进行过滤)
- [第9章:用正则表达式进行搜索](#第9章用正则表达式进行搜索)
  - [正则表达式重复元字符](#正则表达式重复元字符)
- [第10章:创建计算字段](#第10章创建计算字段)
# 第1章-了解SQL
数据库(database)：保存有组织的数据的容器（通常是一个文件或一组文件）

表(table):某种特定类型数据的结构化清单

模式(schema)：关于数据库和表的布局及特性的信息

列(column):表中的一个字段。所有表都是由一个或多个列组成的。

数据类型(datatype)：所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据

行（row) 表中的一个记录

主键（primary key) 一列（或一组列），其值能够唯一区分表中的每个行。

SQL（Structured Query Language) 结构化查询语言

# 第2章-MySQL简介
DBMS 数据库管理系统 MySQL是一种DBMS，即它是一种数据库软件

mysql命令行实用程序

注意事项：
```
命令输入在mysql>之后；
命令用;或\g结束，换句话说，仅按Enter不执行命令；
输入help或\h获得帮助，也可以输入更多的文本获得特定命令的帮助（如，输入help select
获得实用select语句的帮助;
输入quit或exit退出命令行实用程序;
```

# 第3章-使用MySQL
基础命令 (use,show)
选择xxx数据库
```
use xxx;
```

返回可用数据库
```
show databases;
```

返回当前数据库内的表的列表
```
show tables;
```

show的其他用法
```
show columns from customers;//显示表列
```

show的快捷方式 describe
```
describe customers;
//等价于
show columns from customers;
//其他show语句
show status;//显示广泛的服务器信息
show create database;//显示建库语句
show create table;//显示建表语句
show grants;//显示授予用户（所有用户或特定用户）的安全权限
show errors;//显示服务器错误信息
show warnings;//显示服务器警告信息
```

进一步了解show
```
help show;//显示允许的show语句
```

# 第4章:检索数据 select
```
//单个列
select prod_name 
from products;
//多个列
select prod_id,prod_name,prod_price 
from products;
//所有列
select * 
from products;
```

去重检索不同的行 distinct

==distinct关键字应用于所有列而不仅是前置它的列==。
```
select distinct vend_id
from products;//只返回不同（唯一）的vend_id行
```

limit限制结果 返回不多于5行(limit 5)
```
select prod_name
from products
limit 5;
```

从行5开始的5行，分页查询(limit 5,5)
检索出来的第一行为行0二不是行1。因此，limit 1，1将检索出第二行而不是第一行
```
select prod_name
from products
limit 5,5
```

使用完全限定的表名(某表某行)
```
select products.prod_name
from products;
```

表名也可以是完全限定的（某数据库某表）
```
select products.prod_name
from crashcoursel.products;
```

# 第5章:排序检索数据 order by
排序检索数据无顺序
```
select prod_name
from products;
```

指定列排序 字母顺序
```
select prod_name
from products
order by prod_name;
```

按多个列排序 首先按prod_price,prod_price若相同，然后按prod_name排序，如果唯一，则不按prod_name排序
```
select prod_id,prod_price,prod_name
from products
order by prod_price,prod_name;
```

指定排序方向 最贵的排最前 desc只应用于其前面的列名，后续如有字段则按默认的asc排序
```
select prod_id,prod_price,prod_name
from products
order by prod_price desc;
```

MySQL在排序中 A和a视为相同，所以在需要用order by来获取最大值时，需要与limit组合
```
select prod_price
from products
order by prod_price desc
limit 1;
```

# 第6章:过滤数据where

MySQL支持的操作符

操作符 | 说明
---|---
= | 等于
<> | 不等于
!= | 不等于
< | 小于
<= | 小于等于
> | 大于
>= | 大于等于
BETWEEN | 在指定的两个值之间
检测单个的值
```
select prod_name,prod_price
from products
where prod_name = 'fuses';
```

检索小于10美元的所有产品
```
select prod_name,prod_price
from products
where prod_price < 10;
```

检索小于等于10美元的所有产品
```
select prod_name,prod_price
from products
where prod_price <= 10;
```

不匹配检查 <>与!=等价
```
select vend_id,prod_name
from products
where vend_id <> 1003;
```

范围值检查
```
select prod_name, prod_price
from products
where prod_price between 5 and 10;
```

空值检查
```
select prod_name
from products
where prod_price is null;
```

数据过滤

# 第7章 数据过滤 （and or in not）
组合where子句 and
```
select prod_id,prod_price,prod_name
from products
where vend_id = 1003 and prod_price <= 10;
```

or操作符 与and操作符不同，它指示MySQL检索匹配任一条件的行
```
select prod_name,prod_price
from products
where vend_id = 1002 or vend_id = 1003;
```

and和or的计算次序 
```
select prod_name,prod_price
from products
where vend_id = 1002 or vend_id =1003 and prod_price >= 10;
```

SQL在处理OR操作符之前，优先处理and操作符，and在计算次序中的优先级更高 ，如需优先计算or，使用优先级比and更高的括号处理，如下
```
select prod_name, prod_price
from products
where (vend_id = 1002 or vend_id = 1003) and prod_price >=10;
```

in操作符 指定条件范围，范围中的每个条件都可以进行匹配
```
select prod_name, prod_price
from products
where vend_id in (1002,1003)
order by prod_name;//此时完成的功能相当于or
```

in比or的优势
```
在使用长的合法选项清单时，in操作符的语法更清楚且更直观。
在使用in时，计算的次序更容易管理（因为使用的操作符更少）。
in操作符一般比or操作符清单执行更快
in的最大优点是可以包含其他select语句，使得能够更动态地建立where子句。
```

not操作符 否定之后所跟的任何条件
```
select prod_name,prod_price
from products
where vend_id not in (1002,1003)
order by prod_name;
```

为什么使用not？在简单的where子句中，not确实没有什么优势。但是更复杂的子句中，not是非常有用的。
例如，在与in操作符联合使用时，not使找出与条件列表不匹配的行非常简单。
# 第8章:用通配符进行过滤
like操作符

通配符(wildcard) 用来匹配值的一部分的特殊字符。

搜索模式(search pattern) 由字面值、通配符或抢掠组合成的搜索条件

百分号（%）通配符

最常使用的通配符是百分号（%）。在搜索串中，%表示任何字符出现任意次数。例如，为了找出所有以词jet起头的产品，可使用以下select语句：
```
select prod_id,prod_name
from products
where prod_name like 'jet%';
```

带有anvil的词
```
select prod_id,prod_name
from products
where prod_name like %anvil%';
```

s起头e结尾的词
```
select prod_name
from products
where prod_name like 's%e';
```

%能匹配除null之外的0个或多个字符

下划线（_)通配符

功能与%相同，与%能匹配0个或多个字符相比，_只能匹配单个字符

使用通配符的技巧

通配符需要比其他常规搜索更慢,不要过度使用，以下是一些基本技巧
```
不用过度的使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符
在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。
仔细注意通配符的位置。如果放错地方，可能不回返回想要的数据。
```

# 第9章:用正则表达式进行搜索
正则表达式是用来匹配文本特殊的串（字符集合）。用于基本的过滤不能满足的复杂场景

基本字符的匹配

检索列prod_name包含文本1000的所有行
```
select prod_name
from products
where prod_name REGEP '1000'
order by prod_name;
```

检索列prod_name包含文本000的所有行
```
select prod_name
from products
where prod_name REGEXP '.000'
order by prod_name;
```

.是正则表达式语言中一个特殊的字符。它表示匹配任意一个字符，也可以用like和通配符完成
在like和regexp之间有一个重要的差别
```
select prod_name
from products
where prod_name like '1000'
order by prod_name;
```

```
select prod_name
from products
where prod_name REGEXP '1000'
order by prod_name;
```

如果执行上面两条，发现第一条语句不返回数据，而第二条语句返回一行

原因：like匹配整个列，如果被匹配的文本在列值中出现，like将不会找到它，相应的行也不会返回（除非使用通配符）。

而REGEXP在列值中进行匹配，如果配匹配的文本在列值中出现，REGEXP将会找到它，相应的行将被返回。这是一个非常重要的差别

MySQL中的正则表达式匹配不区分大小写，为区分大小写，可使用binary关键字，如 
```
where prod_name REGEXP BINARY 'JetPack .000
```

进行OR匹配 |为正则表达式中的OR操作符
```
select prod_name
from products
where prod_name REGEXP '1000|2000'
order by prod_name;
```

tips:两个以上的or条件 ‘1000|2000|3000’
```
select prod_name
from products
where prod_name REGEXP '[123] Ton'
order by prod_name;
```

匹配几个字符之一  [123] Ton为[1|2|3]Ton的缩写，意思是匹配1或2或3

也可以被否定，用法[^123]Ton，意思是匹配除1或2或3外的任何东西

匹配1到9[0123456789] 	缩写为[0-9].不需要完整的范围，如[1-3]或[5-9]都是合法的，也可以用到字母[a-z][A-Z],例

```
select prod_name
from products
where prod_name REGEXP '[1-5] Ton'
order by prod_name;
```

匹配特殊的字符 （找出包含.字符的值）

为了匹配特殊字符 必须以\\作为前导   这就是所谓的转义(escaping)
```
select vend_name
from vendors
where vend_name REGEXP '\\.'
order by vend_name;
```

\\\也用来引用元字符（具有特殊含义的字符），如下：

元字符 | 说明
---|---
\\\f | 换页
\\\n | 换行
\\\r |回车
\\\t |制表
\\\v |纵向制表

`tips：为了匹配反斜杠（\）字符本身，需要使用\\\`

多数正则表达式实现使用单个反斜杠转义特殊字符以便使用

MySQL要求两个反斜杠（MySQL自己解释一个，正则表达式库解释另一个）

匹配预定义字符类（character class)

类 | 说明
---|---
[\:alnum\:]|任意字母和数字（同[a-zA-Z0-9])
[\:alpha\:]|任意字符（同[a-zA-Z])
[\:blank\:]|空格和制表（同[\\t]）
[\:cntrl\:]|ASCII控制字符（ASCII 0到31和127）
[\:digit\:]|任意数字（同[0-9]）
[:graph:]|与[:print:]相同，但不包括空格
[:lower:]|任意小写字母（同[a-z]）
[:print:]|任意可打印字符
[:punct:]|既不在[:alnum:]又不在[:cntrl:]中的任意字符
[:space:]|包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]）
[:upper:]|任意大写字母（同[A-Z]）
[:xdigit:]|任意十六进制数字（[同a-fA-F0-9]）

ASCII码表具体如下所示

Bin(二进制) | Oct(八进制) | Dec(十进制) | Hex(十六进制) | 缩写/字符 | 解释
---|---|---|---|---|---
0000 0000 | 00 | 0 | 0x00 | NUL(null) | 空字符
0000 0001 | 01 | 1 | 0x01 | SOH(start of headline) | 标题开始
0000 0010 | 02 | 2 | 0x02 | STX (start of text) | 正文开始
0000 0011 | 03 | 3 | 0x03 | ETX (end of text) | 正文结束
0000 0100 | 04 | 4 | 0x04 | EOT (end of transmission) | 传输结束
0000 0101 | 05 | 5 | 0x05 | ENQ | (enquiry) | 请求
0000 0110 | 06 | 6 | 0x06 | ACK | (acknowledge) | 收到通知
0000 0111 | 07 | 7 | 0x07 | BEL | (bell) | 响铃
0000 1000 | 010 | 8 | 0x08 | BS (backspace) | 退格
0000 1001 | 011 | 9 | 0x09 | HT (horizontal tab) | 水平制表符
0000 1010 | 012 | 10 | 0x0A | LF (NL line feed, new line) | 换行键
0000 1011 | 013 | 11 | 0x0B | VT (vertical tab) | 垂直制表符
0000 1100 | 014 | 12 | 0x0C | FF (NP form feed, new page) | 换页键
0000 1101 | 015 | 13 | 0x0D | CR (carriage return) | 回车键
0000 1110 | 016 | 14 | 0x0E | SO (shift out) | 不用切换
0000 1111 | 017 | 15 | 0x0F | SI (shift in) | 启用切换
0001 0000 | 020 | 16 | 0x10 | DLE (data link escape) | 数据链路转义
0001 0001 | 021 | 17 | 0x11 | DC1 (device control 1) | 设备控制1
0001 0010 | 022 | 18 | 0x12 | DC2 (device control 2) | 设备控制2
0001 0011 | 023 | 19 | 0x13 | DC3 (device control 3) | 设备控制3
0001 0100 | 024 | 20 | 0x14 | DC4 (device control 4) | 设备控制4
0001 0101 | 025 | 21 | 0x15 | NAK (negative acknowledge) | 拒绝接收
0001 0110 | 026 | 22 | 0x16 | SYN (synchronous idle) | 同步空闲
0001 0111 | 027 | 23 | 0x17 | ETB (end of trans. block) | 结束传输块
0001 1000 | 030 | 24 | 0x18 | CAN (cancel) | 取消
0001 1001 | 031 | 25 | 0x19 | EM (end of medium) | 媒介结束
0001 1010 | 032 | 26 | 0x1A | SUB (substitute) | 代替
0001 1011 | 033 | 27 | 0x1B | ESC (escape) | 换码(溢出)
0001 1100 | 034 | 28 | 0x1C | FS (file separator) | 文件分隔符
0001 1101 | 035 | 29 | 0x1D | GS (group separator) | 分组符
0001 1110 | 036 | 30 | 0x1E | RS (record separator) | 记录分隔符
0001 1111 | 037 | 31 | 0x1F | US (unit separator) | 单元分隔符
0010 0000 | 040 | 32 | 0x20 | (space) | 空格
0010 0001 | 041 | 33 | 0x21 | ! | 叹号
0010 0010 | 042 | 34 | 0x22 | " | 双引号
0010 0011 | 043 | 35 | 0x23 | # | 井号
0010 0100 | 044 | 36 | 0x24 | $ | 美元符
0010 0101 | 045 | 37 | 0x25 | % | 百分号
0010 0110 | 046 | 38 | 0x26 | & | 和号
0010 0111 | 047 | 39 | 0x27 | ' | 闭单引号
0010 1000 | 050 | 40 | 0x28 | ( | 开括号
0010 1001 | 051 | 41 | 0x29 | ) | 闭括号
0010 1010 | 052 | 42 | 0x2A | * | 星号
0010 1011 | 053 | 43 | 0x2B | + | 加号
0010 1100 | 054 | 44 | 0x2C | , | 逗号
0010 1101 | 055 | 45 | 0x2D | - | 减号/破折号
0010 1110 | 056 | 46 | 0x2E | . | 句号
0010 1111 | 057 | 47 | 0x2F | / | 斜杠
0011 0000 | 060 | 48 | 0x30 | 0 | 字符0
0011 0001 | 061 | 49 | 0x31 | 1 | 字符1
0011 0010 | 062 | 50 | 0x32 | 2 | 字符2
0011 0011 | 063 | 51 | 0x33 | 3 | 字符3
0011 0100 | 064 | 52 | 0x34 | 4 | 字符4
0011 0101 | 065 | 53 | 0x35 | 5 | 字符5
0011 0110 | 066 | 54 | 0x36 | 6 | 字符6
0011 0111 | 067 | 55 | 0x37 | 7 | 字符7
0011 1000 | 070 | 56 | 0x38 | 8 | 字符8
0011 1001 | 071 | 57 | 0x39 | 9 | 字符9
0011 1010 | 072 | 58 | 0x3A | : | 冒号
0011 1011 | 073 | 59 | 0x3B | ; | 分号
0011 1100 | 074 | 60 | 0x3C | < | 小于
0011 1101 | 075 | 61 | 0x3D | = | 等号
0011 1110 | 076 | 62 | 0x3E | > | 大于
0011 1111 | 077 | 63 | 0x3F | ? | 问号
0100 0000 | 0100 | 64 | 0x40 | @ | 电子邮件符号
0100 0001 | 0101 | 65 | 0x41 | A | 大写字母A
0100 0010 | 0102 | 66 | 0x42 | B | 大写字母B
0100 0011 | 0103 | 67 | 0x43 | C | 大写字母C
0100 0100 | 0104 | 68 | 0x44 | D | 大写字母D
0100 0101 | 0105 | 69 | 0x45 | E | 大写字母E
0100 0110 | 0106 | 70 | 0x46 | F | 大写字母F
0100 0111 | 0107 | 71 | 0x47 | G | 大写字母G
0100 1000 | 0110 | 72 | 0x48 | H | 大写字母H
0100 1001 | 0111 | 73 | 0x49 | I | 大写字母I
0100 1010 | 0112 | 74 | 0x4A | J | 大写字母J
0100 1011 | 0113 | 75 | 0x4B | K | 大写字母K
0100 1100 | 0114 | 76 | 0x4C | L | 大写字母L
0100 1101 | 0115 | 77 | 0x4D | M | 大写字母M
0100 1110 | 0116 | 78 | 0x4E | N | 大写字母N
0100 1111 | 0117 | 79 | 0x4F | O | 大写字母O
0101 0000 | 0120 | 80 | 0x50 | P | 大写字母P
0101 0001 | 0121 | 81 | 0x51 | Q | 大写字母Q
0101 0010 | 0122 | 82 | 0x52 | R | 大写字母R
0101 0011 | 0123 | 83 | 0x53 | S | 大写字母S
0101 0100 | 0124 | 84 | 0x54 | T | 大写字母T
0101 0101 | 0125 | 85 | 0x55 | U | 大写字母U
0101 0110 | 0126 | 86 | 0x56 | V | 大写字母V
0101 0111 | 0127 | 87 | 0x57 | W | 大写字母W
0101 1000 | 0130 | 88 | 0x58 | X | 大写字母X
0101 1001 | 0131 | 89 | 0x59 | Y | 大写字母Y
0101 1010 | 0132 | 90 | 0x5A | Z | 大写字母Z
0101 1011 | 0133 | 91 | 0x5B | [ | 开方括号
0101 1100 | 0134 | 92 | 0x5C | \ | 反斜杠
0101 1101 | 0135 | 93 | 0x5D | ] | 闭方括号
0101 1110 | 0136 | 94 | 0x5E | ^ | 脱字符
0101 1111 | 0137 | 95 | 0x5F | _ | 下划线
0110 0000 | 0140 | 96 | 0x60 | ` | 开单引号
0110 0001 | 0141 | 97 | 0x61 | a | 小写字母a
0110 0010 | 0142 | 98 | 0x62 | b | 小写字母b
0110 0011 | 0143 | 99 | 0x63 | c | 小写字母c
0110 0100 | 0144 | 100 | 0x64 | d | 小写字母d
0110 0101 | 0145 | 101 | 0x65 | e | 小写字母e
0110 0110 | 0146 | 102 | 0x66 | f | 小写字母f
0110 0111 | 0147 | 103 | 0x67 | g | 小写字母g
0110 1000 | 0150 | 104 | 0x68 | h | 小写字母h
0110 1001 | 0151 | 105 | 0x69 | i | 小写字母i
0110 1010 | 0152 | 106 | 0x6A | j | 小写字母j
0110 1011 | 0153 | 107 | 0x6B | k | 小写字母k
0110 1100 | 0154 | 108 | 0x6C | l | 小写字母l
0110 1101 | 0155 | 109 | 0x6D | m | 小写字母m
0110 1110 | 0156 | 110 | 0x6E | n | 小写字母n
0110 1111 | 0157 | 111 | 0x6F | o | 小写字母o
0111 0000 | 0160 | 112 | 0x70 | p | 小写字母p
0111 0001 | 0161 | 113 | 0x71 | q | 小写字母q
0111 0010 | 0162 | 114 | 0x72 | r | 小写字母r
0111 0011 | 0163 | 115 | 0x73 | s | 小写字母s
0111 0100 | 0164 | 116 | 0x74 | t | 小写字母t
0111 0101 | 0165 | 117 | 0x75 | u | 小写字母u
0111 0110 | 0166 | 118 | 0x76 | v | 小写字母v
0111 0111 | 0167 | 119 | 0x77 | w | 小写字母w
0111 1000 | 0170 | 120 | 0x78 | x | 小写字母x
0111 1001 | 0171 | 121 | 0x79 | y | 小写字母y
0111 1010 | 0172 | 122 | 0x7A | z | 小写字母z
0111 1011 | 0173 | 123 | 0x7B | { | 开花括号
0111 1100 | 0174 | 124 | 0x7C | | | 垂线
0111 1101 | 0175 | 125 | 0x7D | } | 闭花括号
0111 1110 | 0176 | 126 | 0x7E | ~ | 波浪号
0111 1111 | 0177 | 127 | 0x7F | DEL (delete) | 删除

匹配多个实例

## 正则表达式重复元字符
元字符 | 说明
---|---
* | 0个或多个匹配
+ | 1个或多个匹配（等于{1,}）
? | 0个或多个匹配（等于{0,1}
{n} | 指定数目的匹配
{n,} | 不少于指定数目的匹配
{n,m} | 匹配数目的范围(m不超过255)

```
select prod_name
from products
where prod_name REGEXP '\\([0-9] sticks?\\)'
order by prod_name;
```

[0-9]匹配0-9的任意数字,sticks?匹配stick和sticks（s后的？使s可选，因为？匹配它前面的任何字符的0次或1次出现）。

没有？，匹配stick和sticks会非常困难

配连在一起的4位数字：
```
select prod_name
from products
where prod_name REGEXP '[[:digit:]]{4}'
order by prod_name;
```

[:digit:]匹配任意数字,{4}确切地要求他前面的字符（任意数字）出现4次，所以就是[[:digit:]]{4}
简单的写法如下
```
select prod_name
from products
where prod_name REGEXP '[0-9][0-9][0-9][0-9]'
order by prod_name;
```

定位符
元字符 | 说明
---|---
^ | 文本的开始
$ | 文本的结尾
[[:<:]] | 词的开始
[[:>:]] | 词的结尾

找出以一个数（包括小数点开始的数）开始的所有产品，用[0-9\\.]或[[:digit:]\\.]不行，解决办法是通过^定位符处理
```
select prod_name
from products
where prod_name REGEXP '^[0-9\\.]'
order by prod_name;
```

^的双重用途
```
在集合中
    用[和]定义，用它来否定该集合，
否则，用来指串的开始处。
```

起到类似like的作用
```
LIKE和REGEXP的不同在于，LIKE匹配整个串而REGEXP匹配子串，利用定位符，通过用^开始每个表达式。
用$结束每个表达式，可以使REGEXP的作用于LIKE一样
```

不试用数据库表的情况下的简单测试 
```
select 'hello' REGEXP '[0-9]';
```

显然返回0（因为文本hello没有数字）。

# 第10章:创建计算字段
字段（field）基本与列（column）的意思相同，经常互换使用，不过数据库列一般称为列，而术语 字段通常用在计算字段的连接上。
拼接（concatenate）将值联结到一起构成单个值
解决办法是把两个列拼接起来，在MySQL的SELECT语句中，可使用Concat()函数来拼接两个列,而其他的DBMS使用+或||来实现拼接的，转换时需要注意
select Concat(vend_name),'(',vend_country,')')
from vendors
order by vend_name;

使用RTrim()，通过删除数据右侧多余的空格来整理数据,RTrim()函数去掉值游标的所有空格，LTrim()去掉串左边的空格以及Trim去掉串左右两边的空格
select Concat(RTrim(vend_name),'(',RTrim(vend_country),')')
from vendors
order by vend_name;

别名(alias) 将查询出的结果命名为vend_title 别名有时也成为导出列（derived column），它们代表的都是相同的东西
select Concat(RTrim(vend_name),'(',RTrim(vend_country),')') AS
vend_title
from vendors
order by vend_name;

执行算数计算 MySQL算数操作符 +-*/ 加减乘除
select  prod_id,
        quantity,
        item_price,
        quantity*item_price AS expanded_price
from orderitems
where order_num = 20005;

第11章:使用数据处理函数
函数：一般是在数据上执行的，它给数据的转换和处理提供了方便，之前的RTrim就是一个例子
Upper()：将文本转换为大写
select vend_name,Upper(vend_name) AS vend_name_upcase
from vendors
order by vend_name;

常用的
函数 说明
Left() 返回串左边的字符
Length() 返回串的长度
Locate() 找出串的一个字串
Lower() 将串转换为小写
LTrim() 去掉串左边的空格
Right() 返回串右边的字符
RTrim() 去掉串右边的空格
Soundex() 返回串的SOUNDEX值
SubString() 返回子串的字符
Upper() 将串转换为大写
SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。SOUNDEX考虑了类的发音字符和音节，使得能对串进行发音比较二不是字母比较。
虽然SOUNDEX不是SQL概念，但MySQL（就像是多数DBMS）一样都提供SOUNDEX支持
select cust_name,cust_contact
from customers
where Soundex(cust_contact) = Soundex('Y Lie');

此搜索条件可以匹配 'Y Lee'
日期和时间处理函数
常用的日期和事件处理函数
函数 说明
AddDate() 增加一个日期（天、周等）
AddTime() 增加一个时间（时、分等）
CurDate() 返回当前日期
CurTime() 返回当前时间
Date() 返回日期时间的日期部分
DateDiff() 计算两个日期之差
Date_Add() 高度灵活的日期运算函数
Date_Format() 返回一个格式化的日期或时间串
Day() 返回一个日期的天数部分
DateOfWeek() 对于一个日期，返回对应的星期几
Hour() 返回一个日期的小时部分
Minute() 返回一个时间的分钟部分
Month() 返回一个日期的月份部分
Now() 返回当前日期和时间
Second() 返回一个日期时间的秒部分
Time() 返回一个日期时间的时间部分
Year() 返回一个日期的年份部分
基本日期比较
select cust_id, order_num
from orders
where order_date = '2005-09-01';

这种方式并不很可靠，当值为'2005-09-01 11:30:05'时，匹配失败，时分秒不为0
正确的方式 Time() 忽略日期匹配时间同理
select cust_id, order_num
from orders
where Date(order_date) = '2005-09-01';

匹配2005年9月下的所有订单
select cust_id, order_num
from orders
where Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';

不需要记住每个月有多少天或不需要操心闰年2月的办法；
select cust_id, order_num
from orders
where Year(order_date) = 2005 AND Month(order_date) = 9;

数值处理函数
函数 说明
Abs() 返回一个数的绝对值
Cos() 返回一个角度的余弦
Exp() 返回一个数的指数值
Mod() 返回除操作的余数
Pi() 返回圆周率
Rand() 返回一个随机数
Sin() 返回一个角度的正弦
Sqrt() 返回一个数的平方数
Tan() 返回一个角度的正切
第12章:汇总数据
聚集函数(aggregate function) 	运行在行组上，计算和返回单个值的函数。
SQL聚集函数
函数 说明
AVG() 返回某列的平均值
COUNT() 返回某列的行数
MAX() 返回某列的最大值
MIN() 返回某列的最小值
SUM() 返回某列值之和
AVG()函数 返回products中的所有产品的平均价格
select AVG(prod_price) AS avg_price
from products;

COUNT()函数 返回customers表中客户的总数
select count(*) AS num_cust
form customer;

MAX()函数 返回指定列中的最大值 返回最贵的物品价格
select MAX(prod_price) AS max_price
FROM products;

MIN()函数 返回指定列中最小值 最便宜的物品价格
select MIN(prod_price) AS min_price
FROM products;

SUM()函数 返回指定列值的和（总计）
select SUM(quantity) AS items_ordered
FROM orderitems
WHERE order_num = 20005;

DISTINCT() 聚集不同值 只考虑不同价格的平均值
select AVG(DISTINCT prod_price) AS avg_price
FROM products
WHERE vend_id = 1003;

组合聚集函数 单条select语句执行4个聚集计算，返回四个值（products表中的物品的数目，产品价格的最低、最高以及平均值）
select COUNT(*) AS num_items,
    MIN(prod_price) AS price_min,
    MAX(prod_price) AS price_max,
    AVG(prod_price) AS price_avg
FROM products;

第13章:分组数据
分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。
返回供应商1003提供的产品数目：
select count(*) as num_prods
from products
where vend_id = 1003;

创建分组
SELECT vend_id,COUNT(*) AS num_prods
FROM products
GROUP BY vend_id;

select语句指定了两个列，表示MySQL按vend_id排序并分组数据，表示对每个vend_id二不是整个表计算num_prods一次
使用关键字ROLLUP 
使用with ROLLUP关键字，可以得到每个分组以及每个分组汇总级别（针对每个分组）的值，如
SELECT vend_id,COUNT(*) AS num_prods
FROM products
GROUP BY vend_id WITH ROLLUP;

13.3过滤分组 用having子句实现
SELECT cust_id,COUNT(*) AS orders
FROM orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;

使用where子句不起作用，因为过滤是基于分组聚集值而不是特定行值的
HAVING和WHERE的差别
where在数据分组前进行过滤，HAVING在数据分组后进行过滤.
这是一个重要的区别，where排除的行不包括在分组中。
这可能会改变计算值，从而影响having子句中基于这些值过滤掉的分组。

同时使用where和having的情况 列出具有2个（含）以上、价格为10（含）以上的产品供应商
select vend_id,count(*) as num_prods
from products
where prod_price >=10
group by vend_id
having count(*) >=2;

13.4分组和排序
分组group by 		排序 order by
order by group by
排序产生的输出 分组行。但输出可能不是分组的顺序
任意列都可以使用（甚至非选择的列也可以使用） 只可能使用选择列或表达式列，而且必须使用每个选择列表达式
不一定需要 如果与聚集函数一起使用列（或表达式），则必须使用

一般在使用group by子句时，应该也给出order by子句。这是保证数据正确排序的唯一方法。
千万不要仅依赖group by排序数据

说明group by 和order by的使用方法
select order_num,SUM(quality*item_price) as order total
from orderitems
group by order_num
having SUM(quantity*item_price) >= 50;

为按总计订单价格排序输出，需要添加order by 子句
select order_num,SUM(quantity*item_price) as ordertotal
from orderitems
group by order_num
having sum(quantity*item_price) >=50
order by ordertotal;

在这个句子中，group by 子句用来按订单号(order_num列)分组数据，以便SUM(*)函数能够返回总计订单价格。HAVING子句过滤数据，使得只返回总计订单价格大于等于50的订单。最后，用order by 子句输出
13.5 select子句顺序
回顾一下select语句中子句的顺序
子句 说明 是否必须使用
select 要返回的列或表达式 是
from 从中检索数据的表 仅在从表选择数据时使用
where 行级过滤 否
group by 分组说明 仅在按组计算聚集时使用
having 组级过滤 否
order by  输出排序顺序 否
limit 要检索的行数 否
第14章:使用子查询 subquery
14.1子查询
查询：任何select语句都是查询。但此术语一般指select语句
子查询：即嵌套在其他查询中的查询。
14.2利用子查询进行过滤
对于prod_id为TNT2的所有订单物品，它检索其order_num列。输出列出两个包含此物品的订单：
select order_num
from orderitems
where prod_id = 'TNT2';

查询具有订单20005和20007的客户ID
select cust_id
from orders
where order_num In (20005,20007);

把第一个查询变为子查询组合两个查询
select cust_id
from orders
where order_num In (SELECT order_num
                    FROM orderitems
                    WHERE prod_id = 'TNT2');

下一步是检索这些客户ID的客户信息
检索两列的SQL语句为：
select cust_name,cust_contact
from customers
where cust_id in (select cust_id
                  from orders
                  where order_num in (select order_num
                                      from orderitems
                                      where prod_id = 'TNT2'));

对客户10001的订单进行计数
select cust_name,
       cust_state,
       (select count(*)
       from orders
       where orders.cust_id = customers.cust_id) as orders
from customers
order by cust_name;

第15章 联结表 join
避免笛卡尔积的问题
内联结 inner join 联结条件用特定的on子句二不是where子句给出
select vend_name,prod_name,prod_price
from vendors inner join products
on vendors.vend_id = products.vend_id;

等值联结 联结多个表 显示编号为20005的订单中的物品
select prod_name,vend_name,prod_price,quantity
from orderitems, products, vendors
where products.vend_id = vendors.vend_id
and orderitems.prod_id = products.prod_id
and order_num = 20005;

14章例子返回订购产品TNT2的客户列表
select cust_name,cust_contact
from customers
where cust_id in (select cust_id
                  from orders
                  where order_num in (select order_num
                                      from orderitems
                                      where prod_id = 'TNT2'));

使用联结的相同查询
select cust_name, cust_contact
from customers, orders, orderitems
where customers.cust_id = orders.cust_id
and orderites.order_num = orders.order_num
and prod_id = 'TNT2';

等值联结非常耗费资源，不要联结不必要的表
第16章 创建高级联结 （别名、自，自然，外部联结）
使用表别名
两个理由
1.缩短SQL语句
2.允许在单条select语句中多次使用相同的表
给列起别名的语法如下
select concat(RTrim(vend_name),'(',RTrim(vend_country),')') as 
vend_title
from vendors
order by vend_name;

表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户机
自联结 
此查询要求首先找到生产ID为DTNTR的供应商，然后找出这个供应商生产的其他物品
select prod_id, prod_name
from products
where vend_id = (select vend_id
                 from products
                 where prod_id = 'DTNTR');


查询相同的表 使用表别名避免二义性
select p1.prod_id,p1.prod_name
from products as p1,products as p2
where p1.vend_id = p2.vend_id
and p2.prod_id = 'DTNTR';

自然联结
自然联结是这样一种联结，其中你只能选择那些唯一的列。这一般是通过对表使用通配符（select*),对所有其他表的列使用明确的子集来完成，如
select c.*,o.order_num,o.order_date,
        oi.prod_id,oi.quantity,oi.item_price
from customers as c, orders as o, orderitems as oi
where c.cust_id = o.cust_id
and oi.order_num = o.order_num
and prod_id = 'FB';

通配符只对第一个表使用。所有其他的列明确列出，所以没有重复的列被检索出来。
外部联结
联结包含了那些在相关表中没有关联行的行。这种类型的联结成为外部联结。
检索所有客户及其订单 
内联结
select customers.cust_id,orders.order_num
from customers INNER JOIN orders
on customers.cust_id = orders.cust_id;

外联结
select customers.cust_id,orders.order_num
from customers LEFT OUTER JOIN orders
on customers.cust_id = orders.cust_id;

左右外联结的差别：
关联的表的顺序不同。左外联结可通过颠倒from或where字句中标的顺序转换为右外联结，可互换
使用带聚集函数count(*)的联结
检索所有客户及每个客户所下的订单数
内联结 不统计订单数count为0
select customers.cust_name,
       customers.cust_id,
       COUNT(orders.order_num) as num_ord
from customers INNER JOIN orders
on customers.cust_id = orders.cust_id
group by customers.cust id;

外联结 要统计订单数count为0

select customers.cust_name,
       customers.cust_id,
       COUNT(orders.order_num) as num_ord
from customers LEFT OUTER  JOIN orders
on customers.cust_id = orders.cust_id
group by customers.cust id;

使用联结的要点
注意所使用的联结类型。一般我们使用内部联结，但使用外部联结也是有效的。
保证使用正确的联结条件，否则讲返回不正确的数据。
应该总是提供联结条件，否则会得出笛卡尔积。
在一个联结中可以包含多个表，甚至对于每个联结可以采用不同的联结类型。虽然这样做是合法的，一般也很有用，但应该在一次测试它们前，分别测试每个联结。这将使故障排除更为简单。
第17章 组合查询 union组合多个select语句
union可极大地简化复杂的where子句，简化多表查询工作
需要使用组合查询的两种基本情况：
1.在单个查询中从不同的表返回类似结构的数据
2.对单个表执行多个查询，按单个查询返回数据
使用union
需要价格小于等于5的所有物品的列表，
select vend_id,prod_id,prod_price
from products
where prod_price <=5;

而且还想包括供应商1001和1002生产的所有物品（不考虑价格）
select vend_id,prod_id,prod_price
from products
where vend_id in (1001,1002);

组合语句
select vend_id,prod_id,prod_price
from products
where prod_price <=5
union
select vend_id,prod_id,prod_price
from products
where vend_id in (1001,1002);

union只是MySQL执行两条select语句，并把输出组合成单个查询结果集
union适用于复杂过滤条件
基本规则：
1.union必须由两条或两条以上的select语句组成，语句之间用关键字union分隔（因此，如果组合4条select语句，将要使用3个union关键字）。
2.union中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。
3.列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。

union从查询结果集中自动去除了重复的行，这是union的默认行为。如果想要返回所有的行，可使用union all 而不是union

在用union组合查询时，只能使用一条order by子句，它必须出现在最后一条select语句之后。
第18章 全文本搜索 fulltext 支持MyISAM
当基于文本的搜索 like 正则的功能不满足搜索要求时（性能，明确控制，智能化的结果）
使用全文本搜索 使用索引，之后select可以与Match()和Against()一起使用以实际执行搜索。
create语句演示fulltext子句的使用：
create table productnotes
(
    note_id    int        not null auto_increment,
    prod_id    char(10)   not null,
    note_date  datetime   note null,
    note_text  text       null,
    primary key(note_id),
    fulltext(note_text)
) engine=MyISAM;

这条create table语句定义表productnotes并列出它所包含的列。列中有一个名为note_text的列，为了进行全文本搜索，MySQL根据子句fulltext(note_text)的指示对它进行索引。此处的fulltext索引单个列，如果需要也可以指定多个列。
索引之后使用Match()和Against()执行全文本搜索
select note_text
from productnotes
where Match(note_text) Against('rabbit');

Match(note_text)指示MySQL针对指定的列进行搜索，Against('rabbit')指定词rabbit作为搜索文本。
等价于
select note_text
from productnotes
where note_text like '%rabbit%'

like的排序不是特别有用，而全文本搜索的结果以良好程度排序给出
全文本搜索的第一个重要的部分就是对结果排序。具有较高等级的行先返回(很可能这些行是你真正想要的行)
查询扩展：
用来设法放宽所返回的全文本搜索结果的范围。除了全文本搜索获取到的结果以外，还可以查询到获取到的结果相关的其他行数据
查询扩展极大地增加了返回的行数，但这样做也增加了你实际并不想要的行的数目
布尔文本搜索
全文本搜索的另外一种形式 非常缓慢的操作 可设定要排斥的词，设定关键排列等级值
仅在MyISAM数据库引擎中支持全文本搜索
第19章 插入数据 insert
插入的四种方式
1.插入完整的行
2.插入行的一部分
3.插入多行
4.插入某些查询的结果

一般不要使用没有明确给出列的列表的insert语句（insert into table values(xxx) ）
insert操作可能很耗时，在insert into之间添加关键字low_priority，指示MySQL降低insert语句的优先级
插入多个行，value部分以list的形式编写即可，可提高写入速度
插入检索出的数据 insert into table(xxx) select xxx
第20章 更新和删除数据update delete
两种方式update
更新表中的特定行;
更新表中的所有行;
三部分组成：要更新的表、列名和它们的新值、确定要更新行的过滤条件
update table
set xxx = xxx
,xxx=xxx #更新多个列
where xxx

通过update table set xxx =null来删除某一列的值
用delete删除指定行
delete from customers
where cust_id = 10006;

delete不需要列名或者通配符.delete删除整行而不是删除列。删除指定列用update语句
delete语句从表中删除行，甚至是删除表中的所有行.但是不删除表本身
如果想从表中删除所有航，不要使用delete，建议使用命令
truncate table '表格名'

使用update或delete时所应遵循的习惯。
除非确实打算更新和删除每一行，否则绝对不要使用不带where子句的update或delete语句
保证每个表都有主键，尽可能像where子句那样使用它（可以指定各主键、多个值或值的范围）。
在对update或delete语句使用where子句前，应该先用select进行测试，保证它过滤的是正确的记录，以防编写的where子句不正确。
使用强制实施引用完整性的数据库，这样MySQL将不允许删除具有与其他表相关联的数据的行。
第21章 创建和操纵表 create
create table customers
(
    cust_id        int       not null auto_increment,
    cust_name      char(50)  not null,
    cust_address   char(50)  null,
    cust_city      char(50)  null,
    cust_state     char(5)   null,
    cust_zip       char(10)  null,
    cust_country   char(50)  null,
   cust_contact   char(50)  null,
   cust_email     char(255) null,
   primary key (cust_id)
) ENGINE=InnoDB

创建主键，主键值必须唯一。
1.如果主键使用单个列，则它的值必须唯一。
2.如果使用多个列，则这些列的组合值必须唯一。
auto_increment 自动增量
相关命令
select last_insert_id() #获取最后一个auto_increment

引擎说明
InnoDB是一个可靠的事务处理引擎，它不支持全文本搜索；
MEMORY在功能等同MyISAM，但由于数据存储在内存，速度很快（适用于临时表）
MyISAM是一个性能极高的引擎，它支持全文本搜索，但不支持事务处理。
外键不能跨引擎，即使用一个引擎的表不能引用具有使用不同引擎的表的外键
更新表 alter table tablename
删除表 drop table tablename
重命名表 rename table newtablename to pasttablename
第22章 使用视图 view
视图只包含使用时动态检索数据的查询  将整个查询包装成一个虚拟表
作为视图，它不包含表中应该有的任何列或数据，它包含的是一个SQL查询
为什么使用视图
重用SQL语句；
简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节。
使用表的组成部分而不是整个表。
保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限
更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。
性能问题 
因为视图不包括数据，所以每次使用视图时，都必须处理查询执行时所需的任一个检索。如果你用多个联结和过滤创建了复杂的视图或者嵌套了视图，可能会发现性能下降得很厉害。因此，在部署使用了大量视图的应用前，应该进行测试。
视图的规则和限制
视图必须唯一命名
对于可以创建的视图数目没有限制
为了创建视图，必须具有足够的访问权限。这些限制通常由数据库管理员授予。
视图可以嵌套。
order by可以用在视图中。
视图不能索引
视图可以和表一起使用。
使用视图
视图用create view语句来创建
使用show create view viewname;来查看创建视图的语句
用drop删除视图，其语法为drop view viewname
更新视图时，可以先用drop再用create，也可以直接用create or replace view
视图一般用于检索
第23章 使用存储过程 procedure
简单来说，存储过程就是为以后的使用而保存的一条或多条MySQL语句的集合。可将其视为批文件，虽然它们的作用不仅限于批处理。
为什么要使用存储过程
通常把处理封装在容易使用的单元中，简化复杂的操作
由于不要求反复建立一系列处理步骤，这保证了数据的完整性。如果所有开发人员和应用程序都使用同一（试验和测试）存储过程，则所使用的代码都是相同的。这一点的延伸就防止错误。需要执行的步骤越多，出错的可能性就越大。防止错误保证了数据的一致性。
简化对变动的管理。如果表名、列名或业务逻辑（或别的内容）有变化，只需要更改存储过程的代码。使用它的人员甚至不需要知道这些变化。；
提高性能。因为使用存储过程比使用单独的SQL语句要快。
存在一些只能用在单个请求中的MySQL元素和特性，存储过程可以使用它们来编写功能更强更灵活的代码。
一句话，使用存储过程有3个主要的好处，即简单、安全、高性能。
缺陷
存储过程的编写比基本SQL语句复杂，编写存储过程需要更高的技能，更丰富的经验。
你可能没有创建存储过程的安全访问权限。许多数据库管理员限制存储过程的创建权限，允许用户使用存储过程，但不允许他们创建存储过程。
执行存储过程
MySQL执行存储过程的语句为CALL
CALL productpricing(@pricelow,
                    @pricehigh,
                    @priceaverage);

执行名为productpricing的存储过程，它计算并返回产品的最低、最高和平均价格。
一个返回产品平均价格的存储过程
create procedure productpricing()
begin
    select ave(prod_price) as priceaverage
    from products;
end:

调用该存储过程
CALL productpricing();

删除存储过程
//没有使用后面的(),只给出存储过程名
drop procedure productpricing;

MySQL命令行创建存储过程的办法（;识别异常）
delimiter //
create procedure productpricing()
begin
    select avg(prod_price) as priceaverage
    from products;
end //
delimiter ;

delimiter // 定义//作为新的语法结束分隔符
存储过程中的变量
接收3个参数的存储过程
create procedure productpricing(
    out pl decimal(8,2),
    out ph decimal(8,2),
    out pa decimal(8,2)
)
begin
    select min(prod_price)
    into pl
    from products;
    select Max(prod_price)
    into ph
    from products;
    select avg(prod_price)
    into pa
    from products;
end;

此存储过程接收3个参数：pl存储产品最低价格，ph存储产品最高价格，pa存储产品平均价格。
关键字out支出相应的参数用来从存储过程传出一个值（返回给调用者）。
MySQL支持IN（传递给存储过程）、OUT（从存储过程传出，如这里所用）和INOUT（对存储过程传入和传出）类型的参数。存储过程的代码位于BEGIN和END语句内

ordertotal接收订单号并返回该订单的合计
create procedure ordertotal(
    IN onumber INT,
    OUT ototal DECIMAL(8,2)
)
BEGIN
    SELECT Sum(item_price*quantity)
    FROM orderitems
    WHERE order_num = onumber
    INTO ototal;
END;

调用该存储过程的方式 第一个参数为订单号，第二个参数为包含计算出来的合计的变量名
CALL ordertotal(20005,@total);

显示合计
SELECT @total

得到另一个订单的合计显示，需要再次调用存储过程，然后重新显示变量：
CALL ordertotal(20009,@total);
SELECT @total:

建立智能存储过程
获得合计
把营业税有条件地添加到合计；
返回合计
-- name：ordertotal
-- Parameters:onumber = order number
--            taxable = 0 if note taxable , 1 if taxable
--            ototal = order total variable
create procedure ordertotal(
    in onumber int,
    in taxable boolean,
    out ototal decimal(8,2)
) comment 'Obtain order total, optionally adding tax'
BEGIN
    -- Declare variable for total
    DECLARE total DECIMAL(8,2);
    -- Declare tax percentage
    DECLARE taxrate INT DEFAULT 6;
    
    -- Get the order total
    SELECT Sum(item_price*quantity)
    FROM orderitems
    WHERE order_num = onumber
    INTO total;
    
    -- Is this taxable?
    IF taxable THEN
        -- Yes, so add taxrate to the total
        SELECT total+(total/100*taxrate) INTO total;
    END IF;
    -- And finally, save to out variable
    SELECT total INTO ototal;
    
END;

declare定义了两个局部变量，布尔值taxable进行if判断

检查存储过程 
显示创建存储过程的语句
show create procedure ordertotal;

获得包括何时、由谁创建等详细信息的存储过程列表
show procedure status

限制过程状态输出结果
show procedure status like 'ordertotal';

第24章 使用游标 cursor
游标是一个存储在MySQL服务器上的数据库查询，它不是一条select语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据
使用游标的步骤
在能够使用游标前，必须声明它。
一旦声明后，必须打开游标以供使用。
对于填有数据的游标，根据需要取出（检索）各行
在结束游标使用时，必须关闭游标

创建
定义了名为ordernumbers的游标，使用了可以检索所有订单的select语句。
create procedure processorders()
begin
    declare ordernumbers cursor
    for
    select order_num from orders;
end;

declare语句用来定义和命名游标，这里为ordernumbers。
存储过程完成后，游标消失（局限于存储过程中）

打开和关闭
open cursor打开
open ordernumbers;

close cursor关闭
close ordernumbers;

close释放游标使用的所有内部内存和资源，因此在每个游标不再需要时都应该关闭。
修改上一个例子
create procedure processorders()
begin
    -- Declare the cursor
    declare ordernumbers cursor
    for
    select order_num from orders;
    
    -- Open the cursor
    open ordernumbers;
   -- Close the cursor
   close ordernumbers;
end;



使用游标数据 fetch 检索当前行的order_num列到一个名为o的局部声明变量中。对检索出的数据不做任何处理。
create procedure processorders()
begin
    -- Declare local variables
    declare o int;
    -- Declare the cursor
    declare ordernumbers cursor
    for
    select order_num from orders;
    
   -- Open the cursor
   open ordernumbers;
   
   -- Get order number
   fetch ordernumbers into o;
   
   -- Close the cursor
   clore ordernumbers;

end;


与上一例不同的是，fetch在repeat内，因此它反复执行直到done为真（until done end repeat）
create procedure processorders()
begin
    -- Declare local variables
    DECLARE done BOOLEAN DEFAULT 0;
    DECLARE o INT;
    
    -- Declare the cursor
    DECLARE ordernumbers CURSOR
    FOR
    SELECT order_num FROM orders;
   -- Declare continue handler
   declare continue handler for sqlstate '02000' set done = 1;
   
   -- Open the cursor
   open ordernumbers;
   
   --Loop through all rows
   repeat
       
       -- Get order number
       fetch ordernumbers into 0;
   
   -- End of loop
   until done end repeat;
   
   -- Close the cursor
   close ordernumbers;
end;

declare定义的局部变量必须在定义任意游标或句柄之前定义，而句柄必须在游标之后定义。
进一步修改版本 增加了变量t（存储每个订单的合计）创建一个新表ordertotals保存存储结果。
fetch每个order_num,然后用call执行另一个存储过程来计算每个订单的带税的合计（结果存储到t).
最后用insert保存每个订单的订单号和合计
create procedure processorders()
begin
    -- Declare local variables
    declare done boolean default 0;
    declare o int;
    declare t decimal(8,2);
    
    -- Declare the cursor
    declare ordernumbers cursor
   for
   select order_num from orders;
   -- Declare continue handler
   declare continue handler for sqlstate '02000' set done = 1;
   
   -- Create a table to store the results
   create table if not exists ordertotals
       (order_num int, total decimal(8,2));
       
   -- Open the curosr 
   open ordernumbers;
   
   -- Loop through all rows
   repeat
   
       -- Get order number
       fetch ordernumbers into 0;
       
       -- Get the total for this order
       call ordertotal(o,1,t);
       
       -- Insert order and total into ordertotals
       insert into ordertotal(order_num,total)
       values(o,t);
       
       -- End of loop
       until done end repeat;
       
       -- close the cursor
       close ordernumbers;
end;

第25章 使用触发器
场景：需要某条(或某些)语句在事件发生时自动执行
触发器是响应以下任意语句而自动执行的一条MySQL语句（或位于begin和end之间的一组语句）：
DELETE;
INSERT;
UPDATE;
其他MySQL语句不支持触发器。
创建触发器
需要给出4条信息：
唯一的触发器名（表唯一，数据库不唯一）；
触发器关联的表；
触发器应该响应的活动（delete，insert或update）；
触发器何时执行（处理之前或之后）
触发器用create trigger语句创建。
简单的创建一个名为newproduct的新触发器
只有表才支持触发器，视图不支持
create trigger newproduct 
after insert on products for each row
select 'Product added';

after insert on product	在products表的insert语句成功执行后执行
for each row 	代码对每个插入行执行

触发器按每个表每个事件每次地定义，每个表每个事件每次只允许一个触发器
所以每个表最多支持6个触发器（insert,update,delete 之前和之后）
单一触发器不能与多个事件或多个表关联
如果需要一个对insert和update操作执行的触发器，则应该定义两个触发器
删除触发器
drop trigger newproduct;

触发器不能更新或覆盖。为了修改一个触发器，必须先删除它，然后再重新创建
使用触发器
insert
在insert触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行；
在before insert触发器中，new中的值也可以被更新（允许更改被插入的值）
对于auto_increment列，new在insert执行之前包含0，在insert执行之后包含新的自动生成值。
create trigger neworder 
after insert on orders
for each orw select new.order_num;

创建名为neworder的触发器
orders表插入后执行
生成一个新订单号并保存到order_num中。
delete
在delete触发器代码内，你可以引用一个名为OLD的虚拟表，访问被删除的行；
OLD的值全部都是只读的，不能更新；
使用OLD保存将要被删除的行到一个存档表中
create trigger deleteorder before delete on orders
for each row
begin
    insert into archive_orders(order_num,order_date,cust_id)
    values(old.order_num,old.order_date,old.cust_id);
end;

任意订单被删除前执行该触发器，使用一条insert语句将old中要被删除的订单保存到一个名为archive_orders的存档表中
使用before delete触发器的优点，如果订单不能存档，delete本身将被放弃。
update
在update触发器代码中，你可以引用一个名为old的虚拟表访问以前的值，引用一个名为new的虚拟表访问更新的值；
在before update触发器中，new中的值可以也被更新（允许更改将要用于update语句中的值）；
old中的值全都是只读的，不能更新。
保证州名称缩写总是大写（不管update语句中是否大写）
create trigger updatevendor before update on vendors
for each row set new.vend_state = upper(new.vend_state);

使用触发器注意事项
MySQL 5 中的触发器相当初级。未来可能会改进
创建触发器可能需要特殊的安全访问权限，但是，触发器的执行是自动的。如果insert、update或delete语句能够执行，则相关触发器也能执行；
应该用触发器来保证数据的一致性（大小写、格式等）。在触发器中执行这种类型的处理的优点是它总是进行这种处理，而且是透明的进行，与客户机应用无关。
触发器的一种非常有意义的使用时创建审计追踪。使用触发器，把更改记录到另外一个表非常容易；
触发器不支持call语句，不能从触发器内调用存储过程。所需的存储过程代码需要复制到触发器内；
第26章 管理事务处理 commit rollback
MyISAM不支持事务，而InnoDB支持
术语：
事务 transaction 指一组SQL语句；
回退 rollback 指撤销指定SQL语句的过程；
提交 commit 指将未存储的SQL语句结果写入数据库表；
保留点 savepoint 指事务处理中设置的临时占位符 placeholder ，你可以对它发布回退（与回退整个事务处理不同
标识事务的开始：
start transaction


MySQL的ROLLBACK命令用来回退（撤销）MySQL语句，请看下面的语句：
select * from ordertotals;
start transaction;
delete from ordertotals;
select * from ordertotals;
ROLLBACK;
select * from ordertotals;

执行select，显示该表不为空；
开始事务处理transaction；
delete语句删除ordertotals所有行；
执行select，显示该表为空；
rollback回退事务transaction之后的所有语句；
执行select，显示回退成功（表中有数据）
rollback只能在一个事务处理内使用（在执行一条start transaction命令之后）
可以回退的语句：insert、update和delete
不可回退的语句：create、drop（执行回退不会被撤销）
使用commit
MySQL的提交操作是自动进行而；
在事务处理块，提交不会隐含进行。为进行明确的提交，使用commit语句，如下所示：
start transaction;
delete from orderitems where order_num = 20010;
delete from orders where order_num = 20010;
commit;

从系统中完全删除订单20010，涉及更新两个数据库表orders和orderItems，所以使用事务处理块来保证订单不被部分删除。
commit语句仅在不出错时写出更改。
如果第一条delete起作用，第二条失败，则delete不会提交（自动撤销）
当commit或rollback语句执行后，事务会自动关闭
使用保留点 savepoint
用于复杂的事务处理情况（部分提交或回退）
创建占位符
savepoint delete1;

回退到占位符
rollback to delete1;

保留点不设个数上限，越多越能灵活运用
保留点在事务处理完成后自动释放，也可以用
release savepoint

明确的释放保留点
更改默认的提交行为（指示MySQL不自动提交）
set autocommit = 0;

autocommit标志是针对每个连接而不是服务器
第27章 全球化和本地化
术语说明
字符集 为字母和符号的集合；
编码 为某个字符集成员的内部表示；
校对 为规定字符如何比较的指令；
使用字符集和校对顺序
显示所有可用的字符集以及每个字符集的描述和默认校对
show character set;

显示所有可用的校对，以及它们适用的字符集
show collation

确定创建数据库时默认的字符集和校对
show variables like 'character%';
show variables like 'collation%';

创建数据库时指定字符集和校对（创建一个包含两列的表，并且指定一个字符集和一个校对顺序）
create table mytable
(
    column1 INT,
    column2 VARCHAR(10)
) default character set hebrew
collate hebrew_general_ci;

MySQL默认指定字符集和校对的方式
如果指定character set和collate，则使用这些值；
如果只指定character set，则使用此字符集及其默认的校对（show charater set的输出）；
如果都不指定，则使用数据库默认；
可对每个列单独设置，如
create table mytable
(
    column1 INT,
    column2 VARCHAR(10),
    column3 VARCHAR(10) character set latin1 collate latin1_general_ci
) default character set hebrew
collate hebrew_general_ci;

可在select语句中进行(使用collate执行一个备用的校对顺序，影响结果的排序次序)
select * from customers
order by lastname, firstname collate latin1_general_cs;

此外 collate还可以用于group by、having、聚集函数、别名
如果绝对需要，串（字符串）可以在字符集之间进行转换。为此，使用Cast()或Convert()函数
第28章 安全管理
访问控制
用户应该对它们需要的数据具有适当的访问权，既不能多也不能少。换句话说，用户不能对过多的数据具有过多的访问权。
多数用户值需要对表进行读和写，但少数用户甚至需要能创建和删除表；
某些用户需要读表，但可能不需要更新表；
你可能想允许用户添加数据，但不允许他们删除数据；
某些用户（管理员）可能需要处理用户账号的权限，但多数用户不需要；
你可能想让用户通过存储过程访问数据，但不允许他们直接访问数据；
你可能想根据用户登录的地点限制对某些功能的访问；
防止无意的错误
不要使用root

管理用户
获取所有用户账号列表
use mysql;
select user from user;


创建用户账号
create user ben identified by 'p@$$wOrd';

重命名
rename user ben to bforta;

删除用户账号
drop user bforta;

查看赋予用户账号的权限
show grants for bforta;


使用grant设置权限
要授予的权限；
被授予访问权限的数据库或表；
用户名。
允许用户bforta在crashcourse数据库的所有表中的数据具有只读权限
grant select on crashcourse.* to bforta;

移除上述权限
revoke select on crashcourse.* from bforta;

grant和revoke可在几个层次上控制访问权限
整个服务器，使用grant all和revoke all;
整个数据库，使用on database.*
特定的表，使用on database.table;
特定的列；
特定的存储过程
权限 
权限 说明
all 除grant option 外的所有权限
alter 使用alter table
alter routine 使用alter procedure和drop procedure
create 使用create table
... ...
结合上表可以对用户权限进行完全的控制
简化多次授权
grant select,insert on crashcourse.* to btora;


更改口令（密码）
set password for bforta = Password('n3w p@$$wOrd');

设置自己的口令
set password = Password('n3w p@$$wOrd');

第29章 数据库维护
备份数据 mysqldump
MySQL数据库热备份可能解决方案
使用命令行实用程序mysqldump转储所有数据库内容到某个外部文件。在进行常规备份前这个使用程序应该正常运行，以便能正确地备份转储文件。
可用命令行实用程序mysqlhotcopy从一个数据库复制所有数据（并非所有数据库引擎都支持这个实用程序）
可以使用MySQL的BACKUP TABLE 或SELECT INTO OUTFILE转储所有数据到某个外部文件。这两条语句都接收将要创建的系统文件名，此系统文件必须不存在，否则会出错。数据可以用RESTORE TABLE来复原。
为了保证所有数据被写到磁盘（包括索引数据），可能需要在进行备份前使用FLUSH TABLES语句
进行数据库维护
检查表键是否正确 analyze table
analyze table orders;

针对许多问题对表进行检查 check table
check table orders, orderitems;

诊断启动问题
服务器启动问题通常在对MySQL配置或服务器本身进行更改时出现。MySQL在这个问题发生时报告错误，但由于多数MySQL服务器是作为系统进程或服务自动启动的，这些消息可能看不到。
添加参数进行手动启动查看问题
重要的mysqld命令行选项：
--help 显示帮助——一个选项列表；
--safe-mode装载减去某些最佳配置的服务器；
--verbose显示全文本消息（为获得更详细的帮助消息与--help联合使用）；
--version显示版本信息然后退出；
查看日志文件
日志分类 位于data目录中
错误日志：hostname.err 包含启动和关闭问题以及任意关键错误的细节 用--log-error更改
查询日志：hostname.log 记录所有MySQL活动 --log命令行选项更改
二进制日志：hostname-bin --log-bin更改
缓慢查询日志：hostname-slow.log 记录执行缓慢的任何查询 用--log-slow-queries命令更改
第30章 改善性能
改善性能
回顾前面内容，提供性能优化探讨和分析的一个出发点
首先，MySQL具有特定的硬件建议（最低配置）
一般来说，关键的生产DBMS应该运行在自己的专用服务器上。
MySQL是用一系列的默认设置预先配置的，根据使用情况酌情调整内存分配，缓冲区大小等（show variables；show status；）
MySQL一个多用户多线程的DBMS，换言之，它经常同时执行多个任务。发现显著性能不良时使用SHOW PROCESSLIST显示所有活动进程排查。用KILL命令结束某个进程
总是有不止一种方法编写同一条select语句。应该试验联结、并、子查询等，找出最佳方法
使用explain语句让MySQL解释它将如何执行一条select语句；
一般来说，存储过程执行得比一条一条地执行其中的各条MySQL语句快；
应该总是使用正确的数据类型；
决不要检索比需求还要多的数据。换言之，不要用select *（除非真正需要每个列）
有的操作（包括insert）支持一个可选的delayed关键字，如果使用它，将把控制立即返回给调用程序，并且一旦有可能就实际执行该操作；
在导入数据时，应该关闭自动提交。你可能还想删除索引（包括fulltext索引），然后再导入完成后再重建它们；
必须索引数据库表以改善数据检索的性能。
使用select语句和连接它们的union语句代替or条件；
索引改善数据检索的性能，但损害数据插入、删除和更新的性能；
like很慢。一般来说，最好是使用fulltext而不是like；
数据库是不断变化的实体。需要根据实际情况灵活变动；
以上每条规则在某些条件下都会被打破；
附录C MySQL语句的语法
更新已存在表的模式 alter table
alter table tablename
(
    add       column         datatype    [NULL|NOT NULL]    [CONSTRAINTS],
    change    column columns datatype    [NULL|NOT NULL]    [CONSTRAINTS],
    drop      column,
    ...
)

将事务处理写到数据库 commit
commit;

创建索引 create index
create index indexname
on tablename (column [ASC|DESC], ...);

创建存储过程 create procedure
create procedure procedurename( [parameters] )
begin
...
end;

创建新数据库表 create table
create table tablename
(
    column       datatype    [NULL|NOT NULL]    [CONSTRAINTS],
    column       datatype    [NULL|NOT NULL]    [CONSTRAINTS],
    ...
);

向系统添加新的用户账户 create user
create user username[@hostname]
[IDENTIFIED BY [PASSWORD] 'pass;word']

创建一个或多个表上的新视图 create view
create [OR REPLACE] view viewname
as
select ...;

删除一行或多行 delete
deletefrom tablename
[where ...];

永久地删除数据库对象（表、视图、索引等） drop
drop database|index|procedure|table|trigger|user|view
    itename;

给表增加一行 insert
insert into tablename [(columns, ...)]
values(values, ...);

插入select的结果到一个表 insert select
insert into tablename [(columns, ...)]
seelct columns, ... from tablename, ...
[where ...];

撤销一个事务处理块 rollback
rollback [ to savepointname];

为使用rollback语句设立保留点 savepoint
savepoint sp1;

检索数据 select
select ...

开始一个新的事务处理块 start transaction
start transaction;

更新表中的一行或多行 update
update tablename
set columname = value, ...
[where ...];

附录D MySQL数据类型
串数据类型
char			1~255个字符的定长串。它的长度必须在创建时指定，否则MySQL假定为CHAR(1)
enum		接口最多64K个串组成的一个预定义集合的某个串
longtext		与text相同，但最大程度为4GB
mediumtext	与text相同，但最大长度为16K
set			接受最多64个串组成的一个预定义集合的零个或多个串
text			最大长度为64K的变长文本
tinytext		与text相同，但最大长度为255字节
varchar		长度可变，最多不超过255字节。如果在创建时指定为varchar（n），则可存储0到n
   个字符的变长串（其中n<=255)
数值数据类型
bit			位字段，1~64位
bigint		整数值,支持-9223372036854775808~9223372036854775807
boolean		布尔标志，或者为0或者为1，主要用于开/关(on/off)标志
decimal		精度可变的浮点值
float			单精度浮点值
int			整数值，支持-8388608~8388607
real			4字节的浮点值
smallint		整数值，支持-32768~32767
tinyint		整数值，支持-128~127
日期和时间数据类型
date			表示1000-01-01~9999-12-31的日期，格式为YYYY-MM-DD
datetime		date和time的组合
timestamp	功能和datetime相同（但范围较小）
time			格式为HH:MM:SS
year			用二位数字表示，范围是70（1970年）~69（2069年），用4位数字表示，范围是
   1901年~2155年
二进制数据类型 可存储任何数据，如图像、多媒体、字处理文档等
blob			blob最大长度为64KB
mediumblob	blob最大长度为16MB
longblob		blob最大长度为4GB
tinyblob		blob最大长度为255字节
MySQL保留字
...

mysql事务隔离级别
1.读未提交
2.读已提交
3.可重复读
4.串行化