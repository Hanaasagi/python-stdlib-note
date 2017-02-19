#[6.1. string — Common string operations](https://docs.python.org/3.5/library/string.html#module-string)

##6.1.1. String constants
`string` module 中的一些常量  
###string.ascii_letters
    In [2]: string.ascii_letters
    Out[2]: 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'

###string.ascii_lowercase
    In [3]: string.ascii_lowercase
    Out[3]: 'abcdefghijklmnopqrstuvwxyz'

###string.ascii_uppercase
    In [4]: string.ascii_uppercase
    Out[4]: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

###string.digits
    In [5]: string.digits
    Out[5]: '0123456789'

###string.hexdigits
    In [6]: string.hexdigits
    Out[6]: '0123456789abcdefABCDEF'

###string.octdigits
    In [7]: string.octdigits
    Out[7]: '01234567'


###string.punctuation
在 C 语言环境下，被认为为标点的 ASCII 字符
    
    In [8]: string.punctuation
    Out[8]: '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'

###string.printable
可打印 ASCII 字符

    In [9]: string.printable
    Out[9]: '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~ \t\n\r\x0b\x0c'
    
    In [10]: set(string.printable) == set(string.ascii_letters+string.digits+string.whitespace+string.punctuation)
    Out[10]: True

由 `ascii_letters` `digits` `whitespace` `punctuation` 四者组成  

###string.whitespace

    In [11]: string.whitespace
    Out[11]: ' \t\n\r\x0b\x0c'

##6.1.2. Custom String Formatting
The built-in string class provides the ability to do complex variable substitutions and value formatting via the `format()` method described in PEP 3101. The `Formatter` class in the `string` module allows you to create and customize your own string formatting behaviors using the same implementation as the built-in `format()` method.

使用 `string.Formatter` 可以实现自定义字符串格式化行为  

    from string import Formatter
    
    class NamespaceFormatter(Formatter):
        def __init__(self, namespace={}):
            Formatter.__init__(self)
            self.namespace = namespace
    
        def get_value(self, key, args, kwds):
            if isinstance(key, str):
                try:
                    # Check explicitly passed arguments first
                    return kwds[key]
                except KeyError:
                    return self.namespace[key]
            else:
                Formatter.get_value(key, args, kwds)
    
    fmt = NamespaceFormatter(globals())
    print(fmt.format("{greet}, world", greet="hello"))
    greet = "hello"
    print(fmt.format("{greet}, world"))

详细用法参考 [PEP 3101](https://www.python.org/dev/peps/pep-3101)

##6.1.3. Format String Syntax

`format` 的语法参考 [LINK](https://docs.python.org/3.5/library/string.html#format-string-syntax)


    replacement_field ::=  "{" [field_name] ["!" conversion] [":" format_spec] "}"
    field_name        ::=  arg_name ("." attribute_name | "[" element_index "]")*
    arg_name          ::=  [identifier | integer]
    attribute_name    ::=  identifier
    element_index     ::=  integer | index_string
    index_string      ::=  <any source character except "]"> +
    conversion        ::=  "r" | "s" | "a"
    format_spec ::=  [[fill]align][sign][#][0][width][,][.precision][type]
    fill        ::=  <any character>
    align       ::=  "<" | ">" | "=" | "^"
    sign        ::=  "+" | "-" | " "
    width       ::=  integer
    precision   ::=  integer
    type        ::=  "b" | "c" | "d" | "e" | "E" | "f" | "F" | "g" | "G" | "n" | "o" | "s" | "x" | "X" | "%"


`format`可以还实现截取字符串(`precision` 对于非数字类型会截取字符串)，进制转换(`type`)，unicode码转字符  

Examples:

分数的格式化

    >>> points = 19
    >>> total = 22
    >>> 'Correct answers: {:.2%}'.format(points/total)
    'Correct answers: 86.36%'

日期的格式化  

    >>> import datetime
    >>> d = datetime.datetime(2010, 7, 4, 12, 15, 58)
    >>> '{:%Y-%m-%d %H:%M:%S}'.format(d)
    '2010-07-04 12:15:58'

嵌套  

    >>> width = 5
    >>> for num in range(5,12): 
    ...     for base in 'dXob':
    ...         print('{0:{width}{base}}'.format(num, base=base, width=width), end=' ')
    ...     print()
    ...
        5     5     5   101
        6     6     6   110
        7     7     7   111
        8     8    10  1000
        9     9    11  1001
       10     A    12  1010
       11     B    13  1011

##6.1.4. Template strings
Templates provide simpler string substitutions as described in PEP 292. Instead of the normal `%`-based substitutions, Templates support `$`-based substitutions, using the following rules:

-  `$$` is an escape; it is replaced with a single `$`.
-  `$identifier` names a substitution placeholder matching a mapping key of `"identifier"`. By default, `"identifier"` is restricted to any case-insensitive ASCII alphanumeric string (including underscores) that starts with an underscore or ASCII letter. The first non-identifier character after the $ character terminates this placeholder specification.
-  `${identifier}` is equivalent to `$identifier`. It is required when valid identifier characters follow the placeholder but are not part of the placeholder, such as `"${noun}ification"`.

Any other appearance of `$` in the string will result in a `ValueError` being raised.

The `string` module provides a `Template` class that implements these rules. The methods of `Template` are:

###class string.Template(template)
The constructor takes a single argument which is the template string.

####substitute(mapping, **kwds)
Performs the template substitution, returning a new string. mapping is any dictionary-like object with keys that match the placeholders in the template. Alternatively, you can provide keyword arguments, where the keywords are the placeholders. When both mapping and kwds are given and there are duplicates, the placeholders from kwds take precedence.

####safe_substitute(mapping, **kwds)
Like `substitute()`, except that if placeholders are missing from mapping and kwds, instead of raising a `KeyError` exception, the original placeholder will appear in the resulting string intact. Also, unlike with `substitute()`, any other appearances of the `$` will simply return `$` instead of raising `ValueError`.

While other exceptions may still occur, this method is called “safe” because substitutions always tries to return a usable string instead of raising an exception. In another sense, `safe_substitute()` may be anything other than safe, since **it will silently ignore malformed templates containing dangling delimiters, unmatched braces, or placeholders that are not valid Python identifiers**.

Here is an example of how to use a Template:

    >>> from string import Template
    >>> s = Template('$who likes $what')
    >>> s.substitute(who='tim', what='kung pao')
    'tim likes kung pao'
    >>> d = dict(who='tim')
    >>> Template('Give $who $100').substitute(d)
    Traceback (most recent call last):
    ...
    ValueError: Invalid placeholder in string: line 1, col 11
    >>> Template('$who likes $what').substitute(d)
    Traceback (most recent call last):
    ...
    KeyError: 'what'
    >>> Template('$who likes $what').safe_substitute(d)
    'tim likes $what'


##6.1.5. Helper functions

###string.capwords(s, sep=None)
Split the argument into words using `str.split()`, capitalize each word using `str.capitalize()`, and join the capitalized words using `str.join()`. If the optional second argument sep is absent or None, runs of whitespace characters are replaced by a single space and leading and trailing whitespace are removed, otherwise sep is used to split and join the words.

    In [7]: string.capwords('hello, world', ',')
    Out[7]: 'Hello, world'
    
    In [8]: string.capwords('hello     world')
    Out[8]: 'Hello World'
