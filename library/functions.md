# [2. Built-in Functions](https://docs.python.org/3.5/library/functions.html)

### bin(x)
### hex(x)
### ord(x)

 If x is not a Python int object, it has to define an `__index__()` method that returns an integer.

### chr()  
### ord()
支持 Unicode 编码，合法范围为 0~0x10FFFF (Python2 中为 0~255)  


### dir([object])
**Without arguments, return the list of names in the current local scope**. With an argument, attempt to return a list of valid attributes for that object.
If the object has a method named `__dir__()`, this method will be called and must return the list of attributes. If the object does not provide `__dir__()`, the function tries its best to gather information from the object’s `__dict__` attribute, if defined, and from its type object.

### divmod(a, b)
same as `(a // b, a % b)` for integers, `(math.floor(a / b), a % b)` for floats  

### enumerate(iterable, start=0)
注意第二个参数  

等价实现  

    def enumerate(sequence, start=0):
        n = start
        for elem in sequence:
            yield n, elem
            n += 1

### filter(function, iterable)
If function is None, the identity function is assumed, that is, all elements of iterable that are false are removed.  

### class float([x])  

The argument may also be a string representing a NaN (not-a-number), or a positive or negative infinity.  

    In [11]: float('nan')
    Out[11]: nan

    In [12]: float('inf')
    Out[12]: inf

    In [13]: float('-inf')
    Out[13]: -inf

For a general Python object `x`, `float(x)` delegates to `x.__float__()`.


### class frozenset([iterable])  
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


### id(object)

Return the “identity” of an object. This is an integer which is guaranteed to be unique and constant for this object during its lifetime. **Two objects with non-overlapping lifetimes may have the same id() value.**

*CPython implementation detail: This is the address of the object in memory.*

当两个对象具有不重叠的生命周期时，会有相同的 `id`  

    In [5]: id([1]) == id([2])
    Out[5]: True

    In [6]: id(set([1])) == id(set([2]))
    Out[6]: True

`[1]` 没有被任何变量引用，所以使用结束后，便会被回收掉，不会和 `[2]` 的生命周期产生重叠  

### iter(object[, sentinel])  
Return an iterator object. The first argument is interpreted very differently depending on the presence of the second argument. Without a second argument, object must be a collection object which supports the **iteration protocol** (the `__iter__()` method), or it must support the **sequence protocol** (the `__getitem__()` method with integer arguments starting at 0). If it does not support either of those protocols, TypeError is raised. **If the second argument, sentinel, is given, then object must be a callable object.** The iterator created in this case will call object with no arguments for each call to its `__next__()` method; if the value returned is equal to sentinel, StopIteration will be raised, otherwise the value will be returned.

第一种实现 `__getitem__`  

    class A(object):
        def __getitem__(self, index):
            pass

第二种实现 `__iter__`

    class A(object):
        def __iter__(self):
            # 此处返回可迭代对象
            return self
        def __next__(self):
            pass

第三种实现 `__call__`，只能用在给定哨兵的情况下    

    class A(object):
        def __call__(self):
            pass

文档中的 Demo  

    with open('mydata.txt') as fp:
        for line in iter(fp.readline, ''):
            process_line(line)

### map(function, iterable, ...)
Return an iterator that applies function to every item of iterable, yielding the results. **If additional iterable arguments are passed, function must take that many arguments and is applied to the items from all iterables in parallel.** With multiple iterables, the iterator stops when the shortest iterable is exhausted. For cases where the function inputs are already arranged into argument tuples, see itertools.starmap().

### max(iterable, *[, key, default])  
### max(arg1, arg2, *args[, key])
若出现并列最大的，则取第一个遇到的，和 `sort` 一致  
取前 n 个 `heapq.nlargest(1, iterable, key=keyfunc)`

### memoryview(obj)
可以建立内存映射


### next(iterator[, default])

Retrieve the next item from the iterator by calling its `__next__()` method. **If default is given, it is returned if the iterator is exhausted, otherwise StopIteration is raised.**


### open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)  

    mode 'x' 	open for exclusive creation, failing if the file already exists

'x' 和 `w` 相似，用来写入文件。不过 `x` 要求文件不存在  


A custom opener can be used by passing a callable as opener. The underlying file descriptor for the file object is then obtained by calling opener with (file, flags). opener must return an open file descriptor (passing os.open as opener results in functionality similar to passing None).

The newly created file is non-inheritable.

