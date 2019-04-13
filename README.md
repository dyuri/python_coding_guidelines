# Python coding guidelines

Even if python 2 is (currently) the most common default version in many distributions, it isn't actively developed, and will not get even security updates after 2020, so I use python 3 for my projects.
By python 3 I mean the language version and not the CPython interpreter, for development I recommend to use it, but you can use other - more performance optimized, etc. - implementations like [PyPy](https://pypy.org/) (or Jython, IronPython or whatever).

## Style guide

### The Zen of Python ([PEP-20](https://www.python.org/dev/peps/pep-0020/))

> Beautiful is better than ugly.
> Explicit is better than implicit.
> Simple is better than complex.
> Complex is better than complicated.
> Flat is better than nested.
> Sparse is better than dense.
> Readability counts.
> Special cases aren't special enough to break the rules.
> Although practicality beats purity.
> Errors should never pass silently.
> Unless explicitly silenced.
> In the face of ambiguity, refuse the temptation to guess.
> There should be one-- and preferably only one --obvious way to do it.
> Although that way may not be obvious at first unless you're Dutch.
> Now is better than never.
> Although never is often better than *right* now.
> If the implementation is hard to explain, it's a bad idea.
> If the implementation is easy to explain, it may be a good idea.
> Namespaces are one honking great idea -- let's do more of those!

### PEP-8

Try to follow the rules of [PEP-8](https://www.python.org/dev/peps/pep-0008/). (At least read it once.)

There are tools to help you ([flake8](http://flake8.pycqa.org/)) and there might be some exceptions where I break PEP-8 (default line length is 88 instead of 79), but in general it's a good idea to follow the rules.

### One statement per line

**Bad:**
```python
print 'one'; print 'two'

if x == 1: print 'one'
```

**Good:**
```python
print 'one'
print 'two'

if x == 1:
    print 'one'
```