Placeholders
============


Simple placeholders
-------------------


Let's add a few $placeholders to our template:

::

    >>> from Cheetah.Template import Template
    >>> values = {'what': 'surreal', 'punctuation': '?'}
    >>> t = Template("""\
    ... Hello, $what world$punctuation
    ... One of Python's least-used functions is $xrange.
    ... """, [values])
    >>> print t
    Hello, surreal world?
    One of Python's least-used functions is <built-in function xrange>.

    >>> print t.generatedModuleCode()
      1 #!/usr/bin/env python

      2 """
      3 Autogenerated by CHEETAH: The Python-Powered Template Engine
      4  CHEETAH VERSION: 0.9.12
      5  Generation time: Sun Apr 21 00:53:01 2002
      6 """

      7 __CHEETAH_genTime__ = 'Sun Apr 21 00:53:01 2002'
      8 __CHEETAH_version__ = '0.9.12'

      9 ##################################################
     10 ## DEPENDENCIES

     11 import sys
     12 import os
     13 import os.path
     14 from os.path import getmtime, exists
     15 import time
     16 import types
     17 from Cheetah.Template import Template
     18 from Cheetah.DummyTransaction import DummyTransaction
     19 from Cheetah.NameMapper import NotFound, valueForName,
               valueFromSearchList
     20 import Cheetah.Filters as Filters
     21 import Cheetah.ErrorCatchers as ErrorCatchers

     22 ##################################################
     23 ## MODULE CONSTANTS

     24 try:
     25     True, False
     26 except NameError:
     27     True, False = (1==1), (1==0)

     28 ##################################################
     29 ## CLASSES

     30 class GenTemplate(Template):
     31     """
     32
     33     Autogenerated by CHEETAH: The Python-Powered Template Engine
     34     """

     35     ##################################################
     36     ## GENERATED METHODS


::

     37     def __init__(self, *args, **KWs):
     38         """
     39
     40         """

     41         Template.__init__(self, *args, **KWs)

     42     def respond(self,
     43             trans=None,
     44             dummyTrans=False,
     45             VFS=valueFromSearchList,
     46             VFN=valueForName,
     47             getmtime=getmtime,
     48             currentTime=time.time):


     49         """
     50         This is the main method generated by Cheetah
     51         """

     52         if not trans:
     53             trans = DummyTransaction()
     54             dummyTrans = True
     55         write = trans.response().write
     56         SL = self._searchList
     57         filter = self._currentFilter
     58         globalSetVars = self._globalSetVars
     59
     60         ########################################
     61         ## START - generated method body
     62
     63         write('Hello, ')
     64         write(filter(VFS(SL,"what",1))) # generated from '$what' at
                                                    # line 1, col 8.
     65         write(' world')
     66         write(filter(VFS(SL,"punctuation",1))) # generated from
                                         # '$punctuation' at line 1, col 19.
     67         write("\nOne of Python's least-used methods is ")
     68         write(filter(xrange)) # generated from '$xrange' at line 2,
                                          # col 39.
     69         write('.\n')
     70
     71         ########################################
     72         ## END - generated method body
     73
     74         if dummyTrans:
     75             return trans.response().getvalue()
     76         else:
     77             return ""

::

     78
     79     ##################################################
     80     ## GENERATED ATTRIBUTES

     81     __str__ = respond
     82     _mainCheetahMethod_for_GenTemplate= 'respond'

     83 # CHEETAH was developed by Tavis Rudd, Chuck Esterbrook, Ian Bicking
            # and Mike Orr;
     84 # with code, advice and input from many other volunteers.
     85 # For more information visit https://cheetahtemplate.org/

     86 ##################################################
     87 ## if run from command line:
     88 if __name__ == '__main__':
     89     GenTemplate().runAsMainProgram()


(Again, I have added line numbers and split the lines as in the
previous chapter.)

