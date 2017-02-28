#[7.2. codecs — Codec registry and base classes](https://docs.python.org/3.5/library/codecs.html)

This module defines base classes for standard Python codecs (encoders and decoders) and provides access to the internal Python codec registry, which manages the codec and error handling lookup process. **Most standard codecs are text encodings, which encode text to bytes, but there are also codecs provided that encode text to text, and bytes to bytes.** Custom codecs may encode and decode between arbitrary types, but some module features are restricted to use specifically with text encodings, or with codecs that encode to bytes.

The module defines the following functions for encoding and decoding with any codec:

###codecs.encode(obj, encoding='utf-8', errors='strict')
Encodes obj using the codec registered for encoding.

###codecs.decode(obj, encoding='utf-8', errors='strict')
Decodes obj using the codec registered for encoding.

###codecs.lookup(encoding)
Looks up the codec info in the Python codec registry and returns a `CodecInfo` object as defined below.
**Encodings are first looked up in the registry’s cache. If not found, the list of registered search functions is scanned.** If no `CodecInfo` object is found, a `LookupError` is raised. Otherwise, the `CodecInfo` object is stored in the cache and returned to the caller.

###class codecs.CodecInfo(encode, decode, streamreader=None, streamwriter=None, incrementalencoder=None, incrementaldecoder=None, name=None)
Codec details when looking up the codec registry. The constructor arguments are stored in attributes of the same name:

####name
The name of the encoding.

####encode
####decode
The stateless encoding and decoding functions. These must be functions or methods which have the same interface as the `encode()` and `decode()` methods of Codec instances (see Codec Interface). The functions or methods are expected to work in a stateless mode.

####incrementalencoder
####incrementaldecoder
Incremental encoder and decoder classes or factory functions. These have to provide the interface defined by the base classes `IncrementalEncoder` and `IncrementalDecoder`, respectively. Incremental codecs can maintain state.

####streamwriter
####streamreader
Stream writer and reader classes or factory functions. These have to provide the interface defined by the base classes `StreamWriter` and `StreamReader`, respectively. Stream codecs can maintain state.

To simplify access to the various codec components, the module provides these additional functions which use `lookup()` for the codec lookup:

###codecs.getencoder(encoding)
Look up the codec for the given encoding and return its encoder function.
###codecs.getdecoder(encoding)

###codecs.getincrementalencoder(encoding)
Look up the codec for the given encoding and return its incremental encoder class or factory function.
###codecs.getincrementaldecoder(encoding)

###codecs.getreader(encoding)
Look up the codec for the given encoding and return its StreamReader class or factory function.
###codecs.getwriter(encoding)


Custom codecs are made available by registering a suitable codec search function:

###codecs.register(search_function)
Register a codec search function. Search functions are expected to take one argument, being the encoding name in all lower case letters, and return a `CodecInfo` object. In case a search function cannot find a given encoding, it should return `None`.

**Note: Search function registration is not currently reversible, which may cause problems in some cases, such as unit testing or module reloading.** 


While the builtin `open()` and the associated io module are the recommended approach for working with encoded text files, **this module provides additional utility functions and classes that allow the use of a wider range of codecs when working with binary files**:

###codecs.open(filename, mode='r', encoding=None, errors='strict', buffering=1)
Open an encoded file using the given mode and return an instance of `StreamReaderWriter`, providing transparent encoding/decoding. The default file mode is `'r'`, meaning to open the file in read mode.

Note: Underlying encoded files are always opened in binary mode. No automatic conversion of `'\n'` is done on reading and writing. The mode argument may be any binary mode acceptable to the built-in `open()` function; the `'b'` is automatically added. 

encoding specifies the encoding which is to be used for the file. Any encoding that encodes to and decodes from bytes is allowed, and the data types supported by the file methods depend on the codec used.

errors may be given to define the error handling. It defaults to `'strict'` which causes a `ValueError` to be raised in case an encoding error occurs.

buffering has the same meaning as for the built-in `open()` function. It defaults to line buffered.

###codecs.EncodedFile(file, data_encoding, file_encoding=None, errors='strict')
Return a `StreamRecoder` instance, a wrapped version of file which provides transparent transcoding. The original file is closed when the wrapped version is closed.

**Data written to the wrapped file is decoded according to the given data_encoding and then written to the original file as bytes using file_encoding.** Bytes read from the original file are decoded according to file_encoding, and the result is encoded using data_encoding.

If file_encoding is not given, it defaults to data_encoding.

###codecs.iterencode(iterator, encoding, errors='strict', **kwargs)

Uses an incremental encoder to iteratively encode the input provided by iterator. This function is a generator. The errors argument (as well as any other keyword argument) is passed through to the incremental encoder.

