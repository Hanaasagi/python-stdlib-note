# [7.1. struct — Interpret bytes as packed binary data](https://docs.python.org/3.5/library/struct.html)

This module performs conversions between Python values and C structs represented as Python `bytes` objects. This can be used in handling binary data stored in files or from network connections, among other sources. It uses Format Strings as compact descriptions of the layout of the C structs and the intended conversion to/from Python values.

## 7.1.1. Functions and Exceptions
The module defines the following exception and functions:

### exception struct.error
Exception raised on various occasions; argument is a string describing what is wrong.

### struct.pack(fmt, v1, v2, ...)
Return a bytes object containing the values v1, v2, ... packed according to the format string fmt. The arguments must match the values required by the format exactly.

### struct.pack_into(fmt, buffer, offset, v1, v2, ...)
Pack the values v1, v2, ... according to the format string fmt and write the packed bytes into the writable buffer buffer starting at position offset. Note that offset is a required argument.

### struct.unpack(fmt, buffer)
Unpack from the buffer buffer (presumably packed by `pack(fmt, ...)`) according to the format string fmt. The result is a tuple even if it contains exactly one item. The buffer’s size in bytes must match the size required by the format, as reflected by `calcsize()`.

### struct.unpack_from(fmt, buffer, offset=0)
Unpack from buffer starting at position offset, according to the format string fmt. The result is a tuple even if it contains exactly one item. The buffer’s size in bytes, minus offset, must be at least the size required by the format, as reflected by `calcsize()`.

### struct.iter_unpack(fmt, buffer)
Iteratively unpack from the buffer buffer according to the format string fmt. This function returns an iterator which will read equally-sized chunks from the buffer until all its contents have been consumed. The buffer’s size in bytes must be a multiple of the size required by the format, as reflected by `calcsize()`.

### struct.calcsize(fmt)
Return the size of the struct (and hence of the bytes object produced by pack(fmt, ...)) corresponding to the format string fmt.

## 7.1.2. Format Strings

略

## 7.1.2.3. Examples

这里要主义到这样一个问题：内存布局对齐

在 C 中一个结构体的大小并不是其内部类型大小的和  

> For example, when the computer's word size is 4 bytes (a byte means 8
> bits on most machines, but could be different on some systems), the
> data to be read should be at a memory address which is some multiple
> of 4. When this is not the case, e.g. the data starts at address 14
> instead of 16, then the computer has to read two or more 4 byte chunks
> and do some calculation before the requested data has been read, or it
> may generate an alignment fault. Even though the previous data
> structure end is at address 13, the next data structure should start
> at address 16. Two padding bytes are inserted between the two data
> structures at addresses 14 and 15 to align the next data structure at
> address 16.

引用自[Data structure alignment](https://en.wikipedia.org/wiki/Data_structure_alignment)

所以会产生如下结果  

    In [33]: struct.calcsize('h')
    Out[33]: 2

    In [34]: struct.calcsize('l')
    Out[34]: 8

    In [35]: struct.calcsize('hl')
    Out[35]: 16

    In [36]: struct.calcsize('lh')
    Out[36]: 10

一个从 C 结构体数据转换成 Python 的命名元组的例子  

    In [37]: record = b'raymond   \x32\x12\x08\x01\x08'

    In [38]: from collections import namedtuple

    In [39]: Student = namedtuple('Student', 'name serialnum school gradelevel')

    In [40]: Student._make(struct.unpack('<10sHHb', record))
    Out[40]: Student(name=b'raymond   ', serialnum=4658, school=264, gradelevel=8)


## 7.1.3. Classes
The struct module also defines the following type:

### class struct.Struct(format)
Return a new Struct object which writes and reads binary data according to the format string format. Creating a Struct object once and calling its methods is more efficient than calling the struct functions with the same format since the format string only needs to be compiled once.

Compiled Struct objects support the following methods and attributes:

#### pack(v1, v2, ...)
#### pack_into(buffer, offset, v1, v2, ...)
#### unpack(buffer)
#### unpack_from(buffer, offset=0)
#### iter_unpack(buffer)
#### format
#### size
