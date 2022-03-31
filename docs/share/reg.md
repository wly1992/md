# JS正则表达式详解

正则表达式非常有用，查找、匹配、处理字符串、替换和转换字符串，输入输出等。下面整理一些常用的正则表达式。

## 常用的正则字符

> 大写表示“非”，d表示digit数字。非数字就是\D, w表示word，非单词就是\W 

1.`\` : 将下一个字符标记符、或一个向后引用、或一个八进制转义符。例如，`\\n`匹配`\n`。`\n`匹配换行符。序列`\\`匹配`\`而`\(`则匹配`(`。即相当于多种编程语言中都有的“转义字符”的概念。

2.`^`：匹配输入字符串的开始位置。如果设置了 RegExp 对象的 Multiline 属性，^ 也匹配 `\n` 或 `\r` 之后的位置。

3.`$`：匹配输入字符串的结束位置。如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 `\n` 或 `\r` 之前的位置。

4.`*`：匹配前面的子表达式零次或多次。例如，`zo*` 能匹配 "z" 以及 "zoo"。 等价于`0{0,}`。

5.`+`：匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 `{1,}`。

6.`?`：匹配前面的子表达式零次或一次。例如，"do(es)?" 可以匹配 "do" 或 "does" 中的"do" 。? 等价于 {0,1}。

7.`{n}`：n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。

8.`{n,}`：n 是一个非负整数。至少匹配n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。

9.`{n,m}`：m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。例如，"o{1,3}" 将匹配 "fooooood" 中的前三个 o。'o{0,1}' 等价于 'o?'。请注意在逗号和两个数之间不能有空格。

10.`?`：当该字符紧跟在任何一个其他限制符 (*, +, ?, {n}, {n,}, {n,m}) 后面时，匹配模式是非贪婪的。非贪婪模式尽可能少的匹配所搜索的字符串，而默认的贪婪模式则尽可能多的匹配所搜索的字符串。例如，对于字符串 "oooo"，`o+`将尽可能多的匹配'o'，得到结果['oooo']，而'o+?' 将匹配单个 "o"，得到结果 ['o', 'o', 'o', 'o']。

11.`·`：匹配除 `\r\n` 之外的任何单个字符，相等于 `[^\n\r]`。要匹配包括 `\r\n` 在内的任何字符，请使用象 `[\s\S]` 的模式。

12.`(pattern)`：匹配 `pattern` 并获取这一匹配。所获取的匹配可以从产生的 Matches 集合得到，在VBScript 中使用 SubMatches 集合，在JScript 中则使用 `$0…$9` 属性。要匹配圆括号字符，请使用 '(' 或 ')'。

13.`(?:pattern)`：匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用 "或" 字符 `(|)` 来组合一个模式的各个部分是很有用。例如， 'industr(?:y|ies) 就是一个比 'industry|industries' 更简略的表达式。

14.`(?=pattern)`：正向预查，在任何匹配 `pattern` 的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如，'Windows (?=95|98|NT|2000)' 能匹配 "Windows 2000" 中的 "Windows" ，但不能匹配 "Windows 3.1" 中的 "Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

15.`(?!pattern)`：负向预查，在任何不匹配 pattern 的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如'Windows (?!95|98|NT|2000)' 能匹配 "Windows 3.1" 中的 "Windows"，但不能匹配 "Windows 2000" 中的 "Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始

16.`x|y`：匹配 x 或 y。例如，'z|food' 能匹配 "z" 或 "food"。'(z|f)ood' 则匹配 "zood" 或 "food"。

17.`[xyz]`：字符集合。匹配所包含的任意一个字符。例如， `[abc]` 可以匹配 "plain" 中的 'a'。

18.`[^xyz]`：负值字符集合。匹配未包含的任意字符。例如， `[^abc]` 可以匹配 "plain" 中的'plin'。

19.`[a-z]`：字符范围。匹配指定范围内的任意字符。例如，`[a-z]` 可以匹配 'a' 到 'z' 范围内的任意小写字母字符。

20.`[^a-z]`：负值字符范围。匹配任何不在指定范围内的任意字符。例如，`[^a-z]` 可以匹配任何不在 'a' 到 'z' 范围内的任意字符。

21.`\b`：匹配一个单词边界，也就是指单词和空格间的位置（即正则表达式的“匹配”有两种概念，一种是匹配字符，一种是匹配位置，这里的\b就是匹配位置的）。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。

22.`\B`：匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。

23.`\cx`：匹配由 x 指明的控制字符。例如， `\cM` 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符。

24.`\d`：匹配一个数字字符。等价于 `[0-9]`。

25.`\D`：匹配一个非数字字符。等价于`[^0-9]`。

26.`\f`：匹配一个换页符。等价于 `\x0c` 和 `\cL`。

27.`\n`：匹配一个换行符。等价于 `\x0a` 和 `\cJ`。

28.`\r`：匹配一个回车符。等价于 `\x0d` 和 `\cM`。

29.`\s`：匹配任何空白字符，包括空格、制表符、换页符等等。等价于 `[\f\n\r\t\v]`。

30.`\S`：匹配任何非空白字符。等价于 `[^\f\n\r\t\v]`。

31.`\t`：匹配一个制表符。等价于 `\x09` 和 `\cI`。

32.`\v`：匹配一个垂直制表符。等价于 `\x0b` 和 `\cK`。

33.\w：匹配包括下划线的任何单词字符。类似但不等价于`[A-Za-z0-9_]`。

34.\W：匹配任何非单词字符。等价于 `[^A-Za-z0-9_]`。

35.`\xn`：匹配 n，其中 n 为十六进制转义值。十六进制转义值必须为确定的两个数字长。例如，'\x41' 匹配 "A"。'\x041' 则等价于 '\x04' & "1"。正则表达式中可以使用 ASCII 编码。

36.`\num`：匹配 num，其中 num 是一个正整数。对所获取的匹配的引用。例如，'(.)\1' 匹配两个连续的相同字符。

37.`\n`：标识一个八进制转义值或一个向后引用。如果 \n 之前至少 n 个获取的子表达式，则 n 为向后引用。否则，如果 n 为八进制数字 (0-7)，则 n 为一个八进制转义值。

38.`\nm`：标识一个八进制转义值或一个向后引用。如果 \nm 之前至少有 nm 个获得子表达式，则 nm 为向后引用。如果 \nm 之前至少有 n 个获取，则 n 为一个后跟文字 m 的向后引用。如果前面的条件都不满足，若 n 和 m 均为八进制数字 (0-7)，则 \nm 将匹配八进制转义值 nm。

39.`\nml`：如果 n 为八进制数字 (0-3)，且 m 和 l 均为八进制数字 (0-7)，则匹配八进制转义值 nml。
40.`\un`：匹配 n，其中 n 是一个用四个十六进制数字表示的 Unicode 字符。例如， `\u00A9` 匹配版权符号 (?)。


## 正则表达式 - 修饰符（标记）

```js
var expression = /pattern/flags;
```

- g：表示全局模式,即模式将被应用到所有字符串，而非在发现第一个匹配项时立即停止。

- i：表示不区分大小写模式。

- m：表示多行模式，即在到达一行文本末尾时还在继续查找下一行中是否存在于模式匹配的项。

- s: 特殊字符圆点 `.` 中包含换行符 `\n`，默认情况下的圆点 `.` 是匹配除换行符 `\n` 之外的任何字符

## 正则表达式定义方式

- 以字面量的形式来定义正则表达式

```js
var pattern = /[bc]at/i;
```

- 使用RegExp构造函数

它接收两个参数：一个是要匹配的字符串模式，另一个是可选的标志字符串。可以使用字面量定义的任何表达式，都可以使用构造函数来定义，还是以上面的例子为例：

```js
var pattern = new RegExp("[bc]at","i");
```

> 注意:RegExp构造函数模式参数时字符串，所以在某些情况下要对字符进项双重转义。所有元字符都必须双重转义，如字面量模式为`/\[bc\]at/`,那么等价的字符串为`/\\[bc\\]at/`

- 扩展：正则表达式中的RegExp.$1

`RegExp` 是`javascript`中的一个内置对象。为正则表达式。

`RegExp.$1`是`RegExp`的一个属性,指的是与正则表达式匹配的第一个子匹配(以括号为标志)字符串，以此类推，RegExp.$2, RegExp.$3, ..RegExp.$99总共可以有99个匹配。

```js
var r= /^(\d{4})-(\d{1,2})-(\d{1,2})$/; //正则表达式 匹配出生日期(简单匹配)    
r.exec('1985-10-15');
s1=RegExp.$1;
s2=RegExp.$2;
s3=RegExp.$3;
console.log(s1+" "+s2+" "+s3)//结果为1985 10 15
```

## 正则表达式方法

#### RegExp对象的exec()方法

该方法是专门为捕获组而设计的，其接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组；或者在没有匹配项的情况下返回null。返回的数组虽然是Array的实例，但是包含两个额外的属性：index和input。其中index表示匹配项在字符串中的位置，而input表示应用字符串表达式的字符串。

```js
var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;
var matches = pattern.exec(text);
console.log(matches.index); //0
console.log(matches.input); //mom and dad and baby
console.log(matches[0]);    //mom and dad and baby
console.log(matches[1]);    //and dad and baby
console.log(matches[2]);    //and baby
```

对于exec()方法而言，即使在模式中设置了全局标志g，它每次也只是返回一个匹配项。在不设置全局标志的情况下，在同一个字符串上多次调用exec()方法将始终返回第一个匹配项的信息。而在设置全局标志的情况下，每次调用exec()则都会在字符串中继续查找新匹配项，如下例子：

```js
var text = "cat, bat, sat, fat";
var pattern1 = /.at/;

var matches = pattern1.exec(text);
console.log(matches.index); //0
console.log(matches[0]);  //cat
console.log(pattern1.lastIndex); //0

matches = pattern1.exec(text);
console.log(matches.index); //0
console.log(matches[0]);  //cat
console.log(pattern1.lastIndex); //0

var pattern2 = /.at/g;

var matches = pattern2.exec(text);
console.log(matches.index); //0
console.log(matches[0]);  //cat
console.log(pattern2.lastIndex); //3

var matches = pattern2.exec(text);
console.log(matches.index); //5
console.log(matches[0]);  //bat
console.log(pattern2.lastIndex); //8
```

#### test()方法

正则表达式常用方法test()，它接受一个字符串参数。在模式与该参数匹配的情况下返回true，否则返回false。

用法：正则.test(字符串)

例1：判断是否是数字

```js
var str = '374829348791';
var re = /\D/;      //  \D代表非数字
if( re.test(str) ){   // 返回true,代表在字符串中找到了非数字。
    alert('不全是数字');
}else{
    alert('全是数字');
}
```

#### compile()方法

compile() 方法用于在脚本执行过程中编译正则表达式。
compile() 方法也可用于改变和重新编译正则表达式。


## 支持正则表达式的 String 对象的方法

#### search方法

在字符串搜索符合正则的内容，搜索到就返回出现的位置（从0开始，如果匹配的不只是一个字母，那只会返回第一个字母的位置）， 如果搜索失败就返回 -1

用法：字符串.search(正则)

例子：在字符串中找字母b，且不区分大小写

```js
var str = 'abcdef';
var re = /B/i;
alert( str.search(re) ); // 1
```

####  match方法

语法：
```js
stringObject.match(searchvalue)
stringObject.match(regexp)
```

- searchvalue：必需。规定要检索的字符串值。
- regexp：必需。规定要匹配的模式的 RegExp 对象。如果该参数不是 RegExp 对象，则需要首先把它传递给 RegExp 构造函数，将其转换为 RegExp 对象。

如果 regexp 没有标志 g，那么 match() 方法就只能在 stringObject 中执行一次匹配。如果没有找到任何匹配的文本， match() 将返回 null。否则，它将返回一个数组。

例如：

```js
"186a619b28".match(/\d+/g); // ["186","619","28"] 
```

如果上面的匹配不是全局匹配，那么得到的结果如下：

`["186", index: 0, input: "186a619b28"]`

#### replace方法

replace 本身是JavaScript字符串对象的一个方法，它允许接收两个参数：

replace([RegExp|String],[String|Function])

第1个参数可以是一个普通的字符串或是一个正则表达式.
第2个参数可以是一个普通的字符串或是一个回调函数.

如果第2个参数是回调函数，每匹配到一个结果就回调一次，每次回调都会传递以下参数：

- result: 本次匹配到的结果

- $1...&99: 正则表达式中有几个()，就会传递几个参数，$1...&99分别代表本次匹配中每个()提取的结果，最多99个

- offset:记录本次匹配的开始位置

- source:接受匹配的原始字符串

在本例中，我们将把 "Doe, John" 转换为 "John Doe" 的形式：

```js
name = "Doe, John";
name.replace(/(\w+)\s*, \s*(\w+)/, "$2 $1");
```

在本例中，我们将把所有的花引号替换为直引号：

```js
name = '"a", "b"';
name.replace(/"([^"]*)"/g, "'$1'");
```

在本例中，我们将把字符串中所有单词的首字母都转换为大写：

```js
name = 'aaa bbb ccc';
uw=name.replace(/\b\w+\b/g, function(word){
  return word.substring(0,1).toUpperCase()+word.substring(1);
});
```