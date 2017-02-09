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

####float.is_integer()
判断浮点数是否为整数  