This generated template module is different from the previous one
in several trivial respects and one important respect. Trivially,
{.\_filePath} and {.\_fileMtime} are not updated in
{.\_\_init\_\_}, so they inherit the value {None} from {Template}.
Also, that if-stanza in {.respond} that recompiles the template if
the source file changes is missing - because there is no source
file. So this module is several lines shorter than the other one.

But the important way this module is different is that instead of
the one {write} call outputting a string literal, this module has a
series of {write} calls (lines 63-69) outputting successive chunks
of the template. Regular text has been translated into a string
literal, and placeholders into function calls. Every placeholder is
wrapped inside a {filter} call to apply the current output filter.
(The default output filter converts all objects to strings, and
{None} to {""}.)

Placeholders referring to a Python builtin like {xrange} (line 68)
generate a bare variable name. Placeholders to be looked up in the
searchList have a nested function call; e.g.,

::

    write(filter(VFS(SL,"what",1))) # generated from '$what' at line 1, col 8.

{VFS}, remember, is a function imported from {Cheetah.NameMapper}
that looks up a value in a searchList. So we pass it the
searchList, the name to look up, and a boolean (1) indicating we
want autocalling. (It's {1} rather than {True} because it's
generated from an {and} expression, and that's what Python 2.2
outputs for true {and} expressions.)

Complex placeholders
--------------------


Placeholders can get far more complicated than that. This example
shows what kind of code the various NameMapper features produce.
The formulas are taken from Cheetah's test suite, in the
{Cheetah.Tests.SyntaxAndOutput.Placeholders} class.

::

    1 placeholder: $aStr
    2 placeholders: $aStr $anInt
    2 placeholders, back-to-back: $aStr$anInt
    1 placeholder enclosed in {}: ${aStr}
    1 escaped placeholder: \$var
    func placeholder - with (): $aFunc()
    func placeholder - with (int): $aFunc(1234)
    func placeholder - with (string): $aFunc('aoeu')
    func placeholder - with ('''\nstring'\n'''): $aFunc('''\naoeu'\n''')
    func placeholder - with (string*int): $aFunc('aoeu'*2)
    func placeholder - with (int*float): $aFunc(2*2.0)
    Python builtin values: $None $True $False
    func placeholder - with ($arg=float): $aFunc($arg=4.0)
    deeply nested argstring: $aFunc(  $arg = $aMeth( $arg = $aFunc( 1 ) ) ):
    function with None: $aFunc(None)
    autocalling: $aFunc! $aFunc().
    nested autocalling: $aFunc($aFunc).
    list subscription: $aList[0]
    list slicing: $aList[:2]
    list slicing and subcription combined: $aList[:2][0]
    dict - NameMapper style: $aDict.one
    dict - Python style: $aDict['one']
    dict combined with autocalled string method: $aDict.one.upper
    dict combined with string method: $aDict.one.upper()
    nested dict - NameMapper style: $aDict.nestedDict.two
    nested dict - Python style: $aDict['nestedDict']['two']
    nested dict - alternating style: $aDict['nestedDict'].two
    nested dict - NameMapper style + method: $aDict.nestedDict.two.upper
    nested dict - alternating style + method: $aDict['nestedDict'].two.upper
    nested dict - NameMapper style + method + slice: $aDict.nestedDict.two.upper[:4]
    nested dict - Python style, variable key: $aDict[$anObj.meth('nestedDict')].two
    object method: $anObj.meth1
    object method + complex slice: $anObj.meth1[0: ((4/4*2)*2)/$anObj.meth1(2) ]
    very complex slice: $( anObj.meth1[0: ((4/4*2)*2)/$anObj.meth1(2) ] )
    $_('a call to gettext')

We'll need a big program to set up the placeholder values. Here it
is:

::

    #!/usr/bin/env python
    from ComplexExample import ComplexExample

    try:   # Python >= 2.2.1
        True, False
    except NameError:  # Older Python
        True, False = (1==1), (1==0)

    class DummyClass:
        _called = False
        def __str__(self):
            return 'object'

        def meth(self, arg="arff"):
            return str(arg)

        def meth1(self, arg="doo"):
            return arg

        def meth2(self, arg1="a1", arg2="a2"):
            return str(arg1) + str(arg2)

        def callIt(self, arg=1234):
            self._called = True
            self._callArg = arg

    def dummyFunc(arg="Scooby"):
        return arg

    defaultTestNameSpace = {
        'aStr':'blarg',
        'anInt':1,
        'aFloat':1.5,
        'aList': ['item0','item1','item2'],
        'aDict': {'one':'item1',
                  'two':'item2',
                  'nestedDict':{1:'nestedItem1',
                              'two':'nestedItem2'
                              },
                  'nestedFunc':dummyFunc,
                  },
        'aFunc': dummyFunc,
        'anObj': DummyClass(),
        'aMeth': DummyClass().meth1,
        '_': lambda x: 'translated ' + x
    }

    print ComplexExample( searchList=[defaultTestNameSpace] )

Here's the output:

::

    1 placeholder: blarg
    2 placeholders: blarg 1
    2 placeholders, back-to-back: blarg1
    1 placeholder enclosed in {}: blarg
    1 escaped placeholder: $var
    func placeholder - with (): Scooby
    func placeholder - with (int): 1234
    func placeholder - with (string): aoeu
    func placeholder - with ('''\nstring'\n'''):
    aoeu'

    func placeholder - with (string*int): aoeuaoeu
    func placeholder - with (int*float): 4.0
    Python builtin values:  1 0
    func placeholder - with ($arg=float): 4.0
    deeply nested argstring: 1:
    function with None:
    autocalling: Scooby! Scooby.
    nested autocalling: Scooby.
    list subscription: item0
    list slicing: ['item0', 'item1']
    list slicing and subcription combined: item0
    dict - NameMapper style: item1
    dict - Python style: item1
    dict combined with autocalled string method: ITEM1
    dict combined with string method: ITEM1
    nested dict - NameMapper style: nestedItem2
    nested dict - Python style: nestedItem2
    nested dict - alternating style: nestedItem2
    nested dict - NameMapper style + method: NESTEDITEM2
    nested dict - alternating style + method: NESTEDITEM2
    nested dict - NameMapper style + method + slice: NEST
    nested dict - Python style, variable key: nestedItem2
    object method: doo
    object method + complex slice: do
    very complex slice: do
    translated a call to gettext

And here - tada! - is the generated module. To save space, I've
included only the lines containing the {write} calls. The rest of
the module is the same as in the first example, chapter
pyModules.example. I've split some of the lines to make them fit on
the page.

