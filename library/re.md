# [6.2. re — Regular expression operations](https://docs.python.org/3.5/library/re.html)

Both patterns and strings to be searched can be Unicode strings as well as 8-bit strings. However, **Unicode strings and 8-bit strings cannot be mixed**: that is, you cannot match a Unicode string with a byte pattern or vice-versa; similarly, when asking for a substitution, **the replacement string must be of the same type as both the pattern and the search string**.

建议在正则表达式中使用 raw string

    In [10]: re.match('\\\\', '\\')
    Out[10]: <_sre.SRE_Match object; span=(0, 1), match='\\'>

    In [11]: re.match(r'\\', '\\')
    Out[11]: <_sre.SRE_Match object; span=(0, 1), match='\\'>

## 6.2.1. Regular Expression Syntax

正则表达式语法  

一些需要注意的  

Regular expression pattern strings may not contain null bytes, but can specify the null byte using a `\number` notation such as `'\x00'`.

Repetition qualifiers (`*`, `+`, `?`, `{m,n}`, etc) cannot be directly nested. This avoids ambiguity with the non-greedy modifier suffix `?`, and with other modifiers in other implementations. **To apply a second repetition to an inner repetition, parentheses may be used.** For example, the expression (?:a{6})* matches any multiple of six 'a' characters.


正向断言 `(?=...)`
负向断言 `(?<=...)`
选择匹配 `(?(id/name)yes-pattern|no-pattern)`

    In [35]: re.match('(<)?(\w+@\w+(?:\.\w+)+)(?(1)>|$)', '<user@host.com>')
    Out[35]: <_sre.SRE_Match object; span=(0, 15), match='<user@host.com>'>

    In [36]: re.match('(<)?(\w+@\w+(?:\.\w+)+)(?(1)>|$)', 'user@host.com')
    Out[36]: <_sre.SRE_Match object; span=(0, 13), match='user@host.com'>


## 6.2.2. Module Contents
The module defines several functions, constants, and an exception. Some of the functions are simplified versions of the full featured methods for compiled regular expressions.

### re.compile(pattern, flags=0)

Compile a regular expression pattern into a regular expression object, which can be used for matching using its `match()` and `search()` methods, described below.
The expression’s behaviour can be modified by specifying a flags value. Values can be any of the following variables, **combined using bitwise OR (the `|` operator).**

Note: The compiled versions of the most recent patterns passed to `re.compile()` and the module-level matching functions are cached, so programs that use only a few regular expressions at a time needn’t worry about compiling regular expressions.

调用模块级别的匹配函数会自动缓存，不必盲目使用 `re.compile`; `re.purge` 可以清除缓存  

### re.A
### re.ASCII
Make `\w`, `\W`, `\b`, `\B`, `\d`, `\D`, `\s` and `\S` perform ASCII-only matching instead of full Unicode matching. This is only meaningful for Unicode patterns, and is ignored for byte patterns.

Note that for backward compatibility(向后兼容), the re.U flag still exists (as well as its synonym re.UNICODE and its embedded counterpart `(?u)`), but these are redundant(冗余) in Python 3 since matches are Unicode by default for strings (and Unicode matching isn’t allowed for bytes).

### re.DEBUG
Display debug information about compiled expression.

### re.I
###re.IGNORECASE
Perform case-insensitive matching; expressions like `[A-Z]` will match lowercase letters, too. This is not affected by the current locale and works for Unicode characters as expected.

### re.L
### re.LOCALE
Make `\w`, `\W`, `\b`, `\B`, `\s` and `\S` dependent on the current locale. The use of this flag is discouraged as the locale mechanism is very unreliable, and it only handles one “culture” at a time anyway; you should use Unicode matching instead, which is the default in Python 3 for Unicode (str) patterns. This flag makes sense only with bytes patterns.

**Deprecated since version 3.5, will be removed in version 3.6: Deprecated the use of `re.LOCALE` with string patterns or `re.ASCII`.**


### re.M
### re.MULTILINE
When specified, the pattern character `'^'` matches at the beginning of the string and at the beginning of each line (immediately following each newline); and the pattern character `'$'` matches at the end of the string and at the end of each line (immediately preceding each newline). By default, `'^'` matches only at the beginning of the string, and `'$'` only at the end of the string and immediately before the newline (if any) at the end of the string.


### re.S
### re.DOTALL
Make the `'.'` special character match any character at all, including a newline; without this flag, `'.'` will match anything except a newline.

