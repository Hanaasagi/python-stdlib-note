#[4. Built-in Types](https://docs.python.org/3.5/library/stdtypes.html)  

###4.1. Truth Value Testing  
Any object can be tested for truth value, for use in an **if** or **while** condition or as **operand of the Boolean operations** below. The following values are considered false:

    None

    False

    zero of any numeric type, for example, 0, 0.0, 0j.

    any empty sequence, for example, '', (), [].

    any empty mapping, for example, {}.

    instances of user-defined classes, if the class defines a __bool__() or __len__() method, when that method returns the integer zero or bool value False. [1]

All other values are considered true — so objects of many types are always true.

Operations and built-in functions that have a Boolean result always return 0 or False for false and 1 or True for true, unless otherwise stated. (Important exception: t**he Boolean operations or and and always return one of their operands**.)

	class A(object):
	    def __len__(self):
		print('len called')
		return 0

	a = A()
	print(bool(a))    # `__len__` called, False
	print(a == False) # False
	print(a == True)  # False

	# call `__len__`, because there is no `__bool__`
	if a:
	    pass

	class B(object):
	    def __len__(self):
		print('len called')
		return 0
	    def __bool__(self):
		print('bool called')
		return True

	b = B()
	print(bool(b))    # `__bool__` called, True
	print(b == False) # False
	print(b == True)  # False

	# call `__bool__`
	if b:
	    pass

    # a `__len__` called but b `__bool__` not called
    # <__main__.A object at 0xb703998c>
    print(a and b)
    # `__bool__` called
    # <__main__.A object at 0xb70f398c>
    print(b and a)
    # `__len__` called
    # <__main__.B object at 0xb70f3a6c>
    print(a or b)
    # `__bool__` called
    # <__main__.B object at 0xb70f3a6c>
    print(b or a)
    
从以上可以看出  
1) 优先调用 `__bool__`  
2) 执行 `== False` 时，不会调用，且返回值为 `False`  
3) 布尔运算 `and`, `or` 时，返回的是一侧的操作数


###4.2. Boolean Operations — and, or, not
`x or y`  相当于 `if x is false, then y, else x`  
`x and y` 相当于 `if x is false, then x, else y`  
`not`     相当于 `if x is false, then True, else False`

`and` 和 `or` 具有短路特性  

###4.3. Comparisons  

Comparisons can be **chained** arbitrarily; for example, `x < y <= z` is equivalent to `x < y and y <= z`, except that y is **evaluated only once** (but in both cases z is not evaluated at all when x < y is found to be false).


    In [79]: def y():
        ...:     print('eval y')
        ...:     return 10
        ...: 
    
    In [80]: 1 < y() < 20
    eval y
    Out[80]: True
    
    In [81]: 3 < 1 < y()
    Out[81]: False


Objects of different types, except different numeric types, never compare equal. Furthermore, some types (for example, function objects) support only a degenerate notion of comparison where any two objects of that type are unequal. The `<`, `<=`, `>` and `>=` operators will raise a TypeError exception when comparing a complex number with another built-in numeric type, when the objects are of different types that cannot be compared, or in other cases where there is no defined ordering.

Non-identical instances of a class normally compare as non-equal unless the class defines the `__eq__()` method.

Instances of a class cannot be ordered with respect to other instances of the same class, or other types of object, unless the class defines enough of the methods `__lt__()`, `__le__()`, `__gt__()`, and `__ge__()` (in general, `__lt__()` and `__eq__()` are sufficient, if you want the conventional meanings of the comparison operators).

可以简单的通过 `__lt__` 和 `__eq__` 来实现比较运算  

    In [86]: class Num(object):
        ...:     def __init__(self, num):
        ...:         self.num = num
        ...:     def __lt__(self, other):
        ...:         return self.num < other.num
        ...:     def __eq__(self, other):
        ...:         return self.num == other.num
        ...:     
    
    In [87]: a = Num(10)
    
    In [88]: b = Num(2)
    
    In [89]: a > b
    Out[89]: True
    
    In [90]: a < b
    Out[90]: False
    
    In [91]: c = Num(2)
    
    In [92]: a == c
    Out[92]: False
    
    In [93]: b == c
    Out[93]: True

The behavior of the is and is not operators cannot be customized; also they can be applied to any two objects and never raise an exception.