::

     1  write('1 placeholder: ')
     2  write(filter(VFS(SL,"aStr",1))) # generated from '$aStr' at line 1, col 16.
     3  write('\n2 placeholders: ')
     4  write(filter(VFS(SL,"aStr",1))) # generated from '$aStr' at line 2, col 17.
     5  write(' ')
     6  write(filter(VFS(SL,"anInt",1)))
            # generated from '$anInt' at line 2, col 23.
     7  write('\n2 placeholders, back-to-back: ')
     8  write(filter(VFS(SL,"aStr",1))) # generated from '$aStr' at line 3, col 31.
     9  write(filter(VFS(SL,"anInt",1)))
            # generated from '$anInt' at line 3, col 36.
    10  write('\n1 placeholder enclosed in {}: ')
    11  write(filter(VFS(SL,"aStr",1))) # generated from '${aStr}' at line 4,
            # col 31.
    12  write('\n1 escaped placeholder: $var\nfunc placeholder - with (): ')
    13  write(filter(VFS(SL,"aFunc",0)())) # generated from '$aFunc()' at line 6,
            # col 29.
    14  write('\nfunc placeholder - with (int): ')
    15  write(filter(VFS(SL,"aFunc",0)(1234))) # generated from '$aFunc(1234)' at
            # line 7, col 32.
    16  write('\nfunc placeholder - with (string): ')
    17  write(filter(VFS(SL,"aFunc",0)('aoeu'))) # generated from "$aFunc('aoeu')"
            # at line 8, col 35.
    18  write("\nfunc placeholder - with ('''\\nstring'\\n'''): ")
    19  write(filter(VFS(SL,"aFunc",0)('''\naoeu'\n'''))) # generated from
            # "$aFunc('''\\naoeu'\\n''')" at line 9, col 46.
    20  write('\nfunc placeholder - with (string*int): ')
    21  write(filter(VFS(SL,"aFunc",0)('aoeu'*2))) # generated from
            # "$aFunc('aoeu'*2)" at line 10, col 39.
    22  write('\nfunc placeholder - with (int*float): ')
    23  write(filter(VFS(SL,"aFunc",0)(2*2.0))) # generated from '$aFunc(2*2.0)'
            # at line 11, col 38.
    24  write('\nPython builtin values: ')
    25  write(filter(None)) # generated from '$None' at line 12, col 24.
    26  write(' ')
    27  write(filter(True)) # generated from '$True' at line 12, col 30.
    28  write(' ')
    29  write(filter(False)) # generated from '$False' at line 12, col 36.
    30  write('\nfunc placeholder - with ($arg=float): ')
    31  write(filter(VFS(SL,"aFunc",0)(arg=4.0))) # generated from
            # '$aFunc($arg=4.0)' at line 13, col 40.
    32  write('\ndeeply nested argstring: ')
    33  write(filter(VFS(SL,"aFunc",0)(
            arg = VFS(SL,"aMeth",0)( arg = VFS(SL,"aFunc",0)( 1 ) ) )))
        # generated from '$aFunc(  $arg = $aMeth( $arg = $aFunc( 1 ) ) )'
        # at line 14, col 26.
    34  write(':\nfunction with None: ')
    35  write(filter(VFS(SL,"aFunc",0)(None))) # generated from '$aFunc(None)' at
            # line 15, col 21.
    36  write('\nautocalling: ')
    37  write(filter(VFS(SL,"aFunc",1))) # generated from '$aFunc' at line 16,
            # col 14.
    38  write('! ')
    39  write(filter(VFS(SL,"aFunc",0)())) # generated from '$aFunc()' at line 16,
            # col 22.

::

    40  write('.\nnested autocalling: ')
    41  write(filter(VFS(SL,"aFunc",0)(VFS(SL,"aFunc",1)))) # generated from
            # '$aFunc($aFunc)' at line 17, col 21.
    42  write('.\nlist subscription: ')
    43  write(filter(VFS(SL,"aList",1)[0])) # generated from '$aList[0]' at line
            # 18, col 20.
    44  write('\nlist slicing: ')
    45  write(filter(VFS(SL,"aList",1)[:2])) # generated from '$aList[:2]' at
            # line 19, col 15.
    46  write('\nlist slicing and subcription combined: ')
    47  write(filter(VFS(SL,"aList",1)[:2][0])) # generated from '$aList[:2][0]'
            # at line 20, col 40.
    48  write('\ndict - NameMapper style: ')
    49  write(filter(VFS(SL,"aDict.one",1))) # generated from '$aDict.one' at line
            # 21, col 26.
    50  write('\ndict - Python style: ')
    51  write(filter(VFS(SL,"aDict",1)['one'])) # generated from "$aDict['one']"
            # at line 22, col 22.
    52  write('\ndict combined with autocalled string method: ')
    53  write(filter(VFS(SL,"aDict.one.upper",1))) # generated from
            # '$aDict.one.upper' at line 23, col 46.
    54  write('\ndict combined with string method: ')
    55  write(filter(VFN(VFS(SL,"aDict.one",1),"upper",0)())) # generated from
            # '$aDict.one.upper()' at line 24, col 35.
    56  write('\nnested dict - NameMapper style: ')
    57  write(filter(VFS(SL,"aDict.nestedDict.two",1))) # generated from
            # '$aDict.nestedDict.two' at line 25, col 33.
    58  write('\nnested dict - Python style: ')
    59  write(filter(VFS(SL,"aDict",1)['nestedDict']['two'])) # generated from
            # "$aDict['nestedDict']['two']" at line 26, col 29.
    60  write('\nnested dict - alternating style: ')
    61  write(filter(VFN(VFS(SL,"aDict",1)['nestedDict'],"two",1))) # generated
            # from "$aDict['nestedDict'].two" at line 27, col 34.
    62  write('\nnested dict - NameMapper style + method: ')
    63  write(filter(VFS(SL,"aDict.nestedDict.two.upper",1))) # generated from
            # '$aDict.nestedDict.two.upper' at line 28, col 42.
    64  write('\nnested dict - alternating style + method: ')
    65  write(filter(VFN(VFS(SL,"aDict",1)['nestedDict'],"two.upper",1)))
            # generated from "$aDict['nestedDict'].two.upper" at line 29, col 43.
    66  write('\nnested dict - NameMapper style + method + slice: ')

