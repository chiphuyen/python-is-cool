# python-cool-features

A gentle guide to Python features that I didn't know exist or was too afraid to use. Will be updated as I learn more and become less lazy.

This use `python >= 3.6`.

GitHub has problem rendering Jupyter notebook so I copied the content here. I still keep the notebook in case you want to clone and run it on your machine.

## 1. Lambda, map, filter, reduce
The lambda keyword is used to create inline functions. The functions`square_fn` and `square_ld` below are identical.

```
def square_fn(x):
    return x * x

square_ld = lambda x : x * x

for i in range(10):
    assert square_fn(i) == square_ld(i)
```

Its quick declaration makes `lambda` function ideal for using in callbacks, functions that are passed as arguments to other functions. They are especially useful when used in junction with functions like `map`, `filter`, and `reduce`.

`map(fn, iterable)` applies the `fn` to all elements of the `iterable` (e.g. list, set, dictionary, tuple, string) and returns a map object.

```
nums = [1/3, 333/7, 2323/2230, 40/34, 2/3]
nums_squared = [num * num for num in nums]
print(nums_squared)

==> [0.1111111, 2263.04081632, 1.085147, 1.384083, 0.44444444]
```

This is the same as calling using `map` with a callback function.

```
nums_squared_1 = map(square_fn, nums)
nums_squared_2 = map(lambda x : x * x, nums)
print(list(nums_squared_1))

==> [0.1111111, 2263.04081632, 1.085147, 1.384083, 0.44444444]
```

You can also use map with more than one iterables. For example, if you want to calculate the mean squared error of a simple linear function `f(x) = ax + b` with the true label `labels`, these two methods are equivalent.

```
a, b = 3, -0.5
xs = [2, 3, 4, 5]
labels = [6.4, 8.9, 10.9, 15.3]

# Method 1: using a loop
errors = []
for i, x in enumerate(xs):
    errors.append((a * x + b - labels[i]) ** 2)
result1 = sum(errors) ** 0.5 / len(xs)

# Method 2: using map
diffs = map(lambda x, y: (a * x + b - y) ** 2, xs, labels)
result2 = sum(diffs) ** 0.5 / len(xs)

print(result1, result2)

==> 0.35089172119045514 0.35089172119045514
```

Note that objects returned by `map` and `filter` are iterators, which means that their values aren't stored but generated as needed. After you've called `sum(diffs)`, `diffs` becomes empty. If you want to keep all elements in `diffs`, convert it to a list using `list(diffs)`.

`filter(fn, iterable)` works the same way as `map`, except that `fn` returns a boolean value and `filter` returns all the elements of the `iterable` for which the `fn` returns True.

```
bad_preds = filter(lambda x: x > 0.5, errors)
print(list(bad_preds))

==> [0.8100000000000006, 0.6400000000000011]
```

`reduce(fn, iterable, initializer)` is used when we want to iteratively apply an operator to all elements in a list. For example, if we want to calculate the product of all elements in a list:

```
product = 1
for num in nums:
    product *= num
print(product)

==> 12.95564683272412
```

This is equivalent to:
```
from functools import reduce
product = reduce(lambda x, y: x * y, nums)
print(product)

==> 12.95564683272412
```
## 2. List manipulation
Python lists are super cool.

### 2.1 Unpacking
We can unpack a list by each element like this:
```
elems = [1, 2, 3, 4]
a, b, c, d = elems
print(a, b, c, d)

==> 1 2 3 4
```

We can also unpack a list like this:

```
a, *new_elems, d = elems
print(a)
print(new_elems)
print(d)

==> 1
	[2, 3]
	4
```

### 2.2 Slicing
We know that we can reverse a list using `[::-1]`.

```
elems = list(range(10))
print(elems)

==> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

print(elems[::-1])

[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```
The syntax `[x:y:z]` means "take every zth element of a list from index x to index y". When z is negative, it means going backwards. When x isn't specified, it's default to the first element of the list in the direction you traverse the list. When y isn't specified, it's default to the last element of the list. So if we want to take every 2th element of a list, we use `[::2]`.

```
evens = elems[::2]
print(evens)

reversed_evens = elems[-2::-2]
print(reversed_evens)

==> [0, 2, 4, 6, 8]
	[8, 6, 4, 2, 0]
```

We can also use slicing to delete all the even numbers in the list.

```
del elems[::2]
print(elems)

==> [1, 3, 5, 7, 9]
```

### 2.3 Insertion
We can change the value of an element in a list to another value.

```
elems = list(range(10))
elems[1] = 10
print(elems)

==> [0, 10, 2, 3, 4, 5, 6, 7, 8, 9]
```