### re.X
### re.VERBOSE

这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释
This flag allows you to write regular expressions that look nicer and are more readable by allowing you to visually separate logical sections of the pattern and add comments. Whitespace within the pattern is ignored, except when in a character class or when preceded by an unescaped backslash. When a line contains a `#` that is not in a character class and is not preceded by an unescaped backslash, all characters from the leftmost such `#` through the end of the line are ignored.

This means that the two following regular expression objects that match a decimal number are functionally equal:

    a = re.compile(r"""\d +  # the integral part
                       \.    # the decimal point
                       \d *  # some fractional digits""", re.X)
    b = re.compile(r"\d+\.\d*")


### re.search(pattern, string, flags=0)
Scan through string **looking for the first location** where the regular expression pattern produces a match, and return a corresponding `match object`. Return `None` if no position in the string matches the pattern; note that this is different from finding a zero-length match at some point in the string.

### re.match(pattern, string, flags=0)
If **zero or more characters at the beginning of string match the regular expression pattern**, return a corresponding `match object`. Return `None` if the string does not match the pattern; note that this is different from a zero-length match.

**Note that even in `MULTILINE` mode, `re.match()` will only match at the beginning of the string and not at the beginning of each line.
If you want to locate a match anywhere in string, use search() instead (see also search() vs. match()).**

### re.fullmatch(pattern, string, flags=0)
If the **whole string matches the regular expression** pattern, return a corresponding match object. Return None if the string does not match the pattern; note that this is different from a zero-length match.


### re.split(pattern, string, maxsplit=0, flags=0)
Split string by the occurrences of pattern. If capturing parentheses are used in pattern, then the text of all groups in the pattern are also returned as part of the resulting list. If maxsplit is nonzero, at most maxsplit splits occur, and the remainder of the string is returned as the final element of the list.

按正则表达式分割字符串。如果指定捕获组，则捕获组也会返回

    >>> re.split('\W+', 'Words, words, words.')
    ['Words', 'words', 'words', '']
    >>> re.split('(\W+)', 'Words, words, words.')
    ['Words', ', ', 'words', ', ', 'words', '.', '']
    >>> re.split('\W+', 'Words, words, words.', 1)
    ['Words', 'words, words.']
    >>> re.split('[a-f]+', '0a3B9', flags=re.IGNORECASE)
    ['0', '3', '9']

**If there are capturing groups in the separator and it matches at the start of the string, the result will start with an empty string.** The same holds for the end of the string:

    >>> re.split('(\W+)', '...words, words...')
    ['', '...', 'words', ', ', 'words', '...', '']

That way, separator components are always found at the same relative indices within the result list.

Note: `split()` doesn’t currently split a string on an empty pattern match. For example:

    >>> re.split('x*', 'axbc')
    ['a', 'bc']

Even though `'x*'` also matches 0 ‘x’ before ‘a’, between ‘b’ and ‘c’, and after ‘c’, currently these matches are ignored. The correct behavior (i.e. splitting on empty matches too and returning `['', 'a', 'b', 'c', '']`) will be implemented in future versions of Python, but since this is a backward incompatible change, a `FutureWarning` will be raised in the meanwhile.

**Patterns that can only match empty strings currently never split the string.** Since this doesn’t match the expected behavior, a ValueError will be raised starting from Python 3.5:

    >>> re.split("^$", "foo\n\nbar\n", flags=re.M)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      ...
    ValueError: split() requires a non-empty pattern match.

### re.findall(pattern, string, flags=0)
Return all non-overlapping matches of pattern in string, as a list of strings. The string is scanned left-to-right, and matches are returned in the order found. If one or more groups are present in the pattern, return a list of groups; this will be a list of tuples if the pattern has more than one group. Empty matches are included in the result unless they touch the beginning of another match.

### re.finditer(pattern, string, flags=0)
Return an `iterator` yielding `match objects` over all non-overlapping matches for the RE pattern in string. The string is scanned left-to-right, and matches are returned in the order found. Empty matches are included in the result unless they touch the beginning of another match.

    In [8]: for _ in re.finditer('x*', 'axbc'):
       ...:     print(_)
       ...:     
    <_sre.SRE_Match object; span=(0, 0), match=''>
    <_sre.SRE_Match object; span=(1, 2), match='x'>
    <_sre.SRE_Match object; span=(2, 2), match=''>
    <_sre.SRE_Match object; span=(3, 3), match=''>
    <_sre.SRE_Match object; span=(4, 4), match=''>

