# 正则表达

正则表达适用于字符，而不是单词，隐含串联

## 点和星号
- .点：匹配任何单个字符，c.t将匹配 单个字符c+任何字符+单个字符t
- \*星号：匹配该字符的零个或多个字符，cat\*可匹配cat、catt、cattttt以及ca

## 正则表达式三叉戟
### 锚点
锚点指定个各行的模式位置。下面是两个最重要的锚点
- ^（插入符号）将模式固定到行首。例如，模式^1 匹配以 1 开头的任意行
- $（美元符）将模式固定到句尾。例如，9$匹配以 9 结尾的任意行
### 字符集
- 单个字符
- 字符集
    - [0-9] 匹配 0…9 中的任何一个数字
    - [a-z] 匹配任何小写字母
    - [A-Z] 匹配任何大写字母
    - [A-Za-z0-9] 匹配任何大小写字母和单个数字
### 修饰符
- i, 不区分大小写
/abc/i 可以匹配 abc、aBC、Abc
- g, 查找所有匹配项
var str = 'aaaaaaaa'
var reg1 = /a/;  str.match(reg1)  // 结果为：["a", index: 0, input: "aaaaaaaa"]
var reg2 = /a/g; str.match(reg2)  // 结果为：["a", "a", "a", "a", "a", "a", "a", "a"]
- m, 多行模式
若存在换行\n并且有开始^或结束符\$的情况下，和g一起使用实现全局匹配,
因为存在换行时默认会把换行符作为一个字符任务匹配字符串是个单行，
g只匹配第一行，添加m之后实现多行，每个换行符之后就是开始
var str = "abcggab\nabcoab";
var preg1 = /^abc/gm;  str.match(preg1)  // 结果为：["abc", "abc"]
var preg2 = /ab$/gm;   str.match(preg2)  // 结果为：["ab", "ab"]
- u, 只匹配最近的一个字符串，不重复匹配
$mode="/a(.*?)c/";
$preg="/a.*c/U";//这两个正则返回相同的值
$str="abcabbbcabbbbbc" ;
preg_match($mode,$str,$content);   echo $content[0];//abc
preg_match($preg,$str,$content);   echo $content[0];//abc