If we want to replace the element at an index with multiple elements, e.g. replace the value `1` with 3 values `20, 30, 40`:

```
elems = list(range(10))
elems[1:2] = [20, 30, 40]
print(elems)

==> [0, 20, 30, 40, 2, 3, 4, 5, 6, 7, 8, 9]
```

If we want to insert 3 values `0.2, 0.3, 0.5` between element at index 0 and element at index 1:

```
elems = list(range(10))
elems[1:1] = [0.2, 0.3, 0.5]
print(elems)

==> [0, 0.2, 0.3, 0.5, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 2.4 Flattening
We can flatten a list of lists using `sum`.

```
list_of_lists = [[1], [2, 3], [4, 5, 6]]
sum(list_of_lists, [])

==> [1, 2, 3, 4, 5, 6]
```

If we have nested lists, we can recursively flatten it. That's another beauty of lambda functions -- we can use it in the same line as its creation.

```
nested_lists = [[1, 2], [[3, 4], [5, 6], [[7, 8], [9, 10], [[11, [12, 13]]]]]]
flatten = lambda x: [y for l in x for y in flatten(l)] if type(x) is list else [x]
flatten(nested_lists)

# This line of code is from
# https://github.com/sahands/python-by-example/blob/master/python-by-example.rst#flattening-lists
```

### 2.5 List vs generator
To illustrate the difference between a list and a generator, let's look at an example of creating n-grams out of a list of tokens.

One way to create n-grams is to use a sliding window.

```
tokens = ['i', 'want', 'to', 'go', 'to', 'school']

def ngrams(tokens, n):
    length = len(tokens)
    grams = []
    for i in range(length - n + 1):
        grams.append(tokens[i:i+n])
    return grams

print(ngrams(tokens, 3))

==> [['i', 'want', 'to'],
	 ['want', 'to', 'go'],
	 ['to', 'go', 'to'],
	 ['go', 'to', 'school']]
```

In the above example, we have to store all the n-grams at the same time. If the text has m tokens, then the memory requirement is `O(nm)`, which can be problematic when m is large.

Instead of using a list to store all n-grams, we can use a generator that generates the next n-gram when it's asked. This is known as lazy evaluation. We can make the function `ngrams` returns a generator using the keyword `yield`. Then the memory requirement is `O(n)`.

```
def ngrams(tokens, n):
    length = len(tokens)
    for i in range(length - n + 1):
        yield tokens[i:i+n]

ngrams_generator = ngrams(tokens, 3)
print(ngrams_generator)

==> <generator object ngrams at 0x1069b26d0>

for ngram in ngrams_generator:
    print(ngram)

==> ['i', 'want', 'to']
	['want', 'to', 'go']
	['to', 'go', 'to']
	['go', 'to', 'school']
```

Another way is generate n-grams is to use slices to create lists: `[0, 1, ..., -n]`, `[1, 2, ..., -n+1]`, ..., `[n-1, n, ..., -1]`, and then `zip` them together.

```
def ngrams(tokens, n):
    length = len(tokens)
    slices = (tokens[i:length-n+i+1] for i in range(n))
    return zip(*slices)

ngrams_generator = ngrams(tokens, 3)
print(ngrams_generator)

==> <zip object at 0x1069a7dc8> # zip objects are generators

for ngram in ngrams_generator:
    print(ngram)

==> ('i', 'want', 'to')
	('want', 'to', 'go')
	('to', 'go', 'to')
	('go', 'to', 'school')
```

Note that to create slices, we use `(tokens[...] for i in range(n))` instead of `[tokens[...] for i in range(n)]`. `[]` is the normal list comprehension that returns a list. `()` returns a generator.

## 3. Classes and magic methods
In Python, magic methods are prefixed and suffixed with tbe double underscore `__`, also known as dunder. The most wellknown magic method is probably `__init__`.

```
class Node:
    """ A struct to denote the node of a binary tree.
    It contains a value and pointers to left and right children.
    """
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
```

When we try to print out a Node object, however, it's not very interpretable.

```
root = Node(5)
print(root) # <__main__.Node object at 0x1069c4518>
```

Ideally, when user prints out a node, we want to print out the node's value and the values of its children if it has children. To do so, we use the magic method `__repr__`, which must return a printable object, like string.

```
class Node:
    """ A struct to denote the node of a binary tree.
    It contains a value and pointers to left and right children.
    """
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
        
    def __repr__(self):    
        strings = [f'value: {self.value}']
        strings.append(f'left: {self.left.value}' if self.left else 'left: None')
        strings.append(f'right: {self.right.value}' if self.right else 'right: None')
        return ', '.join(strings)

