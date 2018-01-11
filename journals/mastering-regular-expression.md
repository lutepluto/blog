# Mastering Regular Expression

## Introduction to Regular Expressions

### 解决实际问题

- doubled-word dection
- 确认大量文件中所有的 ‘SetSize’ 都包含在 'ResetSize' 当中
- 打印邮件服务器的所有邮件中某一行 `^(From|Subject):`

### 正则表达式是一门语言

Linux shell 中经常使用到的通配符（wildcard），如：`*.txt`。

正则表达式由 metacharacters 和 literal 组成。就像语言学习一样，一个从没有学习过日语的人是完全无法理解看到的日语。一个对正则表达式没有人经验的人，在看到一段正则表达式的时候也无法理解它所表达的含义。作者鼓励读者一切都会随着学习的深入明确清晰起来。这里还举了一个 Perl 查找 IP 的例子

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

HTML 标签 `<.*>` 但是会匹配到不止一个标签元素，如 `<i>short</i>`

时间，12小时制 `(1[012]|[1-9]):[0-5][0-9]` ，24小时制 `(2[0-3]|[01]?[0-9]):[0-5][0-9]`


