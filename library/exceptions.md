# [5. Built-in Exceptions](https://docs.python.org/3.5/library/exceptions.html)

In Python, all exceptions must be instances of a class that derives from `BaseException`.

The built-in exception classes can be subclassed to define new exceptions; **programmers are encouraged to derive new exceptions from the `Exception` class or one of its subclasses, and not from `BaseException`**.

**When raising (or re-raising) an exception in an `except` or `finally` clause `__context__` is automatically set to the last exception caught; if the new exception is not handled the traceback that is eventually displayed will include the originating exception(s) and the final exception**.

Python3.x 中的 traceback 信息会形成一条链  

    try:
        1 / 0
    except:
        raise ValueError


    Traceback (most recent call last):
      File "tmp.py", line 2, in <module>
        1 / 0
    ZeroDivisionError: division by zero

    During handling of the above exception, another exception occurred:

    Traceback (most recent call last):
      File "tmp.py", line 4, in <module>
        raise ValueError
    ValueError


When raising a new exception (rather than using a bare raise to re-raise the exception currently being handled), **the implicit exception context can be supplemented with an explicit cause by using `from` with `raise`**:

    raise new_exc from original_exc

The expression following `from` must be an exception or `None`. **It will be set as `__cause__` on the raised exception. Setting ``__cause__` also implicitly sets the `__suppress_context__` attribute to True, so that using r`aise new_exc from None` effectively replaces the old exception with the new one for display purposes** (e.g. converting KeyError to AttributeError, while leaving the old exception available in `__context__` for introspection when debugging.

如果 `__suppress_context__` 为 `True` 则存在一个之前发生的异常，保存在 `__cause__` 中

    In [6]: try:
       ...:     raise ZeroDivisionError from TypeError
       ...: except Exception as e:
       ...:     b = e
       ...:     

    In [7]: b
    Out[7]: ZeroDivisionError()

    In [8]: b.__cause__
    Out[8]: TypeError()

    In [9]: b.__suppress_context__
    Out[9]: True

## 5.1. Base classes
经常用来作为基类的几个异常  

#### exception BaseException
**The base class for all built-in exceptions. It is not meant to be directly inherited by user-defined classes (for that, use Exception)**. If str() is called on an instance of this class, the representation of the argument(s) to the instance are returned, or the empty string when there were no arguments.

args

The tuple of arguments given to the exception constructor. Some built-in exceptions (like `OSError`) expect a certain number of arguments and assign a special meaning to the elements of this tuple, while others are usually called only with a single string giving an error message.

with_traceback(tb)

This method sets `tb` as the new traceback for the exception and returns the exception object. It is usually used in exception handling code like this:

    try:
        ...
    except SomeException:
        tb = sys.exc_info()[2]
        raise OtherException(...).with_traceback(tb)


#### exception Exception
All built-in, non-system-exiting exceptions are derived from this class. **All user-defined exceptions should also be derived from this class**.

#### exception ArithmeticError
The base class for those built-in exceptions that are raised for various arithmetic errors: `OverflowError`, `ZeroDivisionError`, `FloatingPointError`.

#### exception BufferError
Raised when a buffer related operation cannot be performed.

#### exception LookupError
The base class for the exceptions that are raised when a key or index used on a mapping or sequence is invalid: `IndexError`, `KeyError`. This can be raised directly by codecs.lookup().

## 5.2. Concrete exceptions

不在赘述 [Concrete exceptions](https://docs.python.org/3.5/library/exceptions.html#concrete-exceptions)

## 5.3. Warnings

## 5.4. Exception hierarchy

    BaseException
     +-- SystemExit
     +-- KeyboardInterrupt
     +-- GeneratorExit
     +-- Exception
          +-- StopIteration
          +-- StopAsyncIteration
          +-- ArithmeticError
          |    +-- FloatingPointError
          |    +-- OverflowError
          |    +-- ZeroDivisionError
          +-- AssertionError
          +-- AttributeError
          +-- BufferError
          +-- EOFError
          +-- ImportError
          +-- LookupError
          |    +-- IndexError
          |    +-- KeyError
          +-- MemoryError
          +-- NameError
          |    +-- UnboundLocalError
          +-- OSError
          |    +-- BlockingIOError
          |    +-- ChildProcessError
          |    +-- ConnectionError
          |    |    +-- BrokenPipeError
          |    |    +-- ConnectionAbortedError
          |    |    +-- ConnectionRefusedError
          |    |    +-- ConnectionResetError
          |    +-- FileExistsError
          |    +-- FileNotFoundError
          |    +-- InterruptedError
          |    +-- IsADirectoryError
          |    +-- NotADirectoryError
          |    +-- PermissionError
          |    +-- ProcessLookupError
          |    +-- TimeoutError
          +-- ReferenceError
          +-- RuntimeError
          |    +-- NotImplementedError
          |    +-- RecursionError
          +-- SyntaxError
          |    +-- IndentationError
          |         +-- TabError
          +-- SystemError
          +-- TypeError
          +-- ValueError
          |    +-- UnicodeError
          |         +-- UnicodeDecodeError
          |         +-- UnicodeEncodeError
          |         +-- UnicodeTranslateError
          +-- Warning
               +-- DeprecationWarning
               +-- PendingDeprecationWarning
               +-- RuntimeWarning
               +-- SyntaxWarning
               +-- UserWarning
               +-- FutureWarning
               +-- ImportWarning
               +-- UnicodeWarning
               +-- BytesWarning
               +-- ResourceWarning
