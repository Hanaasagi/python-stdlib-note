#[6.3. difflib — Helpers for computing deltas](https://docs.python.org/3.5/library/difflib.html)

This module provides classes and functions for comparing sequences. It can be used for example, for comparing files, and can produce difference information in various formats, including HTML and context and unified diffs. For comparing directories and files, see also, the `filecmp` module.


###class difflib.SequenceMatcher
This is a flexible class for comparing pairs of sequences of any type, **so long as the sequence elements are hashable**.

###class difflib.Differ
This is a class for comparing sequences of lines of text, and producing human-readable differences or deltas. Differ uses `SequenceMatcher` both to compare sequences of lines, and to compare sequences of characters within similar (near-matching) lines.

###class difflib.HtmlDiff
This class can be used to create an HTML table (or a complete HTML file containing the table) showing a side by side, line by line comparison of text with inter-line and intra-line change highlights. The table can be generated in either full or contextual difference mode.

####`__init__(tabsize=8, wrapcolumn=None, linejunk=None, charjunk=IS_CHARACTER_JUNK)`
Initializes instance of HtmlDiff.

tabsize is an optional keyword argument to specify tab stop spacing and defaults to 8.

wrapcolumn is an optional keyword to specify column number where lines are broken and wrapped, defaults to None where lines are not wrapped.

linejunk and charjunk are optional keyword arguments passed into ndiff() (used by HtmlDiff to generate the side by side HTML differences). See ndiff() documentation for argument default values and descriptions.

####make_file(fromlines, tolines, fromdesc='', todesc='', context=False, numlines=5, *, charset='utf-8')
Compares fromlines and tolines (lists of strings) and returns a string which is a complete HTML file containing a table showing line by line differences with inter-line and intra-line changes highlighted.


####make_table(fromlines, tolines, fromdesc='', todesc='', context=False, numlines=5)
Compares fromlines and tolines (lists of strings) and returns a string which is a complete HTML table showing line by line differences with inter-line and intra-line changes highlighted.

The arguments for this method are the same as those for the `make_file()` method.


###difflib.context_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')
Compare a and b (lists of strings); return a delta (a generator generating the delta lines) in context diff format.

Context diffs are a compact way of showing just the lines that have changed plus a few lines of context. The changes are shown in a before/after style. The number of context lines is set by n which defaults to three.

