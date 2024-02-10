# Regular Expressions: Regexes in Python

For now, you’ll focus predominantly on one function, re.search().

`re.search(<regex>, <string>)`

`re.search(<regex>, <string>)` scans `<string>` looking for the first location where the pattern `<regex>` matches. If a match is found, then `re.search()` returns a match object. Otherwise, it returns `None`.

```python
>>> s = 'foo123bar'

>>> # One last reminder to import!
>>> import re

>>> re.search('123', s)
<_sre.SRE_Match object; span=(3, 6), match='123'>
```

## Python Regex Metacharacters

In a regex, a set of characters specified in square brackets ([]) makes up a character class. This metacharacter sequence matches any single character that is in the class, as demonstrated in the following example:

```python
>>> s = 'foo123bar'
>>> re.search('[0-9][0-9][0-9]', s)
<_sre.SRE_Match object; span=(3, 6), match='123'>
```
```shell
>>> re.search('[0-9][0-9][0-9]', 'foo456bar')
<_sre.SRE_Match object; span=(3, 6), match='456'>

>>> re.search('[0-9][0-9][0-9]', '234baz')
<_sre.SRE_Match object; span=(0, 3), match='234'>

>>> re.search('[0-9][0-9][0-9]', 'qux678')
<_sre.SRE_Match object; span=(3, 6), match='678'>

>>> print(re.search('[0-9][0-9][0-9]', '12foo34'))
None
```

The dot (.) metacharacter matches any character except a newline, so it functions like a wildcard:
```shell
>>> s = 'foo123bar'
>>> re.search('1.3', s)
<_sre.SRE_Match object; span=(3, 6), match='123'>

>>> s = 'foo13bar'
>>> print(re.search('1.3', s))
None
```
In the first example, the regex 1.3 matches '123' because the '1' and '3' match literally, and the . matches the '2'. Here, you’re essentially asking, “Does s contain a '1', then any character (except a newline), then a '3'?” The answer is yes for 'foo123bar' but no for 'foo13bar'.

## Metacharacters That Match a Single Character

`[]` - Specifies a specific set of characters to match.

```shell
>>> re.search('ba[artz]', 'foobarqux')
<_sre.SRE_Match object; span=(3, 6), match='bar'>
>>> re.search('ba[artz]', 'foobazqux')
<_sre.SRE_Match object; span=(3, 6), match='baz'>
```

A character class can also contain a range of characters separated by a hyphen (-), in which case it matches any single character within the range. For example, [a-z] matches any lowercase alphabetic character between 'a' and 'z', inclusive:

```shell
>>> re.search('[a-z]', 'FOObar')
<_sre.SRE_Match object; span=(3, 4), match='b'>
```
[0-9] matches any digit character:
```shell
>>> re.search('[0-9][0-9]', 'foo123bar')
<_sre.SRE_Match object; span=(3, 5), match='12'>
```
`[0-9a-fA-F]` matches any hexadecimal digit character:
```shell
>>> re.search('[0-9a-fA-f]', '--- a0 ---')
<_sre.SRE_Match object; span=(4, 5), match='a'>
```
You can complement a character class by specifying ^ as the first character, in which case it matches any character that isn’t in the set. In the following example, `[^0-9]` matches any character that isn’t a digit:
```shell
>>> re.search('[^0-9]', '12345foo')
<_sre.SRE_Match object; span=(5, 6), match='f'>
```
If a ^ character appears in a character class but isn’t the first character, then it has no special meaning and matches a literal '^' character:
```shell
>>> re.search('[#:^]', 'foo^bar:baz#qux')
<_sre.SRE_Match object; span=(3, 4), match='^'>
```
 What if you want the character class to include a literal hyphen character? You can place it as the first or last character or escape it with a backslash (\):
```shell
>>> re.search('[-abc]', '123-456')
<_sre.SRE_Match object; span=(3, 4), match='-'>
>>> re.search('[abc-]', '123-456')
<_sre.SRE_Match object; span=(3, 4), match='-'>
>>> re.search('[ab\-c]', '123-456')
<_sre.SRE_Match object; span=(3, 4), match='-'>
```
If you want to include a literal ']' in a character class, then you can place it as the first character or escape it with backslash:
```shell
>>> re.search('[]]', 'foo[1]')
<_sre.SRE_Match object; span=(5, 6), match=']'>
>>> re.search('[ab\]cd]', 'foo[1]')
<_sre.SRE_Match object; span=(5, 6), match=']'>
```
Other regex metacharacters lose their special meaning inside a character class:
```shell
>>> re.search('[)*+|]', '123*456')
<_sre.SRE_Match object; span=(3, 4), match='*'>
>>> re.search('[)*+|]', '123+456')
<_sre.SRE_Match object; span=(3, 4), match='+'>
```


`dot (.)` - Specifies a wildcard.

```shell
>>> re.search('foo.bar', 'fooxbar')
<_sre.SRE_Match object; span=(0, 7), match='fooxbar'>

>>> print(re.search('foo.bar', 'foobar'))
None
>>> print(re.search('foo.bar', 'foo\nbar'))
None
```

`\w`  `\W` - Match based on whether a character is a word character.

`\w` matches any alphanumeric word character. Word characters are uppercase and lowercase letters, digits, and the underscore (`_`) character, so `\w` is essentially shorthand for `[a-zA-Z0-9_]`:
```shell
>>> re.search('\w', '#(.a$@&')
<_sre.SRE_Match object; span=(3, 4), match='a'>
>>> re.search('[a-zA-Z0-9_]', '#(.a$@&')
<_sre.SRE_Match object; span=(3, 4), match='a'>
```
`\W` is the opposite. It matches any non-word character and is equivalent to `[^a-zA-Z0-9_]`:
```shell
>>> re.search('\W', 'a_1*3Qb')
<_sre.SRE_Match object; span=(3, 4), match='*'>
>>> re.search('[^a-zA-Z0-9_]', 'a_1*3Qb')
<_sre.SRE_Match object; span=(3, 4), match='*'>
```


`\d` `\D` - Match based on whether a character is a decimal digit.

`\d` matches any decimal digit character. `\D` is the opposite. It matches any character that isn’t a decimal digit:
```shell
>>> re.search('\d', 'abc4def')
<_sre.SRE_Match object; span=(3, 4), match='4'>

>>> re.search('\D', '234Q678')
<_sre.SRE_Match object; span=(3, 4), match='Q'>
```
`\d` is essentially equivalent to `[0-9]`, and `\D` is equivalent to `[^0-9]`.






























## Credits

- [Regular Expressions: Regexes in Python (Part 1)](https://realpython.com/regex-python/#regexes-in-python-and-their-uses)
- [Regular Expressions: Regexes in Python (Part 2)](https://realpython.com/regex-python-part-2/)