### re.sub(pattern, repl, string, count=0, flags=0)
Return the string obtained by replacing the leftmost non-overlapping occurrences of pattern in string by the replacement repl. If the pattern isn’t found, string is returned unchanged. repl can be a string or a function; if it is a string, any backslash escapes in it are processed. That is, `\n` is converted to a single newline character, `\r` is converted to a carriage return, and so forth. Unknown escapes such as `\&` are left alone. Backreferences, such as `\6`, are replaced with the substring matched by group 6 in the pattern. For example:

    >>> re.sub(r'def\s+([a-zA-Z_][a-zA-Z_0-9]*)\s*\(\s*\):',
    ...        r'static PyObject*\npy_\1(void)\n{',
    ...        'def myfunc():')
    'static PyObject*\npy_myfunc(void)\n{'

**If repl is a function, it is called for every non-overlapping occurrence of pattern.** The function takes a single match object argument, and returns the replacement string. For example:


    >>> def dashrepl(matchobj):
    ...     if matchobj.group(0) == '-': return ' '
    ...     else: return '-'
    >>> re.sub('-{1,2}', dashrepl, 'pro----gram-files')
    'pro--gram files'
    >>> re.sub(r'\sAND\s', ' & ', 'Baked Beans And Spam', flags=re.IGNORECASE)
    'Baked Beans & Spam'

**Empty matches for the pattern are replaced only when not adjacent to a previous match, so `sub('x*', '-', 'abc')` returns ``'-a-b-c-'``.**

In string-type repl arguments, in addition to the character escapes and backreferences described above, `\g<name>` will use the substring matched by the group named `name`, as defined by the `(?P<name>...)` syntax. `\g<number>` uses the corresponding group number; **`\g<2>` is therefore equivalent to `\2`, but isn’t ambiguous in a replacement such as `\g<2>0`. `\20` would be interpreted as a reference to group 20, not a reference to group 2 followed by the literal character `'0'`.** The backreference `\g<0>` substitutes in the entire substring matched by the RE.

### re.subn(pattern, repl, string, count=0, flags=0)
Perform the same operation as `sub()`, but return a tuple `(new_string, number_of_subs_made)`.

### re.escape(string)
Escape all the characters in pattern except ASCII letters, numbers and `'_'`. This is useful if you want to match an arbitrary literal string that may have regular expression metacharacters in it.

Changed in version 3.3: The `'_'` character is no longer escaped.

### re.purge()
Clear the regular expression cache.


## 6.2.3. Regular Expression Objects
Compiled regular expression objects support the following methods and attributes:

### regex.search(string[, pos[, endpos]])
注意

the `'^'` pattern character matches at the **real beginning of the string** and at positions just after a newline, but not necessarily at the index where the search is to start.

指定 `pos` 并不意味着 `^` 从 `pos` 开始匹配  

    In [9]: rx = re.compile('^a')

    In [10]: rx.search('abc')
    Out[10]: <_sre.SRE_Match object; span=(0, 1), match='a'>

    In [11]: rx.search('babc', 1)

The optional parameter endpos limits how far the string will be searched; it will be as if the string is endpos characters long, so **only the characters from pos to `endpos - 1` will be searched for a match**.

### regex.match(string[, pos[, endpos]])

### regex.fullmatch(string[, pos[, endpos]])

### regex.split(string, maxsplit=0)

### regex.findall(string[, pos[, endpos]])

### regex.finditer(string[, pos[, endpos]])

### regex.sub(repl, string, count=0)

### regex.subn(repl, string, count=0)

### regex.flags

    >>> rx = re.compile('', re.IGNORECASE | re.DOTALL)
    >>> rx.flags
    50
    >>> re.IGNORECASE | re.DOTALL | re.U
    50

### regex.groups
The number of capturing groups in the pattern.

### regex.groupindex
A dictionary mapping any symbolic group names defined by `(?P<id>)` to group numbers. The dictionary is empty if no symbolic groups were used in the pattern.

### regex.pattern
The pattern string from which the RE object was compiled.

## 6.2.4. Match Objects
Match objects support the following methods and attributes:

### match.expand(template)
Return the string obtained by doing backslash substitution on the template string template, as done by the `sub()` method. Escapes such as `\n` are converted to the appropriate characters, and numeric backreferences (`\1`, `\2`) and named backreferences (`\g<1>`, `\g<name>`) are replaced by the contents of the corresponding group.