Two more operations with the same syntactic priority, in and not in, are supported only by sequence types (below).


###4.4. Numeric Types — int, float, complex

There are three distinct numeric types: integers, floating point numbers, and complex numbers. In addition, **Booleans are a subtype of integers**. Integers have unlimited precision. Floating point numbers are usually implemented using double in C; information about the precision and internal representation of floating point numbers for the machine on which your program is running is available in **`sys.float_info`**. 

**Python fully supports mixed arithmetic**: when a binary arithmetic operator has operands of different numeric types, the operand with the “narrower” type is widened to that of the other, where integer is narrower than floating point, which is narrower than complex. Comparisons between numbers of mixed type use the same rule. [2] The constructors int(), float(), and complex() can be used to produce numbers of a specific type.
    
    In [105]: 1 == 1.0
    Out[105]: True
    
    In [106]: [1, 2] == [1.0, 2.0]
    Out[106]: True

三个常用的函数  

        math.trunc(x) |	x truncated to Integral
        math.floor(x) |	the greatest Integral <= x
        math.ceil(x)  |	the least Integral >= x

`math.trunc()`  删除掉数字的小数部分和小数点，在参数是正数的情况下，`trunc()` 的执行效果等同于 `floor()`。而在参数是负数的情况下，`trunc()` 的执行效果等同于 `ceil()`

###4.4.1. Bitwise Operations on Integer Types
   
   
###4.4.2. Additional Methods on Integer Types
        
The int type implements the `numbers.Integral` abstract base class. In addition, it provides a few more methods:

####`int.bit_length()`
Return the number of bits necessary to represent an integer in binary, excluding the sign and leading zeros:
More precisely, if x is nonzero, then `x.bit_length()` is the unique positive integer k such that `2**(k-1) <= abs(x) < 2**k`. Equivalently, when `abs(x)` is small enough to have a correctly rounded logarithm, then `k = 1 + int(log(abs(x), 2))`. If x is zero, then `x.bit_length()` returns 0.

####`int.to_bytes(length, byteorder, *, signed=False)`
Return an array of bytes representing an integer.

The integer is represented using length bytes. An OverflowError is raised if the integer is not representable with the given number of bytes.

The byteorder argument determines the byte order used to represent the integer. **If byteorder is "big", the most significant byte(最高有效位) is at the beginning of the byte array. If byteorder is "little", the most significant byte is at the end of the byte array.** To request the native byte order of the host system, use `sys.byteorder` as the byte order value.

一个多位的整数将按照其存储地址的最低或最高字节排列。如果最低有效位在最高有效位的前面，则称小端序；反之则称大端序

例如假设上述变量x类型为int，位于地址0x100处，它的十六进制为0x01234567，地址范围为0x100~0x103字节，其内部排列顺序依赖于机器的类型。大端序从首位开始将是：0x100: 01, 0x101: 23,..。而小端序将是：0x100: 67, 0x101: 45,..

The signed argument determines whether two’s complement is used to represent the integer. If signed is False and a negative integer is given, an OverflowError is raised. The default value for signed is False.

####classmethod int.from_bytes(bytes, byteorder, *, signed=False)

Return the integer represented by the given array of bytes.

    In [22]: a = 1024
    
    In [23]: a.to_bytes(10, 'big')
    Out[23]: b'\x00\x00\x00\x00\x00\x00\x00\x00\x04\x00'
    
    In [24]: int.from_bytes(a.to_bytes(10, 'big'), 'big')
    Out[24]: 1024