This function requires that the codec accept text `str` objects to encode. Therefore **it does not support bytes-to-bytes encoders** such as `base64_codec`.

###codecs.iterdecode(iterator, encoding, errors='strict', **kwargs)
This function requires that the codec accept `bytes` objects to decode. Therefore **it does not support text-to-text encoders** such as `rot_13`, although `rot_13` may be used equivalently with iterencode().

###codecs.BOM
###codecs.BOM_BE
###codecs.BOM_LE
###codecs.BOM_UTF8
###codecs.BOM_UTF16
###codecs.BOM_UTF16_BE
###codecs.BOM_UTF16_LE
###codecs.BOM_UTF32
###codecs.BOM_UTF32_BE
###codecs.BOM_UTF32_LE

BOM = Byte Order Mark 字节序标志  


##7.2.1. Codec Base Classes
The `codecs` module defines a set of base classes which define the interfaces for working with codec objects, and can also be used as the basis for custom codec implementations.

Each codec has to define four interfaces to make it usable as codec in Python: stateless encoder, stateless decoder, stream reader and stream writer. The stream reader and writers typically reuse the stateless encoder/decoder to implement the file protocols. Codec authors also need to define how the codec will handle encoding and decoding errors.

###7.2.1.1. Error Handlers
预置的 handler 有 
`'strict'`  codecs.strict_errors(exception)
`'ignore'`  codecs.replace_errors(exception)
`'replace'` codecs.ignore_errors(exception)
`'xmlcharrefreplace'` codecs.xmlcharrefreplace_errors(exception)
`'backslashreplace'` codecs.backslashreplace_errors(exception)
`'namereplace'` codecs.namereplace_errors(exception)
`'surrogateescape'`
`'surrogatepass'`

另外，可以自己添加 handler

####codecs.register_error(name, error_handler)
Register the error handling function error_handler under the name name. The error_handler argument will be called during encoding and decoding in case of an error, when name is specified as the errors parameter.

For encoding, error_handler will be called with a `UnicodeEncodeError` instance, which contains information about the location of the error. **The error handler must either raise this or a different exception, or return a tuple with a replacement for the unencodable part of the input and a position where encoding should continue.** The replacement may be either `str` or `bytes`. If the replacement is bytes, the encoder will simply copy them into the output buffer. If the replacement is a string, the encoder will encode the replacement. Encoding continues on original input at the specified position. Negative position values will be treated as being relative to the end of the input string. If the resulting position is out of bound an `IndexError` will be raised.

####codecs.lookup_error(name)
Return the error handler previously registered under the name name.

###7.2.1.2. Stateless Encoding and Decoding
The base `Codec` class defines these methods which also define the function interfaces of the stateless encoder and decoder:

####Codec.encode(input[, errors])
Encodes the object input and returns a tuple (output object, length consumed). For instance, text encoding converts a string object to a bytes object using a particular character set encoding (e.g., cp1252 or iso-8859-1).

The method may not store state in the Codec instance. Use `StreamWriter` for codecs which have to keep state in order to make encoding efficient.

The encoder must be able to handle zero length input and return an empty object of the output object type in this situation.

####Codec.decode(input[, errors])
Decodes the object input and returns a tuple (output object, length consumed). For instance, for a text encoding, decoding converts a bytes object encoded using a particular character set encoding to a string object.

For text encodings and bytes-to-bytes codecs, input must be a bytes object or one which provides the read-only buffer interface – for example, buffer objects and memory mapped files.

###7.2.1.3. Incremental Encoding and Decoding
Encoding/decoding the input isn’t done with one call to the stateless encoder/decoder function, but with multiple calls to the `encode()`/`decode()` method of the incremental encoder/decoder.

####class codecs.IncrementalEncoder(errors='strict')
All incremental encoders must provide this constructor interface. They are free to add additional keyword arguments, but only the ones defined here are used by the Python codec registry.

######encode(object[, final])
Encodes object (taking the current state of the encoder into account) and returns the resulting encoded object. If this is the last call to `encode()` final must be true (the default is false).

######reset()
Reset the encoder to the initial state. The output is discarded: call .`encode(object, final=True)`, passing an empty byte or text string if necessary, to reset the encoder and to get the output.

####IncrementalEncoder.getstate()
Return the current state of the encoder which must be an integer. The implementation should make sure that 0 is the most common state. 

####IncrementalEncoder.setstate(state)
Set the state of the encoder to state. state must be an encoder state returned by `getstate()`.


###7.2.1.4. Stream Encoding and Decoding
[Stream Encoding and Decoding](https://docs.python.org/3.5/library/codecs.html#stream-encoding-and-decoding)


##7.2.2. Encodings and Unicode

##7.2.3. Standard Encodings