::

    67  write(filter(VFN(VFS(SL,"aDict.nestedDict.two",1),"upper",1)[:4]))
            # generated from '$aDict.nestedDict.two.upper[:4]' at line 30, col 50.
    68  write('\nnested dict - Python style, variable key: ')
    69  write(filter(VFN(VFS(SL,"aDict",1)
            [VFN(VFS(SL,"anObj",1),"meth",0)('nestedDict')],"two",1)))
        # generated from "$aDict[$anObj.meth('nestedDict')].two" at line 31,
        # col 43.
    70  write('\nobject method: ')
    71  write(filter(VFS(SL,"anObj.meth1",1))) # generated from '$anObj.meth1' at
            # line 32, col 16.
    72  write('\nobject method + complex slice: ')
    73  write(filter(VFN(VFS(SL,"anObj",1),"meth1",1)
            [0: ((4/4*2)*2)/VFN(VFS(SL,"anObj",1),"meth1",0)(2) ]))
        # generated from '$anObj.meth1[0: ((4/4*2)*2)/$anObj.meth1(2) ]'
        # at line 33, col 32.
    74  write('\nvery complex slice: ')
    75  write(filter(VFN(VFS(SL,"anObj",1),"meth1",1)
            [0: ((4/4*2)*2)/VFN(VFS(SL,"anObj",1),"meth1",0)(2) ] ))
        # generated from '$( anObj.meth1[0: ((4/4*2)*2)/$anObj.meth1(2) ] )'
        # at line 34, col 21.
    76  if False:
    77      _('foo')
    78  write(filter(VFS(SL,"_",0)("a call to gettext")))
            # generated from "$_('a call to gettext')"
            # at line 35, col 1.
    79  write('\n')

For each placeholder lookup, the the innermost level of nesting is
a {VFS} call, which looks up the first (leftmost) placeholder
component in the searchList. This is wrapped by zero or more {VFN}
calls, which perform Universal Dotted Notation lookup on the next
dotted component of the placeholder, looking for an attribute or
key by that name within the previous object (not in the
searchList). Autocalling is performed by {VFS} and {VFN}: that's
the reason for their third argument.

Explicit function/method arguments, subscripts and keys (which are
all expressions) are left unchanged, besides expanding any embedded
$placeholders in them. This means they must result in valid Python
expressions, following the standard Python quoting rules.

Built-in Python values ({None}, {True} and {False}) are converted
to {filter(None)}, etc. They use normal Python variable lookup
rather than {VFS}. (Cheetah emulates {True} and {False} using
global variables for Python < 2.2.1, when they weren't builtins
yet.)

Notice the last line is a call to {\_} (i.e. {gettext}) which is
used for internationalization (see
http://docs.python.org/lib/module-gettext.html). The code is
converted normally, but an {if False} block is used so that gettext
can successfully mark the string for translation when parsing the
generated Python. Otherwise, the NameMapper syntax would get in the
way of this.


