
LispReader 读取clojure源文件生成相应的数据结构

读取算法：
每次读取一个字符
如果是空白字符或者','，则当做空白，跳过去
如果第一个字符是数字，则按数字读取
如果第一个字符是宏字符，则根据宏规则读取
如果第一个字符是+,第二个字符是数字，则按照数字读取
剩余的按token规则读取

数字读取：
一直读取到空白字符，或者终结字符，或者宏字符
对读取的字符串进行解析
数字类型
整型
十进制 123， 八进制 0234，十六进制 0x12A, 任意进制 7r23(进制基数在前), 大整数 123N
浮点型
123.4E45M
有理数
12/7

Token读取
一直读取到空白字符，或终结字符，或者宏字符除了#'%, (说明这3个宏字符可以出现在符号命名中 (def a'#% 1))
对字符进行解释
分类
false, true
nil
symbol   abc
keyword  :a ::a(带命名空间的)

Macro读取
"" 字符串 "ma"
； 注释  ;test
' 引用 'abc
@ 用引  @a
^ 添加元数据  ^aa 'symbol ^:tag symbol ^{:a 1} 'symbol
` 语法引用，主要用于宏
~ 宏中逃离符号
~@ 宏中扩展集合
(, {, [ 读取list, map, vector
\ 读取字符  \a \space \u1234 \0123
#^ 添加元数据， #' var #" 正则表达式 #( 匿名函数 #{ set #= evel
除了正常的#外，还支持#user.A{:a, 1}, #user.A[1]


Clojure数据结构
nil
Boolean (false, true)
number
int
float
ratio
collection
list (
vector [
map {
set #{
record
defrecord
deftype
protocol
​defprotocol
