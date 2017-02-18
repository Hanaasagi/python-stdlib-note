#[4. Built-in Types](https://docs.python.org/3.5/library/stdtypes.html)  

##4.1. Truth Value Testing  
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


##4.2. Boolean Operations — and, or, not
`x or y`  相当于 `if x is false, then y, else x`  
`x and y` 相当于 `if x is false, then x, else y`  
`not`     相当于 `if x is false, then True, else False`

`and` 和 `or` 具有短路特性  

##4.3. Comparisons  

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

可以简单的通过 `__lt__` 和 `__eq__` 来实现比较运算（`a <= b` 替代为 `a < b or a ==b`，如果想直接实现则需要 `__le__`）  

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


##4.4. Numeric Types — int, float, complex

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
    
##4.5. Iterator Types
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
    
##4.6. Sequence Types — list, tuple, range
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

##4.7. Text Sequence Type — str
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
Return a copy of the string where all tab characters are replaced by one or more spaces, depending on the current column and the given tab size. Tab positions occur every tabsize characters (default is 8, giving tab positions at columns 0, 8, 16 and so on). **To expand the string, the current column is set to zero and the string is examined character by character. If the character is a tab (`\t`), one or more space characters are inserted in the result until the current column is equal to the next tab position. (The tab character itself is not copied.) If the character is a newline (`\n`) or return (`\r`), it is copied and the current column is reset to zero.** Any other character is copied unchanged and the current column is incremented by one regardless of how the character is represented when printed.

    In [2]: '0\t01\t012\t'.expandtabs(4)
    Out[2]: '0   01  012 '

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

参数中指定的字符串是作为集合存在的。这个方法从左开始剔除集合中出现的字符，直至遇到没有出现的。

`rstrip`, `strip` 也是这样的


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

####str.splitlines([keepends])
Return a list of the lines in the string, breaking at line boundaries. Line breaks are not included in the resulting list unless keepends is given and true.


Representation 	| Description
----------------|------------
\n 	            | Line Feed
\r 	            | Carriage Return
\r\n            | Carriage Return + Line Feed
\v or \x0b      | Line Tabulation
\f or \x0c      | Form Feed
\x1c 	        | File Separator
\x1d 	        | Group Separator
\x1e 	        | Record Separator
\x85 	        | Next Line (C1 Control Code)
\u2028 	        | Line Separator
\u2029 	        | Paragraph Separator

For example:  

    >>> 'ab c\n\nde fg\rkl\r\n'.splitlines()
    ['ab c', '', 'de fg', 'kl']
    >>> 'ab c\n\nde fg\rkl\r\n'.splitlines(keepends=True)
    ['ab c\n', '\n', 'de fg\r', 'kl\r\n']

**Unlike `split()` when a delimiter string sep is given, this method returns an empty list for the empty string, and a terminal line break does not result in an extra line**:

    >>> "".splitlines()
    []
    >>> "One line\n".splitlines()
    ['One line']

For comparison, split('\n') gives:

    >>> ''.split('\n')
    ['']
    >>> 'Two lines\n'.split('\n')
    ['Two lines', '']


####str.swapcase()
Return a copy of the string with uppercase characters converted to lowercase and vice versa. **Note that it is not necessarily true that     `s.swapcase().swapcase() == s`**.

    In [8]: s = 'ß'
    
    In [9]: s.swapcase()
    Out[9]: 'SS'
    
    In [10]: s.swapcase().swapcase()
    Out[10]: 'ss'

the German lowercase letter `'ß'` is equivalent to `"ss"`

