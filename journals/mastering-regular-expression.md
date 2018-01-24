# Mastering Regular Expression

## Introduction to Regular Expressions

### 解决实际问题

- doubled-word dection
- 确认大量文件中所有的 ‘SetSize’ 都包含在 'ResetSize' 当中
- 打印邮件服务器的所有邮件中某一行 `^(From|Subject):`

### 正则表达式是一门语言

Linux shell 中经常使用到的通配符（wildcard），如：`*.txt`。

正则表达式由 metacharacters 和 literal 组成。就像语言学习一样，一个从没有学习过日语的人是完全无法理解看到的日语。一个对正则表达式没有人经验的人，在看到一段正则表达式的时候也无法理解它所表达的含义。作者鼓励读者一切都会随着学习的深入明确清晰起来。这里还举了一个 Perl 查找 IP 的例子

```Perl
s!<emphasis>([0-9]+(\.[0-9]+){3})</emphasis>!<inet>$1</inet>
```

### Egrep 中的 Metacharacters

Name | Metacharacter | Mathes
---- | -------------- | -----------
caret | `^` | the poisition at the start of the line
dollar | `$` | the poisition at the end of the line
character class | `[...]` | any character listed
negated character class | `[^...]` | any character not listed
dot | `.` | any one character
backslash less-than | `\<` | the position at the start of a word
backslash greater-than | `\>` | the position at the end of a word
or; bar | `|` | mathes either subexpression it separates
parentheses | `()` | used to limit scope of `|`, provides grouping for the quantifiers, and "captures" for backreferences
backreference | `\1, \2, ...` | mathes text previously matchesd within first, second, etc., set of parentheses
question mark | `?` | 0 or 1 (one optional)
star; asterisk | `*` | 0 or unlimited (any amount OK)
plus | `+` | 1 or unlimited (at least one)
internals | `{min, max}` | at least min, at most max
escaped character | `\char` | when char is a metacharacter, or the escaped combination is not otherwise specialm, mathes the literal char

### 更多例子

变量名称 `[a-zA-Z_][a-zA-Z_0-9]*`

双引号中的字符串 `"[^"]*"`

美元金额 `\$[0-9]+(\.[0-9][0-9])?`

HTTP/HTML URL `\<http://[-a-z0-9_.:]+/[-a-z0-9_:@&?=+,.!/~*%$]*\.html?\>`

HTML 标签 `<.*>` 但是会匹配到不止一个标签元素，如 `<i>short</i>`

时间，12小时制 `(1[012]|[1-9]):[0-5][0-9]` ，24小时制 `(2[0-3]|[01]?[0-9]):[0-5][0-9]`

## Extended Introductory Examples

先介绍了一些 Perl 的简要语法。然后举了一些正则的匹配字符的例子，摄氏度和华氏度转化：
```Perl
    $input =~ m/^([-+]?[0-9]+)([CF])$/
```

支持浮点数
```Perl
    $input =~ m/^([-+]?[0-9]+(\.[0-9]*)?)([CF])$/
```

支持空白符
```Perl
    $input =~ m/^([-+]?[0-9]+(\.[0-9]*)?)\s*([CF])/i
```

同 Perl 一样，其他语言／工具普遍支持的简写形式（shorthand）：


shorthand | description
--------- | -----------
\t | tab
\n | newline
\r | carriage-return
\s | whitespace(space, tab, newline, formfeed and such)
\w | [a-zA-Z0-9_]
\W | anything not \w, i.e., [^a-zA-Z0-9_]
\d | [0-9], i.e., digit
\D | anything not \d, i.e., [^0-9]

使用正则修改字符串的例子。

- 邮件模版 `$letter =~ s/=FIRST=/$given/g`
- 美化股票价格 `$price =~ s/(\.\d\d[1-9]?)\d*/$1/`

使用 Lookaround 给数字添加逗号

- lookahead `(?=)` lookbehind `(?<=)`
- Lookaround 不占用（consume）字符
- 一些例子: `(?=Jeffrey)Jeff`,  `s/(?<=\bJeff)(?=s\b)/`
- back to the comma example: `$pop =~ s/(?<=\d)(?=(\d\d\d)+$)/,/g`
- Positive/Negative Lookbehind(Lookahead):

Type | Regex | Successful if the enclosed subexpression...
---- | ----- | -------------------------------------------
Positive Lookbehind | `(?<=...)` | successful if can match to the left
Negative Lookbehind | `(?<!...)` | successful if can not match to the left
Positive Lookahead  | `(?=...)`  | successful if can match to the right
Negative Lookahead  | `(?!...)`  | successful if can not match to the right

```Perl
$text = "The population of 298444215 is growing";
$text =~ s/(?<=\d)(?=(\d\d\d)+(?!\d))/,/g
```

### Text-to-HTML Conversion

#### 转译特殊字符

```Perl
$text =~ s/&/&amp;/g;
$text =~ s/</&lt;/g;
$text =~ s/>/&gt;/g;
```

#### 分隔段落

```Perl
$text =~ s/^\s*$/<p>/mg
```

#### 邮箱转为链接

```Perl
$text =~ s/\b(username regex\@hostname regex)\b/<a href="mailto:$1">$1<\/a>/g;
```

```Perl
$text =~ s{
    \b
    (
        \w[-.\w]*
        \@
        [-a-z0-9]+(\.[-a-z0-9]+)*\.(com|edu|info)
    )
    \b
}{<a href="mailto:$1">$1</a>}gix
```

#### 转换链接

```Perl
$text = ̃ s{
    \b
    # Captur e the URL to $1 . . .
    (
        http:// [-a-z0-9]+(\.[-a-z0-9]+)+\.(com;edu;info) \b# hostname
        (
            / [-a-z0-9_:\@&?=+,.!/~*'%\$]* # Optional path
              (?<![.,?!]) # Not allowed to end with
        )?
   )
}{<a href="$1">$1</a>}gix;
```

### Doubled-word 

```Perl
s{
    \b
    ([a-z]+)
    ### Now need to allow any number of spaces and/or <TAGS>
    (
        (?:\s|<[^>]+>)+
    )
    ### Now match the first word again
    (\1\b) # \b ensures not embeded. This copy saved to $3
}{\e[7m$1\e[m$2\e[7m$3\e[m}igx
```