参数 `bytes` 也可以使用 [bytes-like object](https://docs.python.org/3.5/glossary.html#term-bytes-like-object)，例如  

    In [53]: int.from_bytes([192, 168, 1, 1], 'big')
    Out[53]: 3232235777
    
    In [54]: s = bin(3232235777).lstrip('0b')
    
    In [55]: s
    Out[55]: '11000000101010000000000100000001'
    
    In [56]: l = re.findall(r'.{8}', s)
    
    In [57]: l
    Out[57]: ['11000000', '10101000', '00000001', '00000001']
    
    In [58]: from functools import partial
    
    In [59]: int2 = partial(int, base=2)
    
    In [60]: list(map(int2, l))
    Out[60]: [192, 168, 1, 1]

###4.4.3. Additional Methods on Float  

The float type implements the `numbers.Real` abstract base class. float also has the following additional methods.

####float.as_integer_ratio()
Return a pair of integers whose ratio is exactly equal to the original float and with a positive denominator. Raises OverflowError on infinities and a ValueError on NaNs.

    In [68]: a, b = 3.14.as_integer_ratio()
    
    In [69]: a / b
    Out[69]: 3.14

**4.4.4. Hashing of numeric types 示例代码中有使用**

####float.is_integer()
判断浮点数是否为整数  

####float.hex()
####classmethod float.fromhex(s)  
Since Python’s floats are stored internally as binary numbers, **converting a float to or from a decimal string usually involves a small rounding error. In contrast, hexadecimal strings allow exact representation and specification of floating-point numbers**. This can be useful when debugging, and in numerical work.
A hexadecimal string takes the form:  

    [sign] ['0x'] integer ['.' fraction] ['p' exponent]

where the optional `sign` may by either `+` or `-`, `integer` and `fraction` are strings of hexadecimal digits, and `exponent` is a decimal integer with an optional leading sign. Case is not significant, and there must be at least one hexadecimal digit in either the integer or the fraction. This syntax is similar to the syntax specified in section 6.4.4.2 of the C99 standard, and also to the syntax used in Java 1.5 onwards. In particular, the output of `float.hex()` is usable as a hexadecimal floating-point literal in C or Java code, and hexadecimal strings produced by C’s %a format character or `Java’s Double.toHexString` are accepted by `float.fromhex()`.

Note that the exponent is written in decimal rather than hexadecimal, and that it gives the power of 2 by which to multiply the coefficient. For example, the hexadecimal string `0x3.a7p10` represents the floating-point number `(3 + 10./16 + 7./16**2) * 2.0**10`, or `3740.0`:

    >>> float.fromhex('0x3.a7p10')
    3740.0

Applying the reverse conversion to 3740.0 gives a different hexadecimal string representing the same number:


    >>> float.hex(3740.0)
    '0x1.d380000000000p+11'

###4.4.4. Hashing of numeric types  

For numbers `x` and `y`, possibly of different types, it’s a requirement that `hash(x) == hash(y)` whenever `x == y`. Python’s hash for numeric types is based on a single mathematical function that’s defined for any rational number, and hence applies to all instances of int and fractions.Fraction, and all finite instances of float and decimal.Decimal. Essentially, this function is given by reduction modulo P for a fixed prime P. The value of P is made available to Python as the modulus attribute of sys.hash_info.

CPython implementation detail: Currently, the prime used is `P = 2**31 - 1` on machines with 32-bit C longs and `P = 2**61 - 1` on machines with 64-bit C longs.

Here are the rules in detail:

- If `x = m / n` is a nonnegative rational number and `n` is not divisible by `P`, define `hash(x)` as `m * invmod(n, P) % P`, where `invmod(n, P)` gives the inverse of n modulo `P`.
- If `x = m / n` is a nonnegative rational number and `n` is divisible by `P` (but `m` is not) then `n` has no inverse modulo `P` and the rule above doesn’t apply; in this case define `hash(x)` to be the constant value `sys.hash_info.inf`.
- If `x = m / n` is a negative rational number define `hash(x)` as `-hash(-x)`. If the resulting hash is `-1`, replace it with `-2`.
- The particular values sys.hash_info.inf, -sys.hash_info.inf and sys.hash_info.nan are used as hash values for positive infinity, negative infinity, or nans (respectively). (All hashable nans have the same hash value.)
- For a complex number z, the hash values of the real and imaginary parts are combined by computing hash(z.real) + sys.hash_info.imag * hash(z.imag), reduced modulo 2**sys.hash_info.width so that it lies in range(-2**(sys.hash_info.width - 1), 2**(sys.hash_info.width - 1)). Again, if the result is -1, it’s replaced with -2.

To clarify the above rules, here’s some example Python code, equivalent to the built-in hash, for computing the hash of a rational number, `float`, or `complex`:

    import sys, math
    
    def hash_fraction(m, n):
    """Compute the hash of a rational number m / n.

    Assumes m and n are integers, with n positive.
    Equivalent to hash(fractions.Fraction(m, n)).

    """
    P = sys.hash_info.modulus
    # Remove common factors of P.  (Unnecessary if m and n already coprime.)
    while m % P == n % P == 0:
        m, n = m // P, n // P

    if n % P == 0:
        hash_value = sys.hash_info.inf
    else:
        # Fermat's Little Theorem: pow(n, P-1, P) is 1, so
        # pow(n, P-2, P) gives the inverse of n modulo P.
        hash_value = (abs(m) % P) * pow(n, P - 2, P) % P
    if m < 0:
        hash_value = -hash_value
    if hash_value == -1:
        hash_value = -2
    return hash_value

    def hash_float(x):
        """Compute the hash of a float x."""

    if math.isnan(x):
        return sys.hash_info.nan
    elif math.isinf(x):
        return sys.hash_info.inf if x > 0 else -sys.hash_info.inf
    else:
        return hash_fraction(*x.as_integer_ratio())

    def hash_complex(z):
        """Compute the hash of a complex number z."""

    hash_value = hash_float(z.real) + sys.hash_info.imag * hash_float(z.imag)
    # do a signed reduction modulo 2**sys.hash_info.width
    M = 2**(sys.hash_info.width - 1)
    hash_value = (hash_value & (M - 1)) - (hash_value & M)
    if hash_value == -1:
        hash_value = -2
    return hash_value
    
###4.5. Iterator Types
Python supports a concept of iteration over containers. This is implemented using two distinct methods; these are used to allow user-defined classes to support iteration. Sequences, described below in more detail, always support the iteration methods.

**One method needs to be defined for container objects to provide iteration support**:

####`container.__iter__()`
**Return an iterator object**. The object is required to support the iterator protocol described below. If a container supports different types of iteration, additional methods can be provided to specifically request iterators for those iteration types. (An example of an object supporting multiple forms of iteration would be a tree structure which supports both breadth-first and depth-first traversal.) This method corresponds to the `tp_iter` slot of the type structure for Python objects in the Python/C API.

**The iterator objects themselves are required to support the following two methods, which together form the iterator protocol**:

####`iterator.__iter__()`
**Return the iterator object itself**. **This is required to allow both containers and iterators to be used with the `for` and `in` statements**. This method corresponds to the `tp_iter` slot of the type structure for Python objects in the Python/C API.

####`iterator.__next__()`
Return the next item from the container. If there are no further items, raise the `StopIteration` exception. This method corresponds to the `tp_iternext` slot of the type structure for Python objects in the Python/C API.

Python defines several iterator objects to support iteration over general and specific sequence types, dictionaries, and other more specialized forms. **The specific types are not important beyond their implementation of the iterator protocol**.

Once an iterator’s `__next__()` method raises `StopIteration`, it must continue to do so on subsequent calls. Implementations that do not obey this property are deemed broken.

简而言之，对于容器对象（container）来说，要支持迭代的话，需要在容器内部实现 `__iter__`， 并返回一个迭代器对象（iterator）
对于迭代器来说，它也需要一个 `__iter__` 方法，用于返回这个迭代器自身。同时需要一个 `__next__` 方法，来返回下一个元素。这两个方法，合在一起，称为迭代器协议。

    In [71]: class Iterator(object):
        ...:     def __init__(self, limit):
        ...:         self.limit = limit
        ...:         self.num = 0
        ...:     def __next__(self):
        ...:         num = self.num
        ...:         self.num += 1
        ...:         if self.num >= self.limit:
        ...:             raise StopIteration()
        ...:         return num
        ...:         
        ...:     
    
    In [72]: iterator = Iterator(10)
    
    In [73]: for i in iterator:
        ...:     print(i)
        ...:     
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-73-19e5eb572c56> in <module>()
    ----> 1 for i in iterator:
          2     print(i)
          3 
    
    TypeError: 'Iterator' object is not iterable
    
    In [74]: next(iterator)
    Out[74]: 0
    
    In [75]: next(iterator)
    Out[75]: 1

    In [76]: class Iterator(object):
        ...:     def __init__(self, limit):
        ...:         self.limit = limit
        ...:         self.num = 0
        ...:     def __iter__(self):
        ...:         return self
        ...:     def __next__(self):
        ...:         num = self.num
        ...:         self.num += 1  
        ...:         if self.num > self.limit:
        ...:             raise StopIteration()
        ...:         return num
        ...:         
        ...:     
    
    In [77]: iterator = Iterator(10)
    
    In [78]: for i in iterator:
        ...:     print(i)
        ...:     
    0
    1
    ...

可以看出能够应用 `for` `in` 语句的前提是：对象具有 `__iter__` 方法， 且返回一个可以应用于 `next()`  的对象（实现`__next__`，或者是一个生成器），如果是一个对象同时实现了上面的两个方法则称这个对象为迭代器
差不多可以用如下  

    iterator = iter(object)
    while True:
        try:
            res = next(iterator)
            # 调用 for in 中的语句
            call_block(res)
        except StopIteration:
            break
            
另外上面实现的那个迭代器是无法多次进行迭代的，因为没有重置状态  

###4.5.1. Generator Types  
Python’s generators provide a convenient way to implement the iterator protocol. If a container object’s `__iter__()` method is implemented as a generator, it will automatically return an iterator object (technically, a generator object) supplying the `__iter__()` and `__next__()` methods. More information about generators can be found in the documentation for the yield expression.

    In [5]: class A(object):
       ...:     def __iter__(self):
       ...:         def func():
       ...:             for i in range(10):
       ...:                 yield i
       ...:         return func()
       ...:     
    
    In [6]: a = A()
    
    In [7]: for i in a:
       ...:     print(i)
       ...:     
    0
    1
    ...
    
###4.6. Sequence Types — list, tuple, range
There are three basic sequence types: lists, tuples, and range objects. Additional sequence types tailored for processing of binary data and text strings are described in dedicated sections.

###4.6.1. Common Sequence Operations
####s.index(x[, i[, j]])
index of the first occurrence of `x` in `s` (at or after index `i` and before index `j`)

`index` raises `ValueError` when x is not found in s. When supported, the additional arguments to the index method allow efficient searching of subsections of the sequence. Passing the extra arguments is roughly equivalent to using s[i:j].index(x), only without copying any data and with the returned index being relative to the start of the sequence rather than the start of the slice.

####s.count(x)  
total number of occurrences of x in s
####Notes
1) Values of n less than `0` are treated as `0` (which yields an empty sequence of the same type as s). Note that items in the sequence s are not copied; they are referenced multiple times. This often haunts new Python programmers; consider:

    >>> lists = [[]] * 3
    >>> lists
    [[], [], []]
    >>> lists[0].append(3)
    >>> lists
    [[3], [3], [3]]