和这个有点关系 [`str.casefold()`](https://docs.python.org/3.5/library/stdtypes.html#str.casefold)  

####str.title()
Return a titlecased version of the string where words start with an uppercase character and the remaining characters are lowercase.

For example:

    >>> 'Hello world'.title()
    'Hello World'



The algorithm uses a simple language-independent definition of a word as groups of consecutive letters. The definition works in many contexts but it means that apostrophes in contractions and possessives form word boundaries, which may not be the desired result:

    >>> "they're bill's friends from the UK".title()
    "They'Re Bill'S Friends From The Uk"

A workaround for apostrophes(`'`) can be constructed using regular expressions:

    >>> import re
    >>> def titlecase(s):
    ...     return re.sub(r"[A-Za-z]+('[A-Za-z]+)?",
    ...                   lambda mo: mo.group(0)[0].upper() +
    ...                              mo.group(0)[1:].lower(),
    ...                   s)
    ...
    >>> titlecase("they're bill's friends.")
    "They're Bill's Friends."

####str.translate(table)
Return a copy of the string in which each character has been mapped through the given translation table. The table must be an object that implements indexing via `__getitem__()`, typically a `mapping` or `sequence`. When indexed by a Unicode ordinal (an integer), the table object can do any of the following: return a Unicode ordinal or a string, to map the character to one or more other characters; return `None`, to delete the character from the return string; or raise a `LookupError` exception, to map the character to itself.

You can use `str.maketrans()` to create a translation map from character-to-character mappings in different formats.

    In [22]: m = str.maketrans('123456789', 'ⅠⅡⅢⅣⅤⅥⅦⅧⅨ')
    
    In [23]: '1 2 3 4 5'.translate(m)
    Out[23]: 'Ⅰ Ⅱ Ⅲ Ⅳ Ⅴ'

####str.upper()
**Note that `str.upper().isupper()` might be `False` if s contains uncased characters or if the Unicode category of the resulting character(s) is not “Lu” (Letter, uppercase), but e.g. “Lt” (Letter, titlecase).**


    >>> '1'.upper().isupper()
    False
    
####str.zfill(width)
Return a copy of the string left filled with ASCII `'0'` digits to make a string of length width. A leading sign prefix (`'+'`/`'-'`) is handled by inserting the padding after the sign character rather than before. The original string is returned if width is less than or equal to `len(s)`.

    >>> "42".zfill(5)
    '00042'
    >>> "-42".zfill(5)
    '-0042'
    
###4.7.2. printf-style String Formatting
String objects have one unique built-in operation: the `%` operator (modulo). This is also known as the string formatting or interpolation operator. Given `format % values` (where format is a string), `%` conversion specifications in format are replaced with zero or more elements of values. The effect is similar to using the `sprintf()` in the C language.

Note:The formatting operations described here exhibit a variety of quirks that lead to a number of common errors (such as failing to display tuples and dictionaries correctly). **Using the newer str.format() interface helps avoid these errors, and also provides a generally more powerful, flexible and extensible approach to formatting text.**

具体使用见 [printf-style String Formatting](https://docs.python.org/3.5/library/stdtypes.html#printf-style-string-formatting)

##4.8. Binary Sequence Types — bytes, bytearray, memoryview
The core built-in types for manipulating binary data are `bytes` and `bytearray`. They are supported by `memoryview` which uses the `buffer protocol` to access the memory of other binary objects without needing to make a copy.

###4.8.1. Bytes
Bytes objects are immutable sequences of single bytes. Since many major binary protocols are based on the ASCII text encoding, bytes objects offer several methods that are only valid when working with ASCII compatible data and are closely related to string objects in a variety of other ways.
Only ASCII characters are permitted in bytes literals (regardless of the declared source code encoding). Any binary values over 127 must be entered into bytes literals using the appropriate escape sequence.  
As with string literals, bytes literals may also use a `r` prefix to disable processing of escape sequences. 

####classmethod bytes.fromhex(string)
This `bytes` class method returns a bytes object, decoding the given string object. The string must contain two hexadecimal digits per byte, with ASCII spaces being ignored.

####bytes.hex()
A reverse conversion function exists to transform a bytes object into its hexadecimal representation.


`bytes` 和 `list` 可以相互转换


    In [9]: bytes([46, 46, 46])
    Out[9]: b'...'
    
    In [10]: list(b'...')
    Out[10]: [46, 46, 46]



**Note:For Python 2.x users: In the Python 2.x series, a variety of implicit conversions between 8-bit strings (the closest thing 2.x offers to a built-in binary data type) and Unicode strings were permitted. This was a backwards compatibility workaround to account for the fact that Python originally only supported 8-bit text, and Unicode text was a later addition. In Python 3.x, those implicit conversions are gone - conversions between 8-bit binary data and Unicode text must be explicit, and bytes and string objects will always compare unequal.** 

Python3.x 不会隐式转换 `8-bit binary data` 和 `Unicode text`
避免的 Python2.x 中的编码问题


###4.8.2. Bytearray Objects
`bytearray` objects are a **mutable** counterpart to `bytes` objects.

`bytes` 不可变类型  
`bytearray` 可变类型

The representation of bytearray objects uses the bytes literal format `(bytearray(b'...'))` since it is often more useful than e.g. `bytearray([46, 46, 46])`. You can always convert a bytearray object into a list of integers using `list(b)`.

####classmethod bytearray.fromhex(string)
This `bytearray` class method returns bytearray object, decoding the given string object. The string must contain two hexadecimal digits per byte, with ASCII spaces being ignored.

####bytearray.hex()
A reverse conversion function exists to transform a bytearray object into its hexadecimal representation.

###4.8.3. Bytes and Bytearray Operations
Both bytes and bytearray objects support the common sequence operations. They interoperate not just with operands of the same type, but with any `bytes-like object`. Due to this flexibility, they can be freely mixed in operations without causing errors. However, **the return type of the result may depend on the order of operands**.

    In [14]: (bytes(0) + bytearray(0)).__class__
    Out[14]: bytes
    
    In [15]: (bytearray(0) + bytes(0)).__class__
    Out[15]: bytearray

`bytes` 和 `bytearray` 的方法和 `str` 的差不多，不过参数需要为 `bytes-like object`(An object that supports the Buffer Protocol and can export a C-contiguous buffer. This includes all `bytes`, `bytearray`, and `array.array` objects, as well as many common `memoryview` objects) 


Some bytes and bytearray operations assume the use of ASCII compatible binary formats, and hence should be avoided when working with arbitrary binary data. These restrictions are covered below.

**Note: Using these ASCII based operations to manipulate binary data that is not stored in an ASCII based format may lead to data corruption.**

The following methods on bytes and bytearray objects can be used with arbitrary binary data.


####bytes.count(sub[, start[, end]])
####bytearray.count(sub[, start[, end]])

####bytes.decode(encoding="utf-8", errors="strict")
####bytearray.decode(encoding="utf-8", errors="strict")

####bytes.endswith(suffix[, start[, end]])
####bytearray.endswith(suffix[, start[, end]])

####bytes.find(sub[, start[, end]])
####bytearray.find(sub[, start[, end]])

    In [29]: bytes('你好世界', encoding='utf-8').find(bytes('好', encoding='utf-8'))
    Out[29]: 3
    In [300]: bytes('Y好', encoding='utf-8').find(bytes('好', encoding='utf-8'))
    Out[30]: 1

**返回的字节中的索引**

####bytes.index(sub[, start[, end]])
####bytearray.index(sub[, start[, end]])

####bytes.join(iterable)
####bytearray.join(iterable)
Return a bytes or bytearray object which is the concatenation of the binary data sequences in the `iterable` iterable. A `TypeError` will be raised if there are any values in iterable that are not `bytes-like objects`, including str objects. 

####static bytes.maketrans(from, to)
####static bytearray.maketrans(from, to)

####bytes.partition(sep)
####bytearray.partition(sep)

####bytes.replace(old, new[, count])
####bytearray.replace(old, new[, count])

####bytes.rfind(sub[, start[, end]])
####bytearray.rfind(sub[, start[, end]])

####bytes.rindex(sub[, start[, end]])
####bytearray.rindex(sub[, start[, end]])

####bytes.rpartition(sep)
####bytearray.rpartition(sep)

####bytes.startswith(prefix[, start[, end]])
####bytearray.startswith(prefix[, start[, end]])

####bytes.translate(table[, delete])
####bytearray.translate(table[, delete])

这个和 [`str.translate(table)`](https://docs.python.org/3.5/library/stdtypes.html#str.translate)用法不相同

The following methods on bytes and bytearray objects have default behaviours that assume the use of ASCII compatible binary formats, but can still be used with arbitrary binary data by passing appropriate arguments. 

####bytes.center(width[, fillbyte])
####bytearray.center(width[, fillbyte])

####bytes.ljust(width[, fillbyte])
####bytearray.ljust(width[, fillbyte])

####bytes.lstrip([chars])
####bytearray.lstrip([chars])

####bytes.rsplit(sep=None, maxsplit=-1)
####bytearray.rsplit(sep=None, maxsplit=-1)

####bytes.rstrip([chars])
####bytearray.rstrip([chars])

####bytes.split(sep=None, maxsplit=-1)
####bytearray.split(sep=None, maxsplit=-1)

####bytes.strip([chars])
####bytearray.strip([chars])

The following methods on bytes and bytearray objects assume the use of ASCII compatible binary formats and **should not be applied to arbitrary binary data.** 

####bytes.capitalize()
####bytearray.capitalize()
Return a copy of the sequence with each byte interpreted as an ASCII character, and the first byte capitalized and the rest lowercased. **Non-ASCII byte values are passed through unchanged**.

####bytes.isalpha()
####bytearray.isalpha()

####bytes.isdigit()
####bytearray.isdigit()

####bytes.isupper()
####bytearray.isupper()

####bytes.lower()
####bytearray.lower()

####bytes.splitlines(keepends=False)
####bytearray.splitlines(keepends=False)

####bytes.swapcase()
####bytearray.swapcase()
Unlike `str.swapcase()`, **it is always the case that `bin.swapcase().swapcase() == bin` for the binary versions**. Case conversions are symmetrical in ASCII, even though that is not generally true for arbitrary Unicode code points.

####bytes.title()
####bytearray.title()

####bytes.upper()
####bytearray.upper()

####bytes.zfill(width)
####bytearray.zfill(width)

###4.8.4. printf-style Bytes Formatting
[printf-style Bytes Formatting](https://docs.python.org/3.5/library/stdtypes.html#printf-style-bytes-formatting)

###4.8.5. Memory Views
`memoryview` objects allow Python code to access the internal data of an object that supports the `buffer protocol` without copying.

####class memoryview(obj)
Create a `memoryview` that references obj. obj must support the buffer protocol. Built-in objects that support the buffer protocol include `bytes` and `bytearray`.

A `memoryview` has the notion of an element, which is the atomic memory unit handled by the originating object obj. For many simple types such as `bytes` and `bytearray`, an element is a single byte, but other types such as `array.array` may have bigger elements.

`len(view)` is equal to the length of `tolist`

A `memoryview` supports slicing and indexing to expose its data. One-dimensional(一维) slicing will result in a subview:

    >>> v = memoryview(b'abcefg')
    >>> v[1]
    98
    >>> v[-1]
    103
    >>> v[1:4]
    <memory at 0x7f3ddc9f4350>
    >>> bytes(v[1:4])
    b'bce'

If `format` is one of the native format specifiers from the `struct` module, indexing with an integer or a tuple of integers is also supported and returns a single element with the correct type. One-dimensional memoryviews can be indexed with an integer or a one-integer tuple. Multi-dimensional memoryviews can be indexed with tuples of exactly ndim integers where ndim is the number of dimensions. Zero-dimensional memoryviews can be indexed with the empty tuple.

Here is an example with a non-byte format:

    >>> import array
    >>> a = array.array('l', [-11111111, 22222222, -33333333, 44444444])
    >>> m = memoryview(a)
    >>> m[0]
    -11111111
    >>> m[-1]
    44444444
    >>> m[::2].tolist()
    [-11111111, -33333333]

If the underlying object is writable, the memoryview supports one-dimensional slice assignment. **Resizing is not allowed**:

    >>> data = bytearray(b'abcefg')
    >>> v = memoryview(data)
    >>> v.readonly
    False
    >>> v[0] = ord(b'z')
    >>> data
    bytearray(b'zbcefg')
    >>> v[1:4] = b'123'
    >>> data
    bytearray(b'z123fg')
    >>> v[2:3] = b'spam'
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ValueError: memoryview assignment: lvalue and rvalue have different structures
    >>> v[2:6] = b'spam'
    >>> data
    bytearray(b'z1spam')


One-dimensional memoryviews of hashable (read-only) types with formats `‘B’`, `‘b’` or `‘c’` are also hashable. The hash is defined as `hash(m) == hash(m.tobytes())`:

    >>> v = memoryview(b'abcefg')
    >>> hash(v) == hash(b'abcefg')
    True
    >>> hash(v[2:4]) == hash(b'ce')
    True
    >>> hash(v[::-2]) == hash(b'abcefg'[::-2])
    True


`memoryview` has several methods:

####`__eq__(exporter)`
A memoryview and a `PEP 3118` exporter are equal if their **shapes are equivalent** and if **all corresponding values are equal** when the operands’ respective format codes are interpreted using struct syntax.

For the subset of struct format strings currently supported by `tolist()`, v and w are equal if `v.tolist() == w.tolist()`:

    >>> import array
    >>> a = array.array('I', [1, 2, 3, 4, 5])
    >>> b = array.array('d', [1.0, 2.0, 3.0, 4.0, 5.0])
    >>> c = array.array('b', [5, 3, 1])
    >>> x = memoryview(a)
    >>> y = memoryview(b)
    >>> x == a == y == b
    True
    >>> x.tolist() == a.tolist() == y.tolist() == b.tolist()
    True
    >>> z = y[::-2]
    >>> z == c
    True
    >>> z.tolist() == c.tolist()
    True

**If either format string is not supported by the `struct` module, then the objects will always compare as unequal** (even if the format strings and buffer contents are identical):

    >>> from ctypes import BigEndianStructure, c_long
    >>> class BEPoint(BigEndianStructure):
    ...     _fields_ = [("x", c_long), ("y", c_long)]
    ...
    >>> point = BEPoint(100, 200)
    >>> a = memoryview(point)
    >>> b = memoryview(point)
    >>> a == point
    False
    >>> a == b
    False
    
####tobytes()
Return the data in the buffer as a bytestring. This is equivalent to calling the `bytes` constructor on the memoryview

####hex()
Return a string object containing two hexadecimal digits for each byte in the buffer.

####tolist()
Return the data in the buffer as a list of elements.

####release()
Release the underlying buffer exposed by the memoryview object. Many objects take special actions when a view is held on them (for example, a `bytearray` would temporarily forbid resizing); therefore, calling release() is handy to remove these restrictions (and free any dangling resources) as soon as possible.

After this method has been called, any further operation on the view raises a `ValueError` (except `release()` itself which can be called multiple times):

    >>> m = memoryview(b'abc')
    >>> m.release()
    >>> m[0]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ValueError: operation forbidden on released memoryview object

The context management protocol can be used for a similar effect, using the `with` statement:

    >>> with memoryview(b'abc') as m:
    ...     m[0]
    ...
    97
    >>> m[0]
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ValueError: operation forbidden on released memoryview object

####cast(format[, shape])
Cast a memoryview to a new format or shape. shape defaults to [`byte_length//new_itemsize`], which means that the result view will be one-dimensional. The return value is a new memoryview, but **the buffer itself is not copied**. 

    >>> b = bytearray(b'zyz')
    >>> x = memoryview(b)
    >>> x[0] = b'a'
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ValueError: memoryview: invalid value for format "B"
    >>> y = x.cast('c')
    >>> y[0] = b'a'
    >>> b
    bytearray(b'ayz')


There are also several readonly attributes available:
####obj
The underlying object of the memoryview:

    >>> b  = bytearray(b'xyz')
    >>> m = memoryview(b)
    >>> m.obj is b
    True

####nbytes
`nbytes == product(shape) * itemsize == len(m.tobytes())`. This is the amount of space in bytes that the array would use in a contiguous representation. It is not necessarily equal to `len(m)`:

    >>> import array
    >>> a = array.array('i', [1,2,3,4,5])
    >>> m = memoryview(a)
    >>> len(m)
    5
    >>> m.nbytes
    20
    >>> y = m[::2]
    >>> len(y)
    3
    >>> y.nbytes
    12
    >>> len(y.tobytes())
    12

Multi-dimensional arrays:

    >>> import struct
    >>> buf = struct.pack("d"*12, *[1.5*x for x in range(12)])
    >>> x = memoryview(buf)
    >>> y = x.cast('d', shape=[3,4])
    >>> y.tolist()
    [[0.0, 1.5, 3.0, 4.5], [6.0, 7.5, 9.0, 10.5], [12.0, 13.5, 15.0, 16.5]]
    >>> len(y)
    3
    >>> y.nbytes
    96

####readonly
A bool indicating whether the memory is read only.

####format
A string containing the format (in struct module style) for each element in the view. 

####itemsize
The size in bytes of each element of the memoryview

####ndim
An integer indicating how many dimensions of a multi-dimensional array the memory represents.

    In [40]: import struct
    
    In [41]: buf = struct.pack('i'*12, *list(range(12)))
    
    In [42]: x = memoryview(buf)
    
    In [43]: y = x.cast('i', shape=[2,2,3])
    
    In [44]: y.tolist()
    Out[44]: [[[0, 1, 2], [3, 4, 5]], [[6, 7, 8], [9, 10, 11]]]
    
    In [45]: y.ndim
    Out[45]: 3


####shape
A tuple of integers the length of `ndim` giving the shape of the memory as an N-dimensional array.

    In [46]: y.shape
    Out[46]: (2, 2, 3)

####strides
A tuple of integers the length of `ndim` giving the size in bytes to access each element for each dimension of the array.

    In [47]: y.strides
    Out[47]: (24, 12, 4)

####suboffsets
Used internally for PIL-style arrays. The value is informational only.

####c_contiguous
A bool indicating whether the memory is C-contiguous.

####f_contiguous
A bool indicating whether the memory is Fortran contiguous.

####contiguous
A bool indicating whether the memory is contiguous.

##4.9. Set Types — set, frozenset
A set object is an **unordered** collection of **distinct** `hashable` objects. Common uses include membership testing, removing duplicates from a sequence, and computing mathematical operations such as intersection, union, difference, and symmetric difference. sets do not support indexing, slicing, or other sequence-like behavior.

There are currently two built-in set types, `set` and `frozenset`. The `set` type is mutable — the contents can be changed using methods like `add()` and `remove()`. Since it is mutable, it has no hash value and cannot be used as either a dictionary key or as an element of another set. The `frozenset` type is immutable and `hashable` — its contents cannot be altered after it is created; it can therefore be used as a dictionary key or as an element of another set.

Non-empty sets (not frozensets) can be created by placing a comma-separated list of elements within braces, for example: `{'jack', 'sjoerd'}`, in addition to the `set` constructor.

####class set([iterable])
####class frozenset([iterable])
Return a new set or frozenset object whose elements are taken from iterable. **The elements of a set must be hashable**. To represent sets of sets, the inner sets must be frozenset objects. 


instances of `set` and `frozenset` provide the following operations:

####isdisjoint(other)
Return `True` if the set has no elements in common with other. Sets are disjoint if and only if their intersection is the empty set.


集合的比较操作是集合运算
####issubset(other)
####set <= other
Test whether every element in the set is in other.

####set < other
Test whether the set is a proper subset of other, that is, `set <= other and set != other`.


####issuperset(other)
####set >= other
Test whether every element in other is in the set.

####set > other
Test whether the set is a proper superset of other, that is, `set >= other and set != other`.

####union(*others)
####set | other | ...
Return a new set with elements from the set and all others.

####intersection(*others)
####set & other & ...
Return a new set with elements common to the set and all others.

####difference(*others)
####set - other - ...
Return a new set with elements in the set that are not in the others.

####symmetric_difference(other)
####set ^ other
Return a new set with elements in either the set or other but not both.

####copy()
Return a new set with a shallow copy of s.

Note, the non-operator versions of `union()`, `intersection()`, `difference()`, and `symmetric_difference()`, `issubset()`, and `issuperset()` methods will accept any iterable as an argument. In contrast, their operator based counterparts require their arguments to be sets. This precludes error-prone constructions like `set('abc') & 'cbs'` in favor of the more readable `set('abc').intersection('cbs')`.

Instances of set are compared to instances of frozenset based on their members. For example, **`set('abc') == frozenset('abc')` returns True and so does` set('abc') in set([frozenset('abc')])`**.


Binary operations that mix `set` instances with `frozenset` **return the type of the first operand**. For example: `frozenset('ab') | set('bc')` returns an instance of `frozenset`.


####set.update(*others)
####set |= other | ...
Update the set, adding elements from all others.

####set.intersection_update(*others)
####set &= other & ...
Update the set, keeping only elements found in it and all others.

####difference_update(*others)
####set -= other | ...
Update the set, removing elements found in others.

####symmetric_difference_update(other)
####set ^= other
Update the set, keeping only elements found in either set, but not in both.

####add(elem)
Add element elem to the set.

####remove(elem)
Remove element elem from the set. Raises `KeyError` if elem is not contained in the set.

####discard(elem)
Remove element elem from the set if it is present.

作用同 `remove`，只是不会在元素不存在时抛出异常  

####pop()
Remove and return an arbitrary element from the set. Raises `KeyError` if the set is empty.

####clear()
Remove all elements from the set.

Note, the non-operator versions of the `update()`, `intersection_update()`, `difference_update()`, and `symmetric_difference_update()` methods will accept any iterable as an argument.

Note, the elem argument to the `__contains__()`, `remove()`, and `discard()` methods may be a set. **To support searching for an equivalent frozenset, the elem set is temporarily mutated during the search and then restored. During the search, the elem set should not be read or mutated since it does not have a meaningful value.**

查找是否有相同的 `frozenset` 期间，传入的参数会发生改变，并重新存储。所以这个期间不能读取或者改变参数的值  

##4.10. Mapping Types — dict
A mapping object maps hashable values to arbitrary objects. Mappings are mutable objects. There is currently only one standard mapping type, the dictionary. 

Note however, that **since computers store floating-point numbers as approximations it is usually unwise to use them as dictionary keys**.

####class dict(**kwarg)
####class dict(mapping, **kwarg)
####class dict(iterable, **kwarg)
If no positional argument is given, an empty dictionary is created. If a positional argument is given and it is a mapping object, a dictionary is created with the same key-value pairs as the mapping object. Otherwise, the positional argument must be an iterable object. **Each item in the iterable must itself be an iterable with exactly two objects**. The first object of each item becomes a key in the new dictionary, and the second object the corresponding value. If a key occurs more than once, the last value for that key becomes the corresponding value in the new dictionary.

`dict()` 也支持 `iterable` 参数

    >>> a = dict(one=1, two=2, three=3)
    >>> b = {'one': 1, 'two': 2, 'three': 3}
    >>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
    >>> d = dict([('two', 2), ('one', 1), ('three', 3)])
    >>> e = dict({'three': 3, 'one': 1}, two=2)
    >>> a == b == c == d == e
    True

####d[key]
Return the item of d with key key. Raises a KeyError if key is not in the map.

If a subclass of dict **defines a method `__missing__()`** and key is not present, the `d[key]` operation calls that method with the key key as argument. 

    >>> class Counter(dict):
    ...     def __missing__(self, key):
    ...         return 0
    >>> c = Counter()
    >>> c['red']
    0
    >>> c['red'] += 1
    >>> c['red']
    1


####iter(d)
Return an iterator over the keys of the dictionary. This is a shortcut for `iter(d.keys())`.


####classmethod fromkeys(seq[, value])
Create a new dictionary with keys from seq and values set to value.
`fromkeys()` is a class method that returns a new dictionary. value defaults to `None`.

    In [26]: dict.fromkeys(['a', 'b'], 'lala')
    Out[26]: {'a': 'lala', 'b': 'lala'}


####pop(key[, default])
If key is in the dictionary, remove it and return its value, else return default. If default is not given and key is not in the dictionary, a `KeyError` is raised.

可以指定默认值  

####popitem()
Remove and return an arbitrary (key, value) pair from the dictionary.

####setdefault(key[, default])
If key is in the dictionary, return its value. If not, insert key with a value of default and return default. default defaults to `None`.

    In [29]: d = dict(a=1, b=2)
    
    In [30]: d.setdefault('b', 10)
    Out[30]: 2
    
    In [31]: d
    Out[31]: {'a': 1, 'b': 2}
    
    In [32]: d.setdefault('c', 10)
    Out[32]: 10
    
    In [33]: d
    Out[33]: {'a': 1, 'b': 2, 'c': 10}



####update([other])
Update the dictionary with the key/value pairs from other, overwriting existing keys. Return `None`.

`update()` accepts either another dictionary object or an iterable of key/value pairs (as tuples or other iterables of length two). If keyword arguments are specified, the dictionary is then updated with those key/value pairs:`d.update(red=1, blue=2)`.

    In [34]: d = dict(a=1, b=2)
    
    In [35]: d.update(zip(['c', 'd'], [3, 4]))
    
    In [36]: d
    Out[36]: {'a': 1, 'b': 2, 'c': 3, 'd': 4}


###4.10.1. Dictionary view objects
The objects returned by `dict.keys()`, `dict.values()` and `dict.items()` are view objects. They provide a dynamic view on the dictionary’s entries, which means that **when the dictionary changes, the view reflects these changes**.

    In [2]: d = {'a':1}
    
    In [3]: k = d.keys()
    
    In [4]: k.__class__
    Out[4]: dict_keys
    
    In [5]: k
    Out[5]: dict_keys(['a'])
    
    In [6]: d.update(b=2)
    
    In [7]: k
    Out[7]: dict_keys(['a', 'b'])

####iter(dictview)
Return an iterator over the keys, values or items (represented as tuples of `(key, value)`) in the dictionary.

Keys and values are iterated over in an arbitrary order which is non-random, varies across Python implementations, and depends on the dictionary’s history of insertions and deletions. **If keys, values and items views are iterated over with no intervening modifications to the dictionary, the order of items will directly correspond.** This allows the creation of `(value, key)` pairs using zip(): `pairs = zip(d.values(), d.keys())`. 

Iterating views while adding or deleting entries in the dictionary may raise a `RuntimeError` or fail to iterate over all entries.

对字典进行迭代时不能插入或删除  

Keys views are set-like since their entries are unique and hashable. If all values are hashable, so that `(key, value)` pairs are unique and hashable, then the items view is also set-like. (Values views are not treated as set-like since the entries are generally not unique.) For set-like views, all of the operations defined for the abstract base class `collections.abc.Set` are available (for example, `==`, `<`, or `^`).

##4.11. Context Manager Types
[上下文管理器](https://docs.python.org/3.5/library/stdtypes.html#context-manager-types)

##4.12. Other Built-in Types
The interpreter supports several other kinds of objects. Most of these support only one or two operations.

###4.12.1. Modules
The only special operation on a module is attribute access: `m.name`, where m is a module and name accesses a name defined in m‘s symbol table. 

A special attribute of every module is `__dict__`. This is the dictionary containing the module’s symbol table. Modifying this dictionary will actually change the module’s symbol table, but direct assignment to the `__dict__` attribute is not possible (you can write `m.__dict__['a'] = 1`, which defines m.a to be 1, but you can’t write `m.__dict__ = {}`). **Modifying `__dict__` directly is not recommended.**

###4.12.2. Classes and Class Instances
See [Objects, values and types](https://docs.python.org/3.5/reference/datamodel.html#objects) and [Class definitions](https://docs.python.org/3.5/reference/compound_stmts.html#class) for these.

###4.12.3. Functions
**Function objects** are created by function definitions. **The only operation on a function object is to call it: `func(argument-list)`**.

There are really two flavors of function objects: built-in functions and user-defined functions. Both support the same operation (to call the function), but the implementation is different, hence the different object types.

###4.12.4. Methods
Methods are functions that are called using the attribute notation. There are two flavors: built-in methods (such as `append()` on lists) and class instance methods. Built-in methods are described with the types that support them.

方法调用时隐式传递` self`

**If you access a method (a function defined in a class namespace) through an instance, you get a special object: a bound method (also called instance method) object. When called, it will add the `self` argument to the argument list. Bound methods have two special read-only attributes: `m.__self__` is the object on which the method operates, and `m.__func__` is the function implementing the method. Calling `m(arg-1, arg-2, ..., arg-n)` is completely equivalent to calling `m.__func__(m.__self__, arg-1, arg-2, ..., arg-n)`.**

    In [10]: class A(object):
        ...:     def func(self):
        ...:         return 0
        ...:     
    
    In [11]: a = A()
    
    In [12]: a.func
    Out[12]: <bound method A.func of <__main__.A object at 0x7f2b2028ae48>>
    
    In [13]: a.func.__func__(a.func.__self__)
    Out[13]: 0

Like function objects, bound method objects support getting arbitrary attributes. However, **since method attributes are actually stored on the underlying function object (`meth.__func__`), setting method attributes on bound methods is disallowed.** Attempting to set an attribute on a method results in an `AttributeError` being raised. In order to set a method attribute, you need to explicitly set it on the underlying function object:

    >>> class C:
    ...     def method(self):
    ...         pass
    ...
    >>> c = C()
    >>> c.method.whoami = 'my name is method'  # can't set on the method
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    AttributeError: 'method' object has no attribute 'whoami'
    >>> c.method.__func__.whoami = 'my name is method'
    >>> c.method.whoami
    'my name is method'

形如 `obj.method` 获取的是一个特殊的 `bound method object` 只能从中获取任意的属性，而不能设置。如果想要为方法设置属性则要获取 `function object`  

###4.12.5. Code Objects
Code objects are used by the implementation to represent “pseudo-compiled” executable Python code such as a function body. **They differ from function objects because they don’t contain a reference to their global execution environment**. Code objects are returned by the built-in `compile()` function and can be extracted from function objects through their `__code__` attribute. See also the code module.

A code object can be executed or evaluated by passing it (instead of a source string) to the `exec()` or `eval()` built-in functions.

###4.12.6. Type Objects
Type objects represent the various object types. An object’s type is accessed by the built-in function `type()`. 

###4.12.7. The Null Object
There is exactly one null object, named `None` (a built-in name). `type(None)()` produces the same singleton.

###4.12.8. The Ellipsis Object
This object is commonly used by slicing (see `Slicings`). It supports no special operations. There is exactly one ellipsis object, named `Ellipsis` (a built-in name). `type(Ellipsis)()` produces the `Ellipsis` singleton.

It is written as `Ellipsis` or ```...``.

###4.12.9. The NotImplemented Object
This object is returned from comparisons and binary operations when they are asked to operate on types they don’t support. See Comparisons for more information. There is exactly one `NotImplemented` object. `type(NotImplemented)()` produces the singleton instance.

It is written as `NotImplemented`.

###4.12.10. Boolean Values
Boolean values are the two constant objects `False` and `True`. The built-in function `bool()` can be used to convert any value to a Boolean, if the value can be interpreted as a truth value (see section Truth Value Testing above).

###4.12.11. Internal Objects
See [The standard type hierarchy](https://docs.python.org/3.5/reference/datamodel.html#types) for this information. It describes stack frame objects, traceback objects, and slice objects.

##4.13. Special Attributes
The implementation adds a few special read-only attributes to several object types, where they are relevant. **Some of these are not reported by the `dir()` built-in function**.

####`object.__dict__`

A dictionary or other mapping object used to store an object’s (writable) attributes.

instance.__class__

    The class to which a class instance belongs.

class.__bases__

    The tuple of base classes of a class object.

####definition.__name__`
The name of the class, function, method, descriptor, or generator instance.

####`definition.__qualname__`
The qualified name of the class, function, method, descriptor, or generator instance.

什么是一个 qualified name

A dotted name showing the “path” from a module’s global scope to a class, function or method defined in that module, as defined in PEP 3155. For top-level functions and classes, the qualified name is the same as the object’s name:

    >>> class C:
    ...     class D:
    ...         def meth(self):
    ...             pass
    ...
    >>> C.__qualname__
    'C'
    >>> C.D.__qualname__
    'C.D'
    >>> C.D.meth.__qualname__
    'C.D.meth'
    >>> C.D.meth.__name__
    'meth'

####`class.__mro__`
This attribute is a tuple of classes that are considered when looking for base classes during method resolution.

####class.mro()
This method can be overridden by a metaclass to customize the method resolution order for its instances. It is called at class instantiation, and its result is stored in `__mro__`.

####`class.__subclasses__()`
Each class keeps a list of weak references to its immediate subclasses. This method returns a list of all those references still alive. Example:

    >>> int.__subclasses__()
    [<class 'bool'>]
