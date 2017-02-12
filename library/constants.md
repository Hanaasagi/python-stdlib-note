#[3. Built-in Constants](https://docs.python.org/3.5/library/constants.html)


###Ellipsis

The same as `...`. Special value used mostly in conjunction with extended slicing syntax for user-defined container data types.

一个常量代表 `...`，可以用来实现一个有趣的伪 `Haskell` `List`

    In [57]: class InfiniteSeq(object):
        ...:     def __getitem__(self, key):
        ...:         if isinstance(key, tuple):
        ...:             for index, value in enumerate(key):
        ...:                 if value is Ellipsis:
        ...:                     if index > 1:
        ...:                         step = key[index-1] - key[index-2]
        ...:                     else:
        ...:                         step = 1
        ...:                     value = key[index-1] + step
        ...:                     end = key[index+1] if index+1 < len(key) else float('INF')
        ...:                     while value < end:
        ...:                         yield value
        ...:                         value = value + step
        ...:                     continue
        ...:                 yield value
        ...:         else:
        ...:             raise SyntaxError
        ...:         
    
    In [58]: l = InfiniteSeq()
    
    In [59]: list(l[1,3,...,5])
    Out[59]: [1, 3, 5]
    
    In [60]: list(l[1,3,1,5,67,89,3])
    Out[60]: [1, 3, 1, 5, 67, 89, 3]
    
    In [61]: list(l[1,3,...,6])
    Out[61]: [1, 3, 5, 6]
    
    In [62]: list(l[1,3,...,7])
    Out[62]: [1, 3, 5, 7]
    
    In [63]: list(l[1,3,...,8])
    Out[63]: [1, 3, 5, 7, 8]
    
    In [64]: list(l[1,3,...,8,9])
    Out[64]: [1, 3, 5, 7, 8, 9]
    
    In [65]: list(l[1,3,...,8,9,...,15])
    Out[65]: [1, 3, 5, 7, 8, 9, 10, 11, 12, 13, 14, 15]
    