The following example uses the dir_fd parameter of the os.open() function to open a file relative to a given directory:

    >>> import os
    >>> dir_fd = os.open('somedir', os.O_RDONLY)
    >>> def opener(path, flags):
    ...     return os.open(path, flags, dir_fd=dir_fd)
    ...
    >>> with open('spamspam.txt', 'w', opener=opener) as f:
    ...     print('This will be written to somedir/spamspam.txt', file=f)
    ...
    >>> os.close(dir_fd)  # don't leak a file descriptor


### pow(x, y[, z])
Return x to the power y; if z is present, return x to the power y, modulo z (**computed more efficiently than pow(x, y) % z**). The two-argument form pow(x, y) is equivalent to using the power operator: x**y.  
这个计算方法 SICP 中提到过  


### round(number[, ndigits])
The behavior of `round()` for floats can be surprising: for example, `round(2.675, 2)` gives `2.67` instead of the expected `2.68`. This is not a bug: it’s a result of the fact that most decimal fractions can’t be represented exactly as a float. See Floating Point Arithmetic: Issues and Limitations for more information.


### sorted(iterable[, key][, reverse])

Return a new sorted list from the items in iterable.

Has two optional arguments which must be specified as keyword arguments.

key specifies a function of one argument that is used to extract a comparison key from each list element: `key=str.lower`. The default value is None (compare the elements directly).

reverse is a boolean value. If set to True, then the list elements are sorted as if each comparison were reversed.

可以直接返回逆序，不需要 `reversed(sorted())`

### super([type[, object-or-type]])

`__mro__` 链，解决多继承中的 `diamond diagrams` 问题


### class type(object)
### class type(name, bases, dict)

With one argument, return the type of an object. The return value is a type object and generally the same object as returned by `object.__class__`.

The isinstance() built-in function is recommended for testing the type of an object, because it takes subclasses into account.

With three arguments, return a new type object. This is essentially a dynamic form of the class statement. The name string is the class name and becomes the `__name__` attribute; the bases tuple itemizes the base classes and becomes the `__bases__` attribute; and the dict dictionary is the namespace containing definitions for class body and is copied to a standard dictionary to become the `__dict__` attribute. For example, the following two statements create identical type objects:

    >>> class X:
    ...     a = 1
    ...
    >>> X = type('X', (object,), dict(a=1))



### vars([object])

Return the `__dict__` attribute for a module, class, instance, or any other object with a `__dict__` attribute.

Objects such as modules and instances have an updateable `__dict__` attribute; however, other objects may have write restrictions on their `__dict__` attributes (for example, classes use a types.MappingProxyType to prevent direct dictionary updates).

Without an argument, `vars()` acts like `locals()`. Note, the locals dictionary is only useful for reads since updates to the locals dictionary are ignored.



### zip(*iterables)

Make an iterator that aggregates elements from each of the iterables.

Returns an iterator of tuples, where the i-th tuple contains the i-th element from each of the argument sequences or iterables. The iterator stops when the shortest input iterable is exhausted. With a single iterable argument, it returns an iterator of 1-tuples. With no arguments, it returns an empty iterator. Equivalent to:

    def zip(*iterables):
        # zip('ABCD', 'xy') --> Ax By
        sentinel = object()
        iterators = [iter(it) for it in iterables]
        while iterators:
            result = []
            for it in iterators:
                elem = next(it, sentinel)
                if elem is sentinel:
                    return
                result.append(elem)
            yield tuple(result)

The left-to-right evaluation order of the iterables is guaranteed. This makes possible an idiom for clustering a data series into n-length groups using `zip(*[iter(s)]*n)`. **This repeats the same iterator n times so that each output tuple has the result of n calls to the iterator. This has the effect of dividing the input into n-length chunks.**


    In [24]: s = [1,2,3,4]

    In [25]: n = 2

    In [26]: zip(*[iter(s)]*n)
    Out[26]: <zip at 0xb4f8e6ac>

    In [27]: r = zip(*[iter(s)]*n)

    In [28]: list(r)
    Out[28]: [(1, 2), (3, 4)]

这里引用的是同一个对象，所以对第一个 `iterator` 调用 `next` 时返回 `1`，接着对第二个 `iterator` 调用 `next` 时返回 `2`  

`zip()` should only be used with unequal length inputs when you don’t care about trailing, unmatched values from the longer iterables. If those values are important, use `itertools.zip_longest()` instead.

**zip() in conjunction with the * operator can be used to unzip a list**:

    >>> x = [1, 2, 3]
    >>> y = [4, 5, 6]
    >>> zipped = zip(x, y)
    >>> list(zipped)
    [(1, 4), (2, 5), (3, 6)]
    >>> x2, y2 = zip(*zip(x, y))
    >>> x == list(x2) and y == list(y2)
    True

### `__import__`(name, globals=None, locals=None, fromlist=(), level=0)
用于 hook
