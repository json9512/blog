---
layout: post
title:  "Python Code style"
date: 2021-01-03 14:53:17 +0900
tags: python
comments: true
---
애용하는 언어인 파이썬에 대해 좀 알아보고 복기하기 위한 포스트 

- 파이썬은 원래 가독성이 좋은 프로그래밍 언어
- 가독성을 살려서 코드를 작성하게끔 pythonic 한 가이드라인이 있음
- Python coding convention과 흔히 접하지 않는 것들에 대해 알아보자

[Python Code Styles](https://docs.python-guide.org/writing/style/) 를 보며 내 마음대로 의역한 것

# Content
1. [General Rules](#general-rules)
2. [Idioms](#idioms)

****

# General Rules
## 1. Explicit code

**Bad**
```python
def make_complex(*args):
    x, y = args
    return dict(**locals())
```

**Good**
```python
def make_complex(x, y):
    return {'x': x, 'y': y}
```

딱 보면 무슨 코드인지 알 수 있도록 (self-explanatory)

## 2. One statement per line

**Bad**
```python
print 'a'; print 'b'

if x == 1: print 'a'

if <complex comparison> and <complex comparison>:
    # do some'in
```

**Good**
```python
print 'a'
print 'b'

if x == 1: 
    print 'a'

condition1 = <complex comparison>
condition2 = <complex comparison>

if condition1 and condition2:
    # do some'in
```

## 3. Function arguments
4 가지 방법이 있음
1. positional arguments - `send(message, recipient) -> send("Hello", "God")`, `point(x, y) -> point(0, 35)` <br>function 구동에 무조건 필요한 arguments
2. keyword arguments - `send(message, to, cc=None) -> send("Hello", "God", "Mom") or send("Hello", "God")`<br>기본값을 지정한 argument; function 구동에 필수적이지 않은 argument (`cc`)  
3. arbitrary argument list - `send(message, *args) -> send("Hello", "God", "Mom", "Dad"` <br>`message` argument 이후에 하나의 tuple로 입력 arguments를 저장함; tuple에 저장되는 값의 type이 다 다를 때 사용하는 것이 유용함
4. arbitrary keyword argument dictionary - `send(message, **kwargs) -> send("Hello", to=God, cc=Mom)`

개발자의 판단에 따라 가장 적합한 방식으로 코드를 짜면 됨

## 4. Don't use the magical wand
파이썬은 customizable하다. 하지만 기본적으로 작동되는 방식을 바꾸게 되면 가독성/효율이 떨어진다.

정말 필요할 때가 아니면 하지 말자

## 5. We are all responsible users
파이썬은 다른 언어에 있는 `private` 변수나 클래스 같은 것들이 기본적으로 사용되지 않는다. 이는 Java와 같이 보호적인 언어들과 다르다. 

이유는 python 개발자는 모두 책임감이 있다고 믿기 때문이다. 개발자로서 자신이 사용하면 안되는 코드/특성은 사용을 하지 않는 것이 좋다.

하지만 `private` 특성을 만들지 못하거나 encapsulation이 불가능 하다는 뜻이 아니다. 

`__`를 특성/함수 앞에다가 붙이면 된다.

```python
class Foo:
    '''
        __test = "Ac"
    '''
    def __init__(self):
        self.__test = "Ac"

    def __print_me(self):
        print(self.__test)

# outside
b = Foo()
b.__print_me() # no attribute error
print(b.__test) # no attribute error
```
## 6. Returning Values
- 함수가 복잡할수록 여러 곳에서 return 을 할 때가 종종 있다. 하지만 가독성을 중요시하고 코드가 더 직관적이고자 한다면, 여러 곳에서 return을 하는 것은 좋지 않다 

- 보통 2가지의 경우에 함수에서 return을 한다
    1. 로직을 따라 정상처리가 됐을 때 
    2. 에러가 났을 때

- Refactoring을 위해 하나의 return 포인트를 유지 하는 것이 좋다

```python
def complex_function(a, b):
    if not a:
        return None # raising an exception might be better
    
    if not b: 
        return None # raising an exception might be better
    
    # Some complex code to compute x from a, b
    # Resist temptation to return when compute succeeds

    if not x:
        # some code to compute x as plan-b
    return x # single exit point for maintainability

```
****


# Idioms
- pythonic 한 코드를 작성하는 방법은 아래와 같다

## 1. Unpacking values

1. 리스트에서 아이템 추출 시 `enumerate()` 함수 사용
```python
    for index, item in enumerate(some_list):
        # do some'in with index and item
```
2. 변수끼리 swap 할 때
```python
    # common 
    temp = b
    b = a
    a = temp

    # pythonic
    a, b = b, a
```
3. Nested unpacking
```python
    a, (b, c) = 1, (2, 3)

    # python 3 (new feature)
    a, *rest = [1, 2, 3, 4]
    # a = 1, rest = [2, 3, 4]

    a, *mid, c = [1, 2, 3, 4]
    # a = 1, mid = [2, 3], c = 4
```

## 2. Creating an ignored variable
```python
filename = "awesomefile.txt"
basename, __, ext = filename.rpartition(".")

# basename = awesomefile, __ = ., ext = txt
# use __ to ignore vals
# using _ might conflict since it's an alias for 
# gettext(), and _ is used to store the last operation 
# at the interactive prompt
```

## 3. Creating lists
**A list of length - N**
```python
arr_four = [0] * 4
```

**A list of length - N of lists**
```python
arr_four_nested = [[] for __ in range(4)]
```

**Create a string from list**
```python
some_list = ["a", "b", "c"]
stringified = ''.join(some_list)
```

## 4. Searching items in iterables
```python
some_set = set(["i", "t", "e", "m"])
some_list = ["i", "t", "e", "m"]

cond1 = "t" in some_set
cond2 = "i" in some_set

# Set/Dict is a hash table
# Much faster than lists/tuples when searching a large dataset
# Sometimes using lists is better because creating the hash table requires more memory 
```

## 5. Zen of Python
```python
>>> import this
'''
The Zen of Python, by Tim Peters

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
'''
```
[With example on github](https://github.com/hblanks/zen-of-python-by-example/blob/master/pep20_by_example.pdf)

## 6. PEP 8 
- defacto code style for python. Available [here](https://pep8.org/)

## 7. Some Conventions
**1. Check if variable equals constant**
```python
# Bad
if attr == True:
    return "True!"

if attr == None:
    return "None!"

# Good
if attr:
    return "True"

if not attr:
    return "False"

if attr is None:
    return "None"
```

**2. Access a dictionary element**
```python
# Bad
d = {"hello": "there"}

# Don't use dict.has_key()
if d.has_key("hello"):
    print(d["hello"])
else:
    print("default_value")

# Good
d = {"hello": "there"}

print(d.get('hello', 'default_value')) # prints 'there'
print(d.get('somein', 'default_value')) # prints 'default_value'

# Or:
if 'hello' in d:
    print(d['hello'])
```

**3. Short ways to manipulate lists**
```python
# Bad - needlessly allocates a list of all (gpa, name) entries in memory
valedictorian = max([(student.gpa, student.name) for student in graduates])

# Good
valedictorian = max((student.gpa, student.name) for student in graduates)
```

```python
# Good
# Generator functions for more complex tasks
def make_batches(items, batch_size):
    '''
    >>> list(make_batches([1, 2, 3, 4, 5], batch_size=3))
    [[1, 2, 3], [4, 5]]

    >>> generator = make_batches([1, 2, 3, 4, 5], batch_size=3)
    >>> next(generator)
    [1, 2, 3]
    '''

    current_batch = []
    for item in items:
        current_batch.append(item)

        if len(current_batch) == batch_size:
            yield current_batch
            current_batch = []

    yield current_batch 
```
```python
# Bad - never use list comprehension for its side effects
[print(x) for x in some_list]

# Good
for x in some_list:
    print(x)
```

**4. Filtering a List**
```python
# Bad - Don't delete items when iterating through the list
a = [3, 4, 5]

for i in a:
    if i > 4:
        a.remove(i)

# Don't make multiple passes through the list
while i in a:
    a.remove(i)

# Good
# comprehensions create a new list object
filtered_values = [value for value in some_list if value > 4]

# generators don't create another list 
filtered_values = (value for value in some_list if value > 4)
```

**5. Modifying the values in a list**
```python
# Bad
a = [3, 4, 5]
b = a # a,b refer to same list object

for i in range(len(a)):
    a[i] += 3 # changing a will also change b

# Good - create a new list object
a = [3, 4, 5]
b = a

a = [i+3 for i in a] # does not change b
```

**6. Read from a file**
```python
# Bad
f = open("file.txt")
a = f.read()
print(a)
f.close()

# Good - with open automatically closes the file
with open("file.txt") as f:
    for line in f:
        print(line)
```

**7. Line Continuations**
```python
# Bad - using "\" might break if there is space afterwards"\ "
some_string = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa \
    aaaaaaaaaaaaaaaaaaaa"

# Good - open (, [, { will indicate the python interpreter to read until ), ], } closes
some_string = (
    "aaaaaaaaaaaaaaaaaaaaaaaaaaa"
    "aaaaaaaaaaaaaaaaaaaaaaaaaaaa"
    "aaaaaaaaaaaaaaaaaaaaaaaaaaa"
)
```
****