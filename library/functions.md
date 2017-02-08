Python3.5  

###bin(x)

    Convert an integer number to a binary string. The result is a valid Python expression. If x is not a Python int object, it has to define an __index__() method that returns an integer.

###真值判断  
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
3) 布尔运算 `and`, `or` 时，返回的是操作数

###chr()  
支持 Unicode 编码，合法范围为 0~0x10FFFF (Python2 中为 0~255)  


###dir([object])
**Without arguments, return the list of names in the current local scope**. With an argument, attempt to return a list of valid attributes for that object.
If the object has a method named `__dir__()`, this method will be called and must return the list of attributes. If the object does not provide `__dir__()`, the function tries its best to gather information from the object’s `__dict__` attribute, if defined, and from its type object. 

###divmod(a, b)
same as `(a // b, a % b)` for integers, `(math.floor(a / b), a % b)` for floats  

###enumerate(iterable, start=0)
注意第二个参数  

等价实现  

    def enumerate(sequence, start=0):
        n = start
        for elem in sequence:
            yield n, elem
            n += 1

###filter(function, iterable)
If function is None, the identity function is assumed, that is, all elements of iterable that are false are removed.  

###class float([x])  

The argument may also be a string representing a NaN (not-a-number), or a positive or negative infinity.  

    In [11]: float('nan')
    Out[11]: nan
    
    In [12]: float('inf')
    Out[12]: inf
    
    In [13]: float('-inf')
    Out[13]: -inf

For a general Python object `x`, `float(x)` delegates to `x.__float__()`.


###class frozenset([iterable])  
不可变 `set`, 可以用来作为字典的键值  

    In [30]: b = set([1,2])
    
    In [31]: hash(b)
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-31-7a1b7036ac14> in <module>()
    ----> 1 hash(b)
    
    TypeError: unhashable type: 'set'
    
    In [32]: b = frozenset([1,2])
    
    In [33]: hash(b)
    Out[33]: -924239479


###id(object)

Return the “identity” of an object. This is an integer which is guaranteed to be unique and constant for this object during its lifetime. **Two objects with non-overlapping lifetimes may have the same id() value.**

*CPython implementation detail: This is the address of the object in memory.*

当两个对象具有不重叠的生命周期时，会有相同的 `id`  

    In [5]: id([1]) == id([2])
    Out[5]: True
    
    In [6]: id(set([1])) == id(set([2]))
    Out[6]: True

`[1]` 没有被任何变量引用，所以使用结束后，便会被回收掉，不会和 `[2]` 的生命周期产生重叠  

###iter(object[, sentinel])  
Return an iterator object. The first argument is interpreted very differently depending on the presence of the second argument. Without a second argument, object must be a collection object which supports the iteration protocol (the __iter__() method), or it must support the sequence protocol (the __getitem__() method with integer arguments starting at 0). If it does not support either of those protocols, TypeError is raised. If the second argument, sentinel, is given, then object must be a callable object. The iterator created in this case will call object with no arguments for each call to its __next__() method; if the value returned is equal to sentinel, StopIteration will be raised, otherwise the value will be returned.
