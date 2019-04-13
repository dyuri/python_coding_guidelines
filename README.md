# Python coding guidelines

Even if python 2 is (currently) the most common default version in many distributions, it isn't actively developed, and will not get even security updates after 2020, so I use python 3 for my projects.
By python 3 I mean the language version and not the CPython interpreter, for development I recommend to use it, but you can use other - more performance optimized, etc. - implementations like [PyPy](https://pypy.org/) (or Jython, IronPython or whatever).

## Style guide

### The Zen of Python ([PEP-20](https://www.python.org/dev/peps/pep-0020/))

```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

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

### Global variables

Avoid them.

### Generator expressions / comprehensions

Prefer them in simple cases over for loops.

**Good:**
```python
result = [mapping_fn(x) for x in iterable] #  JS: iterable.map(mapping_fn)

result = [x for x in iterable if filter_fn(x)] # JS: iterable.filter(filter_fn)

# JS: iterable.filter(filter_fn).map(mapping_fn) - if position is required as well
result = [mapping_fn(x, i) for x, i in zip(iterable, range(len(iterable)) if filter_fn(x, i)]

# but *
result = []
for x in range(10):
    for y in range(10):
        if x * y > 10:
            result.append((x, y))
```

**Bad:**
```python
# same as *, for loop is easier to understand
[(x, y) for x in range(10) for y in range(10) if x * y > 10]
```

### Generators

Use generators where you can to save memory or CPU.

**Bad:**
```python
def squares_list(iterable):
    squares = []
    for i in iterable:
        squares.append(i*i)
    return squares
```

**Good:**
```python
def squares_gen(iterable):
    for i in iterable:
        yield i*i
```

### Lambda functions

Okay for one-liners, like mapping/filtering/sorting, etc.

Check the [operator module](https://docs.python.org/3/library/operator.html) if they are already available. (i.e. use `operator.mul` instead of `lambda x, y: x*y`).

### Conditional expressions

```python
# JS: let x = cond ? 1 : 2;
x = 1 if cond else 2
```

### Default argument values

Use only immutable values for default arguments, mutable objects can introduce tricky side-effects - they are initialized only at function definition.

**Good:**
```python
def foo(a, b=None):
    if b is None:
        b = []
    b.append(1)
```

**Bad:**
```python
def foo(a, b=[]):
    b.append(1)  # 1 will be appended to the list every time the function is called, [1, 1, 1, ...]
```

### Properties

Use properties for simple getters/setters.

**Example:**
```python
import math

class Square():

    def __init__(self, side):
        self.side = side

    @property
    def area(self):
        return self.side * self.side

    @area.setter
    def area_set(self, area):
        self.side = math.sqrt(area)

    # OR
    @property
    def perimeter(self):
        self._get_perimeter()

    def _get_perimeter(self):
        return self.side * 4
```