使用 match 到的数据来填充模板  

    In [10]: rm  = re.match('(\d+)', '123')

    In [11]: rm.expand(r'\1hahah')
    Out[11]: '123hahah'

### match.group([group1, ...])
Returns one or more subgroups of the match. If there is a single argument, the result is a single string; if there are multiple arguments, the result is a tuple with one item per argument. Without arguments, group1 defaults to zero (the whole match is returned). If a groupN argument is zero, the corresponding return value is the entire matching string; if it is in the inclusive range [1..99], it is the string matching the corresponding parenthesized group. If a group number is negative or larger than the number of groups defined in the pattern, an `IndexError` exception is raised. If a group is contained in a part of the pattern that did not match, the corresponding result is `None`. If a group is contained in a part of the pattern that matched multiple times, the last match is returned.

    >>> m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
    >>> m.group(0)       # The entire match
    'Isaac Newton'
    >>> m.group(1)       # The first parenthesized subgroup.
    'Isaac'
    >>> m.group(2)       # The second parenthesized subgroup.
    'Newton'
    >>> m.group(1, 2)    # Multiple arguments give us a tuple.
    ('Isaac', 'Newton')

If the regular expression uses the `(?P<name>...)` syntax, the groupN arguments may also be strings identifying groups by their group name. If a string argument is not used as a group name in the pattern, an `IndexError` exception is raised.

A moderately complicated example:

    >>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
    >>> m.group('first_name')
    'Malcolm'
    >>> m.group('last_name')
    'Reynolds'

Named groups can also be referred to by their index:

    >>> m.group(1)
    'Malcolm'
    >>> m.group(2)
    'Reynolds'

If a group matches multiple times, only the last match is accessible:

    >>> m = re.match(r"(..)+", "a1b2c3")  # Matches 3 times.
    >>> m.group(1)                        # Returns only the last match.
    'c3'

### match.groups(default=None)
Return a tuple containing all the subgroups of the match, from 1 up to however many groups are in the pattern. The default argument is used for groups that did not participate in the match; it defaults to `None`.

If we make the decimal place and everything after it optional, not all groups might participate in the match. These groups will default to `None` unless the default argument is given:

    >>> m = re.match(r"(\d+)\.?(\d+)?", "24")
    >>> m.groups()      # Second group defaults to None.
    ('24', None)
    >>> m.groups('0')   # Now, the second group defaults to '0'.
    ('24', '0')

### match.groupdict(default=None)
Return a dictionary containing all the named subgroups of the match, keyed by the subgroup name. The default argument is used for groups that did not participate in the match; it defaults to None. For example:

    >>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
    >>> m.groupdict()
    {'first_name': 'Malcolm', 'last_name': 'Reynolds'}

### match.start([group])
### match.end([group])
Return the indices of the start and end of the substring matched by group; group defaults to zero (meaning the whole matched substring)

Note that `m.start(group)` will equal `m.end(group)` if group matched a null string. For example, after `m = re.search('b(c?)', 'cba')`, `m.start(0)` is 1, `m.end(0)` is 2, `m.start(1)` and `m.end(1)` are both 2, and `m.start(2)` raises an `IndexError` exception.

### match.span([group])
For a match m, return the 2-tuple `(m.start(group), m.end(group))`. Note that if group did not contribute to the match, this is `(-1, -1)`. group defaults to zero, the entire match.

### match.pos
The value of pos which was passed to the `search()` or `match()` method of a regex object. This is the index into the string at which the RE engine started looking for a match.

### match.endpos
The value of endpos which was passed to the `search()` or `match()` method of a regex object. This is the index into the string beyond which the RE engine will not go.

### match.lastindex
The integer index of the last matched capturing group, or None if no group was matched at all. For example, the expressions `(a)b`, `((a)(b))`, and `((ab))` will have `lastindex == 1` if applied to the string `'ab'`, while the expression `(a)(b)` will have `lastindex == 2`, if applied to the same string.

### match.lastgroup
The name of the last matched capturing group, or None if the group didn’t have a name, or if no group was matched at all.

### match.re
The regular expression object whose `match()` or `search()`  method produced this match instance.

### match.string
The string passed to `match()` or `search()`.

## 6.2.5. Regular Expression Examples
[正则表达式示例](https://docs.python.org/3.5/library/re.html#regular-expression-examples)