What has happened is that `[[]]` is a one-element list containing an empty list, so all three elements of `[[]] * 3` are references to this single empty list. Modifying any of the elements of `lists` modifies this single list. You can create a list of different lists this way:

    >>> lists = [[] for i in range(3)]
    >>> lists[0].append(3)
    >>> lists[1].append(5)
    >>> lists[2].append(7)
    >>> lists
    [[3], [5], [7]]



2) **Concatenating immutable sequences always results in a new object**. This means that building up a sequence by repeated concatenation will have a quadratic runtime cost in the total sequence length. To get a linear runtime cost, you must switch to one of the alternatives below:

- if concatenating `str` objects, you can build a list and use `str.join()` at the end or else write to an `io.StringIO instance and retrieve its value when complete
- if concatenating `bytes` objects, you can similarly use `bytes.join()` or `io.BytesIO`, or you can do in-place concatenation with a `bytearray` object. `bytearray` objects are mutable and have an efficient overallocation mechanism
- if concatenating `tuple` objects, extend a `list` instead
- for other types, investigate the relevant class documentation

###4.6.2. Immutable Sequence Types
The only operation that immutable sequence types generally implement that is not also implemented by mutable sequence types is support for the `hash()` built-in.
This support allows immutable sequences, such as `tuple` instances, to be used as `dict` keys and stored in `set` and `frozenset` instances.
Attempting to hash an immutable sequence that contains unhashable values will result in `TypeError`.  

###4.6.3. Mutable Sequence Types
The operations in the following table are defined on mutable sequence types. The `collections.abc.MutableSequence` ABC is provided to make it easier to correctly implement these operations on custom sequence types.
In the table s is an instance of a mutable sequence type, t is any **iterable object** and x is an arbitrary object that meets any type and value restrictions imposed by s (for example, `bytearray` only accepts integers that meet the value restriction `0 <= x <= 255`).

注意是可迭代对象即可  

    In [27]: def b():
        ...:     for i in range(5):
        ...:         yield i
        ...:         
    
    In [28]: a = []
    
    In [29]: a.extend(b())
    
    In [30]: a
    Out[30]: [0, 1, 2, 3, 4]


####s[i:j:k] = t
the elements of s[i:j:k] are replaced by those of t  

    In [1]: a = [1,2,3,4,5,6,7,8]
    
    In [2]: a[0:8:2] = [2,4,6,8]
    
    In [3]: a
    Out[3]: [2, 2, 4, 4, 6, 6, 8, 8]

####s.append(x)
appends x to the end of the sequence (**same as `s[len(s):len(s)] = [x]`**)

类似的，`s.insert(i, x)` 和 `s[i:i] = x` 等价

####s.extend(t)
extends s with the contents of t (**for the most part** the same as `s[len(s):len(s)] = t`)
大部分情况满足

####s *= n
updates s with its contents repeated n times. The value n is an integer, or an object implementing `__index__()`. Zero and negative values of n clear the sequence.  

    In [31]: class A(object):
        ...:     def __index__(self):
        ...:         return 2
        ...:     
    
    In [32]: b = [1,2] * A()
    
    In [33]: b
    Out[33]: [1, 2, 1, 2]


###4.6.4. Lists
Lists are mutable sequences, typically used to store collections of homogeneous items (where the precise degree of similarity will vary by application).Lists implement all of the common and mutable sequence operations. Lists also provide the following additional method:
####sort(*, key=None, reverse=None)
This method sorts the list in place, using only `<` comparisons between items. Exceptions are not suppressed - if any comparison operations fail, the entire sort operation will fail (and **the list will likely be left in a partially modified state**).
key specifies a function of one argument that is used to extract a comparison key from each list element (for example, key=`str.lower`). The key corresponding to each item in the list is calculated once and then used for the entire sorting process. The default value of `None` means that list items are sorted directly without calculating a separate key value.

而在 Python2.x 的 `sort` 是接受是一个 `cmp` 参数


    >>> l = ['a', 'B', 'c', 'D']
    >>> l.sort()
    >>> l
    ['B', 'D', 'a', 'c']
    >>> l.sort(cmp=lambda a, b: cmp(a.lower(), b.lower()))
    >>> l
    ['a', 'B', 'c', 'D']

**The `functools.cmp_to_key()` utility is available to convert a 2.x style cmp function to a key function.**

To remind users that it operates by side effect, it does not return the sorted sequence (use `sorted()` to explicitly request a new sorted list instance).

`list.sort()` 修改原列表
`sorted()`    返回新的列表  

**The `sort()` method is guaranteed to be stable. A sort is stable if it guarantees not to change the relative order of elements that compare equal** — this is helpful for sorting in multiple passes (for example, sort by department, then by salary grade).

###4.6.5. Tuples
Tuples are immutable sequences, typically used to store collections of heterogeneous data (such as the 2-tuples produced by the `enumerate()` built-in). Tuples are also used for cases where an immutable sequence of homogeneous data is needed (such as allowing storage in a `set` or `dict` instance).  

对于创建一个元组不仅有 `(a,)` 的形式，还有 `a, `  

    In [16]: a = 1,
    
    In [17]: a.__class__
    Out[17]: tuple

或者使用 `tuple()` 将可迭代对象转换为元组  

**Note that it is actually the comma which makes a tuple, not the parentheses**. **The parentheses are optional, except in the empty tuple case, or when they are needed to avoid syntactic ambiguity**. For example, f(a, b, c) is a function call with three arguments, while f((a, b, c)) is a function call with a 3-tuple as the sole argument.

`,` 创建元组，而不是 `()`。`()` 是可选的，除非空元组或者需要避免二义性  

For heterogeneous collections of data where access by name is clearer than access by index, `collections.namedtuple()` may be a more appropriate choice than a simple tuple object.

###4.6.6. Ranges
The `range` type represents an immutable sequence of numbers and is commonly used for looping a specific number of times in `for` loops.  

####class range(stop)
####class range(start, stop[, step])

The arguments to the range constructor must be integers (either built-in `int` or any object that implements the `__index__` special method). If the step argument is omitted, it defaults to `1`. If the start argument is omitted, it defaults to `0`. If step is zero, `ValueError` is raised.

**Ranges implement all of the common sequence operations except concatenation and repetition** (due to the fact that range objects can only represent sequences that follow a strict pattern and repetition and concatenation will usually violate that pattern).

    >>> r = range(0, 20, 2)
    >>> r
    range(0, 20, 2)
    >>> 11 in r
    False
    >>> 10 in r
    True
    >>> r.index(10)
    5
    >>> r[5]
    10
    >>> r[:5]
    range(0, 10, 2)
    >>> r[-1]
    18


因为 range 的元素不会产生重复，所以 `range().index()` 只有一个参数  

**The advantage of the `range` type over a regular `list` or `tuple` is that a `range` object will always take the same (small) amount of memory,** no matter the size of the range it represents (as it only stores the `start`, `stop` and `step` values, calculating individual items and subranges as needed).

Testing range objects for equality with `==` and `!=` compares them as sequences. That is, two range objects are considered equal if they represent the same sequence of values. (**Note that two range objects that compare equal might have different `start`, `stop` and `step` attributes, for example `range(0) == range(2, 1, 3)` or `range(0, 3, 2) == range(0, 4, 2)`.**)

###4.7. Text Sequence Type — str
Textual data in Python is handled with `str` objects, or strings. Strings are immutable `sequences` of Unicode code points.

**String literals that are part of a single expression and have only whitespace between them will be implicitly converted to a single string literal**. That is, `("spam " "eggs")` == `"spam eggs"`.

所以字符串续行可以有以下两种形式  

    In [7]: s = ('hello '
       ...: 'world')
    
    In [8]: s = 'hello \
       ...: world'

There is also no mutable string type, but `str.join()` or `io.StringIO` can be used to efficiently construct strings from multiple fragments.

Changed in version 3.3: For backwards compatibility with the Python 2 series, the `u` prefix is once again permitted on string literals. It has no effect on the meaning of string literals and cannot be combined with the `r` prefix.

####class str(object='')
####class str(object=b'', encoding='utf-8', errors='strict')
Return a `string` version of object.
If neither encoding nor errors is given, `str(object)` returns `object.__str__()`, which is the “informal” or nicely printable string representation of object. For string objects, this is the string itself. **If object does not have a `__str__()` method, then `str()` falls back to returning `repr(object)`**.

If at least one of encoding or errors is given, object should be a `bytes-like object` (e.g. `bytes` or `bytearray`). In this case, if object is a bytes (or bytearray) object, then str(bytes, encoding, errors) is equivalent to bytes.decode(encoding, errors). 

###4.7.1. String Methods
####str.center(width[, fillchar])
Return centered in a string of length width. Padding is done using the specified fillchar (default is an ASCII space). The original string is returned if width is less than or equal to len(s).

这个 `width` 不是想在两侧添加的字符长度，而是返回的总长度  
`str.ljust(width[, fillchar])`, `str.rjust(width[, fillchar])` 也是这样

####str.count(sub[, start[, end]])
计算区间中指定字符出现次数  

####str.endswith(suffix[, start[, end]])
Return `True` if the string ends with the specified suffix, otherwise return `False`. suffix can also be a tuple of suffixes to look for. With optional start, test beginning at that position. With optional end, stop comparing at that position.

`str.endswith` 和 `str.startswith` 都可以指定区间，比如我们可以匹配时忽略标点  

    In [34]: s = 'balabala, desu!'
    
    In [35]: s.endswith('desu', 0, -1)
    Out[35]: True

####str.expandtabs(tabsize=8)
Return a copy of the string where all tab characters are replaced by one or more spaces, depending on the current column and the given tab size. Tab positions occur every tabsize characters (default is 8, giving tab positions at columns 0, 8, 16 and so on). To expand the string, the current column is set to zero and the string is examined character by character. If the character is a tab (`\t`), **one or more space characters are inserted** in the result until the current column is equal to the next tab position. (The tab character itself is not copied.) If the character is a newline (`\n`) or return (`\r`), it is copied and the current column is reset to zero. Any other character is copied unchanged and the current column is incremented by one regardless of how the character is represented when printed.

####str.find(sub[, start[, end]])
Return the lowest index in the string where substring sub is found within the slice `s[start:end]`. Optional arguments start and end are interpreted as in slice notation. Return `-1` if sub is not found.

Note:The `find()` method should be used only if you need to know the position of sub. To check if sub is a substring or not, use the `in` operator:

对应存在 `str.rfind`  


####str.index(sub[, start[, end]])
Like `find()`, but raise `ValueError` when the substring is not found.

对应存在 `str.rindex`

####str.format_map(mapping)
Similar to `str.format(**mapping)`, except that `mapping` is used directly and not copied to a dict. This is useful if for example `mapping` is a dict subclass:


    >>> class Default(dict):
    ...     def __missing__(self, key):
    ...         return key
    ...
    >>> '{name} was born in {country}'.format_map(Default(name='Guido'))
    'Guido was born in country'

####str.isdecimal() 
####str.isdigit()
####str.isnumeric()
`str.isdecimal()` (Only Decimal Numbers)
`str.isdigit()`   (Decimals, Subscripts, Superscripts)
`str.isnumeric()` (Digits, Vulgar Fractions, Subscripts, Superscripts, Roman Numerals, Currency Numerators)

    In [66]: '²'.isdecimal()
    Out[66]: False
    
    In [67]: '²'.isdigit()
    Out[67]: True
    
    In [68]: '²'.isnumeric()
    Out[68]: True
    
    In [69]: 'Ⅵ'.isdecimal()
    Out[69]: False
    
    In [70]: 'Ⅵ'.isdigit()
    Out[70]: False
    
    In [71]: 'Ⅵ'.isnumeric()
    Out[71]: True

####str.isidentifier()
Return true if the string is a valid identifier according to the language definition, section `Identifiers and keywords`.
Use `keyword.iskeyword()` to test for reserved identifiers such as `def` and `class`.


####str.isprintable()
Return true if all characters in the string are printable or the string is empty, false otherwise. Nonprintable characters are those characters defined in the Unicode character database as “Other” or “Separator”, excepting the ASCII space (0x20) which is considered printable. (Note that printable characters in this context are those which should not be escaped when `repr()` is invoked on a string. It has no bearing on the handling of strings written to `sys.stdout` or `sys.stderr`.)


####str.lstrip([chars])
Return a copy of the string with leading characters removed. The chars argument is a string **specifying the set of characters to be removed**. If omitted or `None`, the chars argument defaults to removing whitespace. **The chars argument is not a prefix; rather, all combinations of its values are stripped**:


    >>> 'www.example.com'.lstrip('cmowz.')
    'example.com'

`rstrip`, `strip` 也是这样移除的是制定字符的排列组合  


####static str.maketrans(x[, y[, z]])
This static method returns a translation table usable for `str.translate()`.
If there is only one argument, it must be a dictionary mapping Unicode ordinals (integers) or characters (strings of length 1) to Unicode ordinals, strings (of arbitrary lengths) or `None`. Character keys will then be converted to ordinals.

If there are two arguments, they must be strings of equal length, and in the resulting dictionary, each character in x will be mapped to the character at the same position in y. If there is a third argument, it must be a string, whose characters will be mapped to `None` in the result.

    In [104]: str.maketrans('abc', 'ABC','a')
    Out[104]: {97: None, 98: 66, 99: 67}

####str.partition(sep)
Split the string at the **first** occurrence of sep, and return a 3-tuple containing the part before the separator, the separator itself, and the part after the separator. If the separator is not found, return a 3-tuple containing the string itself, followed by two empty strings.

按指定字符将字符串分割为三部分  

对应存在 `str.rpartition(sep)`

####str.replace(old, new[, count])
Return a copy of the string with all occurrences of substring old replaced by new. If the optional argument count is given, only the first count occurrences are replaced.

可以指定替换的次数

####str.split(sep=None, maxsplit=-1)
Return a list of the words in the string, using sep as the delimiter string. **If maxsplit is given, at most maxsplit splits are done (thus, the list will have at most `maxsplit+1` elements)**. If maxsplit is not specified or -1, then there is no limit on the number of splits (all possible splits are made).If sep is given, consecutive delimiters are not grouped together and are deemed to delimit empty strings (for example, `'1,,2'.split(',')` returns `['1', '', '2'])`.
If sep is not specified or is `None`, a **different splitting algorithm** is applied: runs of consecutive whitespace are regarded as a single separator, and the result will contain no empty strings at the start or end if the string has leading or trailing whitespace(for example, `'  a b   c'.split()` returns `['a', 'b', 'c']`). 

对应存在 `str.rsplit(sep=None, maxsplit=-1)`


