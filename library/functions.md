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