By default, the diff control lines (those with `*** or `---`) are created with a trailing newline. This is helpful so that inputs created from `io.IOBase.readlines()` result in diffs that are suitable for use with `io.IOBase.writelines()` since both the inputs and outputs have trailing newlines.

For inputs that do not have trailing newlines, set the lineterm argument to `""` so that the output will be uniformly newline free.

The context diff format normally has a header for filenames and modification times. Any or all of these may be specified using strings for fromfile, tofile, fromfiledate, and tofiledate. The modification times are normally expressed in the ISO 8601 format. If not specified, the strings default to blanks.

    >>> s1 = ['bacon\n', 'eggs\n', 'ham\n', 'guido\n']
    >>> s2 = ['python\n', 'eggy\n', 'hamster\n', 'guido\n']
    >>> sys.stdout.writelines(context_diff(s1, s2, fromfile='before.py', tofile='after.py'))
    *** before.py
    --- after.py
    ***************
    *** 1,4 ****
    ! bacon
    ! eggs
    ! ham
      guido
    --- 1,4 ----
    ! python
    ! eggy
    ! hamster
      guido

###difflib.get_close_matches(word, possibilities, n=3, cutoff=0.6)

查找最相近的字符串  

Return a list of the best “good enough” matches. word is a sequence for which close matches are desired (typically a string), and possibilities is a list of sequences against which to match word (typically a list of strings).

Optional argument n (default 3) is the maximum number of close matches to return; n must be greater than 0.

Optional argument cutoff (default 0.6) is a float in the range [0, 1]. Possibilities that don’t score at least that similar to word are ignored.

The best (no more than n) matches among the possibilities are returned in a list, sorted by similarity score, most similar first.

    >>> get_close_matches('appel', ['ape', 'apple', 'peach', 'puppy'])
    ['apple', 'ape']
    >>> import keyword
    >>> get_close_matches('wheel', keyword.kwlist)
    ['while']
    >>> get_close_matches('pineapple', keyword.kwlist)
    []
    >>> get_close_matches('accept', keyword.kwlist)
    ['except']

###difflib.ndiff(a, b, linejunk=None, charjunk=IS_CHARACTER_JUNK)
Compare a and b (lists of strings); return a Differ-style delta (a generator generating the delta lines).

Optional keyword parameters linejunk and charjunk are filtering functions (or `None`):

linejunk: A function that accepts a single string argument, and returns true if the string is junk, or false if not. The default is None. There is also a module-level function `IS_LINE_JUNK()`, which filters out lines without visible characters, except for at most one pound character ('#') – however the underlying SequenceMatcher class does a dynamic analysis of which lines are so frequent as to constitute noise, and this usually works better than using this function.

charjunk: A function that accepts a character (a string of length 1), and returns if the character is junk, or false if not. The default is module-level function `IS_CHARACTER_JUNK()`, which filters out whitespace characters (a blank or tab; it’s a bad idea to include newline in this!).

    >>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True),
    ...              'ore\ntree\nemu\n'.splitlines(keepends=True))
    >>> print(''.join(diff), end="")
    - one
    ?  ^
    + ore
    ?  ^
    - two
    - three
    ?  -
    + tree
    + emu


###difflib.restore(sequence, which)
Return one of the two sequences that generated a delta.

Given a sequence produced by `Differ.compare()` or `ndiff()`, extract lines originating from file 1 or 2 (parameter which), stripping off line prefixes.

Example:

    >>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True),
    ...              'ore\ntree\nemu\n'.splitlines(keepends=True))
    >>> diff = list(diff) # materialize the generated delta into a list
    >>> print(''.join(restore(diff, 1)), end="")
    one
    two
    three
    >>> print(''.join(restore(diff, 2)), end="")
    ore
    tree
    emu

###difflib.unified_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')
Compare a and b (lists of strings); return a delta (a generator generating the delta lines) in unified diff format.

Unified diffs are a compact way of showing just the lines that have changed plus a few lines of context. The changes are shown in an inline style (instead of separate before/after blocks). The number of context lines is set by n which defaults to three.

By default, the diff control lines (those with `---`, `+++`, or `@@`) are created with a trailing newline. This is helpful so that inputs created from `io.IOBase.readlines()` result in diffs that are suitable for use with `io.IOBase.writelines()` since both the inputs and outputs have trailing newlines.

For inputs that do not have trailing newlines, set the lineterm argument to `""` so that the output will be uniformly newline free.

The context diff format normally has a header for filenames and modification times. Any or all of these may be specified using strings for fromfile, tofile, fromfiledate, and tofiledate. The modification times are normally expressed in the ISO 8601 format. If not specified, the strings default to blanks.

    >>> s1 = ['bacon\n', 'eggs\n', 'ham\n', 'guido\n']
    >>> s2 = ['python\n', 'eggy\n', 'hamster\n', 'guido\n']
    >>> sys.stdout.writelines(unified_diff(s1, s2, fromfile='before.py', tofile='after.py'))
    --- before.py
    +++ after.py
    @@ -1,4 +1,4 @@
    -bacon
    -eggs
    -ham
    +python
    +eggy
    +hamster
     guido


###difflib.diff_bytes(dfunc, a, b, fromfile=b'', tofile=b'', fromfiledate=b'', tofiledate=b'', n=3, lineterm=b'\n')
Compare a and b (lists of bytes objects) using dfunc; yield a sequence of delta lines (also bytes) in the format returned by dfunc. dfunc must be a callable, typically either `unified_diff()` or `context_diff()`.

Allows you to compare data with unknown or inconsistent encoding. All inputs except n must be bytes objects, not str. Works by losslessly converting all inputs (except n) to str, and calling `dfunc(a, b, fromfile, tofile, fromfiledate, tofiledate, n, lineterm)`. The output of dfunc is then converted back to bytes, so the delta lines that you receive have the same unknown/inconsistent encodings as a and b.


###difflib.IS_LINE_JUNK(line)
Return true for ignorable lines. The line line is ignorable if line is blank or contains a single `'#'`, otherwise it is not ignorable. Used as a default for parameter linejunk in `ndiff()` in older versions.

###difflib.IS_CHARACTER_JUNK(ch)
Return true for ignorable characters. The character ch is ignorable if ch is a space or tab, otherwise it is not ignorable. Used as a default for parameter charjunk in `ndiff()`.


##6.3.1. SequenceMatcher Objects
###class difflib.SequenceMatcher(isjunk=None, a='', b='', autojunk=True)

Optional argument isjunk must be `None` (the default) or a one-argument function that takes a sequence element and returns true if and only if the element is “junk” and should be ignored. Passing `None` for isjunk is equivalent to passing `lambda x: 0`; in other words, no elements are ignored. For example, pass:

    lambda x: x in " \t"

if you’re comparing lines as sequences of characters, and don’t want to synch up on blanks or hard tabs.

The optional arguments a and b are sequences to be compared; both default to empty strings. The elements of both sequences must be hashable.

SequenceMatcher objects get three data attributes: bjunk is the set of elements of b for which isjunk is `True`; bpopular is the set of non-junk elements considered popular by the heuristic (if it is not disabled); b2j is a dict mapping the remaining elements of b to a list of positions where they occur. All three are reset whenever b is reset with `set_seqs()` or `set_seq2()`.

`SequenceMatcher` objects have the following methods:

####set_seqs(a, b)
Set the two sequences to be compared.

`SequenceMatcher` computes and caches detailed information about the second sequence, so if you want to compare one sequence against many sequences, use `set_seq2()` to set the commonly used sequence once and call `et_seq1()` repeatedly, once for each of the other sequences.

####set_seq1(a)
Set the first sequence to be compared. The second sequence to be compared is not changed.

####set_seq2(b)
Set the second sequence to be compared. The first sequence to be compared is not changed.

####find_longest_match(alo, ahi, blo, bhi)
Find longest matching block in `a[alo:ahi]` and `b[blo:bhi]`.

####get_matching_blocks()
Return list of triples describing matching subsequences. Each triple is of the form `(i, j, n)`, and means that `a[i:i+n] == b[j:j+n]`. The last triple is a dummy, and has the value `(len(a), len(b), 0)`.

    >>> s = SequenceMatcher(None, "abxcd", "abcd")
    >>> s.get_matching_blocks()
    [Match(a=0, b=0, size=2), Match(a=3, b=2, size=2), Match(a=5, b=4, size=0)]

####get_opcodes()
对比字符串，并给出如何修改的建议
Return list of 5-tuples describing how to turn a into b. Each tuple is of the form `(tag, i1, i2, j1, j2)`. The first tuple has `i1 == j1 == 0`, and remaining tuples have i1 equal to the i2 from the preceding tuple, and, likewise, j1 equal to the previous j2.

For example:

    >>> a = "qabxcd"
    >>> b = "abycdf"
    >>> s = SequenceMatcher(None, a, b)
    >>> for tag, i1, i2, j1, j2 in s.get_opcodes():
    ...     print('{:7}   a[{}:{}] --> b[{}:{}] {!r:>8} --> {!r}'.format(
    ...         tag, i1, i2, j1, j2, a[i1:i2], b[j1:j2]))
    delete    a[0:1] --> b[0:0]      'q' --> ''
    equal     a[1:3] --> b[0:2]     'ab' --> 'ab'
    replace   a[3:4] --> b[2:3]      'x' --> 'y'
    equal     a[4:6] --> b[3:5]     'cd' --> 'cd'
    insert    a[6:6] --> b[5:6]       '' --> 'f'

####get_grouped_opcodes(n=3)
Return a generator of groups with up to n lines of context.

Starting with the groups returned by `get_opcodes()`, this method splits out smaller change clusters and eliminates intervening ranges which have no changes.

The groups are returned in the same format as `get_opcodes()`.

####ratio()
计算相似率
Return a measure of the sequences’ similarity as a float in the range `[0, 1]`.

Where T is the total number of elements in both sequences, and M is the number of matches, this is 2.0*M / T. Note that this is 1.0 if the sequences are identical, and 0.0 if they have nothing in common.

**This is expensive to compute if `get_matching_blocks()` or g`et_opcodes()` hasn’t already been called, in which case you may want to try `quick_ratio()` or `real_quick_ratio()` first to get an upper bound.**

####quick_ratio()
Return an upper bound on `ratio()` relatively quickly.

####real_quick_ratio()
Return an upper bound on `ratio()` very quickly.

The three methods that return the ratio of matching to total characters can give different results due to differing levels of approximation, although `quick_ratio()` and `real_quick_ratio()` are always at least as large as `ratio()`:

    >>> s = SequenceMatcher(None, "abcd", "bcde")
    >>> s.ratio()
    0.75
    >>> s.quick_ratio()
    0.75
    >>> s.real_quick_ratio()
    1.0

##6.3.2. SequenceMatcher Examples

##6.3.3. Differ Objects
Note that Differ-generated deltas make no claim to be minimal diffs. To the contrary, minimal diffs are often counter-intuitive, because they synch up anywhere possible, sometimes accidental matches 100 pages apart. Restricting synch points to contiguous matches preserves some notion of locality, at the occasional cost of producing a longer diff.

The Differ class has this constructor:

###class difflib.Differ(linejunk=None, charjunk=None)
Optional keyword parameters linejunk and charjunk are for filter functions (or `None`):

linejunk: A function that accepts a single string argument, and returns true if the string is junk. The default is `None`, meaning that no line is considered junk.

charjunk: A function that accepts a single character argument (a string of length 1), and returns true if the character is junk. The default is `None`, meaning that no character is considered junk.
 
Differ objects are used (deltas generated) via a single method:

####compare(a, b)
Compare two sequences of lines, and generate the delta (a sequence of lines).

Each sequence must contain individual single-line strings ending with newlines. Such sequences can be obtained from the `readlines()` method of file-like objects. The delta generated also consists of newline-terminated strings, ready to be printed as-is via the `writelines()` method of a file-like object 

##6.3.4. Differ Example

##6.3.5. A command-line interface to difflib