left = Node(4)
root = Node(5, left)
print(root) # value: 5, left: 4, right: None
```

We'd also like to compare two nodes by comparing their values. To do so, we overload the operator `==` with `__eq__`, `<` with `__lt__`, and `>=` with `__ge__`.

```
class Node:
    """ A struct to denote the node of a binary tree.
    It contains a value and pointers to left and right children.
    """
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
    
    def __eq__(self, other):
        return self.value == other.value
    
    def __lt__(self, other):
        return self.value < other.value
    
    def __ge__(self, other):
        return self.value >= other.value


left = Node(4)
root = Node(5, left)
print(left == root) # False
print(left < root) # True
print(left >= root) # False
```

For a comprehensive list of supported magic methods [here](https://www.tutorialsteacher.com/python/magic-methods-in-python) or see the official Python documentation [here](https://docs.python.org/3/reference/datamodel.html#special-method-names) (slightly harder to read).

Some of the methods that I highly recommend:

- `__len__`: to overload the `len()` function.
- `__str__`: to overload the `str()` function.
- `__iter__`: if you want to your objects to be iterators. This also allows you to call `next()` on your object.

For classes like Node where we know for sure all the attributes they can support (in the case of Node, they are `value`, `left`, and `right`), we might want to use `__slots__` to denote those values for both performance boost and memory saving. For a comprehensive understanding of pros and cons of `__slots__`, see this [absolutely amazing answer by Aaron Hall on StackOverflow](https://stackoverflow.com/a/28059785/5029595).

```
class Node:
    """ A struct to denote the node of a binary tree.
    It contains a value and pointers to left and right children.
    """
    __slots__ = ('value', 'left', 'right')
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
```
## 4. local namespace, object's attributes
The `locals()` function returns a dictionary containing the variables defined in the local namespace.

```
class Model1:
    def __init__(self, hidden_size=100, num_layers=3, learning_rate=3e-4):
        print(locals())
        self.hidden_size = hidden_size
        self.num_layers = num_layers
        self.learning_rate = learning_rate

model1 = Model1()

==> {'learning_rate': 0.0003, 'num_layers': 3, 'hidden_size': 100, 'self': <__main__.Model1 object at 0x1069b1470>}
```

All attributes of an object is stored in its `__dict__`.
```
print(model1.__dict__)

==> {'hidden_size': 100, 'num_layers': 3, 'learning_rate': 0.0003}
```

Note that manually assigning each of the argument to an attribute can be quite tiring when the list of the arguments is large. To avoid this, we can directly assign the list of arguments to the object's `__dict__`.

```
class Model2:
    def __init__(self, hidden_size=100, num_layers=3, learning_rate=3e-4):
        params = locals()
        del params['self']
        self.__dict__ = params

model2 = Model2()
print(model2.__dict__)

==> {'learning_rate': 0.0003, 'num_layers': 3, 'hidden_size': 100}
```

This can be especially convenient when the object is initiated using the catch-all `**kwargs`, though the use of `**kwargs` should be reduced to the minimum.

```
class Model3:
    def __init__(self, **kwargs):
        self.__dict__ = kwargs

model3 = Model3(hidden_size=100, num_layers=3, learning_rate=3e-4)
print(model3.__dict__)

==> {'hidden_size': 100, 'num_layers': 3, 'learning_rate': 0.0003}
```

## 5. Wild import
Often, you run into this wild import `*` that looks something like this:

`file.py`
    
    from parts import *

This is irresponsible because it will import everything in module, even the imports of that module. For example, if `parts.py` looks like this:

`parts.py`

    import numpy
    import tensorflow
    
    class Encoder:
        ...
    
    class Decoder:
        ...
        
    class Loss:
        ...
    
    def helper(*args, **kwargs):
        ...
    
    def utils(*args, **kwargs):
        ...

Since `parts.py` doesn't have `__all__` specified, `file.py` will import Encoder, Decoder, Loss, utils, helper together with numpy and tensorflow.

If we intend that only Endoer, Decoder, and Loss are ever to be imported and used in another module, we should specify that in `parts.py` using the `__all__` keyword.

`parts.py`
    
    __all__ = ['Encoder', 'Decoder', 'Loss']
    import numpy
    import tensorflow
    
    class Encoder:
        ...

Now, if some user irresponsibly does a wild import with `parts`, they can only import Encoder, Decoder, Loss. Personally, I also find `__all__` helpful as it gives me an overview of the module.