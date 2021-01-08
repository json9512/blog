---
layout: post
title:  "Python Advanced Topic"
date: 2021-01-03 14:59:17 +0900
tags: python
comments: true
---
파이썬 개발자라면 알아야 할 고오급 정보들 !

아래 출처에서 부분적으로 의역한 포스트

출처: 
- [Real Python](https://realpython.com/)
- [Official Doc](https://docs.python.org/3.9/faq/design.html)
- [Stackify](https://stackify.com/python-garbage-collection/#:~:text=The%20Python%20garbage%20collector%20has,a%20threshold%20number%20of%20objects.)
- [Programiz](https://www.programiz.com/python-programming/closure)


**목차**

1. **[Memory Management](#memory-management)**
2. **[Global Interpreter Lock](#global-interpreter-lock)**
3. **[All is One, One is all](#all-is-one-one-is-all)**
4. **[Decorators](#decorators)**
5. **[Decorators 2](#decorators-2)**

****

# Memory Management

출처: [Real Python](https://realpython.com/python-memory-management/), [Stackify](https://stackify.com/python-garbage-collection/#:~:text=The%20Python%20garbage%20collector%20has,a%20threshold%20number%20of%20objects.)

컴퓨터에서 프로그램이 실행될 때, OS에서 프로그램을 위해 할당하는 메모리는 fixed하다. 프로그램은 정해진 메모리 관리 알고리즘에 따라 주어진 메모리를 관리한다.

**CPython**

파이썬은 CPython이 베이스이다. CPython은 C로 만들어졌다.

CPython 외에도 다른 interpreter/compiler는 다음과 같다:
- IronPython - .NET
- Jython - JAVA
- MicroPython - 마이크로 컨트롤러

기본적으로 CPython이 하는 일은 다음과 같다.
1. .py에 적힌 코드를 bytecode로 compile 한다
2. .pyc (컴파일 된 코드)를 파이썬 가상 환경에서 실행한다

**파이썬의 object (int, list, etc)는 어떻게 관리가 될까?**

파이썬에서는 모든 object가 `PyObject`로 표현된다. 

`PyObject`는 C struct 이고:
- ob_refcnt: 이 object를 가르키고 있는 변수 갯수
- ob_type: 이 object의 type (int, list, etc)

와 같은 특성을 가지고 있다

`PyObject`의 `ob_refcnt` 특성이 0이 될 때, 가비지 콜렉터는 `PyObject`가 차지하고 있던 메모리를 다시 free 시킬 수 있다. 

*주의: 만약 object가 자기 자신을 reference 한다면?*
```python
class Foo(object):
    pass

# 'Foo' will be allocated in some place in the memory
a = Foo() # ob_refcnt of Foo = 1

# This will increment the ob_refcnt
a.obj = a # ob_refcnt of Foo = 2

# Will not deallocate the memory occupied by 'a' 
# because the ob_refcnt is never 0
del a # ob_refcnt of Foo = 1

# this is a 'reference cycle'
```
위와 같이 reference cycle이 생길 경우, `Foo` 가 차지하고 있는 메모리는 절대 비워지지 않는다. 

파이썬에서는 이것을 해결하고자 Generation garbage collection 도 같이 사용한다.

**Generation Garbage Collection**

2 가지 특징이 있다:
1. 가비지 콜렉터가 메모리 내에 모든 `object`를 track 한다. 이후 collection process에서 살아남은 `object`들은 다음 generation으로 넘어간다. 파이썬의 garbage collection process의 generation은 총 3번이다.
2. 각 generation 마다 threshold 가 있다. 이 threshold는 가비지 콜렉터가 track하는 `object`의 최대 갯수이다. 만약이 threshold를 넘어서게 되면, collection process가 시작되고 살아남은 `object`만 다음 세대로 넘어간다. 이 threshold는 개발자가 직접 바꿀 수 있다. *Instagram은 실제로 이 기능을 해제하여 서버를 10%가량 더 효율적으로 만들었다.*

**Global Interpreter Lock (GIL)**

메모리는 공유제이다. 메모리를 보호하는 알고리즘이 없다면 여러 프로세스가 같은 메모리 값을 읽고, 쓰고, 지우고 할 수 있다. 즉, race condition과 같은 문제점들이 발현한다. 

이를 해결하는 방법은 여러가지인데 파이썬은 Global Interpreter Lock (GIL) 을 사용함으로써 메모리의 데이터를 보호한다.

파이썬의 GIL은 단일 Thread에게만 메모리 권한을 준다.

**CPython Memory Management**

![image](https://files.realpython.com/media/memory_management.92ad564ec680.png)

위 그림과 같이 OS가 할당한 메모리 중 Python은 메모리를 크게 Object를 위한 메모리와 그 외 메모리로 분할한다.

그리고 Object를 위한 메모리에 CPython의 object allocator가 할당된다.

이는 새 object 가 메모리에 할당되거나 삭제될 때마다 실행된다.

![image](https://files.realpython.com/media/memory_management_5.394b85976f34.png)

CPython 메모리 관리에는 크게 3 가지가 존재한다.

1. Arenas
    - 가장 큰 메모리 덩어리
    - OS가 메모리를 읽을 때 사용하는 블록과 연결된다     
    - Python은 Arena의 크기를 256 KB로 추정한다
    - eg. 256KB - 256KB - 256KB 단위로 OS가 읽는다
    - ~~사실 뭔 소린지 잘 모르겠다 하하~~

2. Pool
    - 가상 메모리
    - 4 KB
    - 더 작은 메모리 블록으로 이루어져 있다
    - Pool내에 모든 블록은 같은 "size class"다

3. Blocks
    - 8 bit 단위로 나뉘어진 "size class" 가 있다
    - eg. 데이터가 7 bytes 면 "size class"는 8 (bytes) 이다, 데이터가 10 bytes 면 "size class"는 16 (bytes) 이다.

**Pools**
- 같은 size class의 블록들로 구성된다
- 같은 size class를 담고 있는 pool들과 double-linked list를 형성한다
- 3 가지의 상태를 가진다:
    - `used` - 데이터를 저장할 블록을 가지고 있을 때
    - `full` - 모든 블록이 데이터로 가득 차 있을 때
    - `empty` - 데이터가 존재하지 않고 , 아무 size class로 구성된 블록들을 만들 수 있을 때
- `used`pools 은 새로운 객체가 저장 될 수 있는 block이 있는 모든 pool 리스트다 (저장되는 객체의 size는 block의 size class가 수용 가능 해야한다). 수용 가능한 블록을 찾을때 `used`pools 리스트에서 먼저 찾는다.
- `free`pools 는 모든 `empty` 상태의 pool을 기록한 리스트다.
- `full`에서 특정 블록을 free 할 경우, 그 pool의 상태는 `used`로 바뀐다. 

![image](https://files.realpython.com/media/memory_management_3.52bffbf302d3.png)

**Blocks**
- 3가지 상태가 있다:
    1. `untouched` - 할당되지 않은 메모리
    2. `free` - 할당 됐으나 CPython이 free 한 메모리
    3. `allocated` - 할당 된 메모리

- 위 그림과 같이, 각 pool 에는 자기 pool 내에 `free` 블록을 가르키는 포인터가 있다
- `free` 블록이 꼭 연속성을 가져야 하는 것은 아니다:

<br>

![image](https://files.realpython.com/media/memory_management_4.4a30dfa2d111.png)

-  `free` 블록을 가르키는 포인터는 `free` 블록들을 탐색할 수 있는 single linked list다
- 만약 필요한 메모리의 사이즈가 `free` 블록의 사이즈보다 클 경우, `untouched` 블록들을 사용한다

**Arena**

- 여러 개의 pool로 구성되어 있다
- double-linked 리스트다
- Arena내에 할당 가능한 pool의 갯수를 기준으로 정렬이 되어있다 (오름차순) 
- 오름차순인 이유?
    - CPython이 특정 메모리를 `free`한다는 것은 OS에게 그 메모리를 돌려준다는 뜻이 아니다. CPython은 메모리를 `free`한 이후에도 그 메모리를 계속 사용한다.
    <br>즉 Arena를 더 적게 사용하는 것이 전체적인 프로그램의 메모리를 더 적게 사용하는 것이다.

****

# Global Interpreter Lock
source: *[Real Python](https://realpython.com/python-gil/#:~:text=The%20Python%20Global%20Interpreter%20Lock%20or%20GIL%2C%20in%20simple%20words,at%20any%20point%20in%20time.)*

- 간단히 말하면 mutex (lock) 이다. 단 하나의 thread만 Python Interpreter를 쓸 수 있게 하는 장치다
- CPU를 이용한 multi threaded 앱에서는 bottleneck이 될 수 있다
- 아무리 내 코드가 multi threaded용으로 짜여졌다고 하더라도, 실제로는 single threaded 앱처럼 실행이 된다

**왜 도입 됐는가?**

- 일단 Python GIL은 thread라는 개념이 존재하기 전에 도입됐다
- 파이썬 메모리 관리 특성 상 object에 대한 race condition이 생길 수 있는데, 이를 방지 하려면 일종의 lock이 필요했다
- object 단위로 lock을 도입하기에는 deadlock 문제가 생기고 성능이 저하 될 수 있어서 GIL을 도입했다.
- 가장 간단하고 효과적이었기 때문이다

**Parallel program은 파이썬으로 불가능한가?**

- Multi-threaded 말고 multi-process 방법으로 접근하면 앱을 parallel하게 짤 수 있다
- 혹은 다른 python interpreter를 사용하면 된다. GIL은 CPython 에만 존재하기 때문이다 (CPython이 Python의 베이스다)

****

# All is One, One is all
> 전체는 하나, 하나는 전체

뜻하지 않게 생각난 불교의 무진연기다. 파이썬에서 모든 것은 객체다. 변수, 함수, 클래스 등등 다 객체다. 

`PyObject`라는 객체로 모든 것이 관리되기 때문에 이것을 이해하면 신기한 방법으로 코드를 짤 수 있다. 

몇 가지만 알아보자면:
1. First class function: 파이썬은 변수, 클래스뿐만 아니라 함수를 다른 함수의 리턴 값 혹은 인자 (parameter) 로 쓸 수 있다. 이는 함수가 객체이기 때문.
2. Closure function: 함수(A) 내의 함수(B)가 함수(A)가 가진 변수를 저장하고 사용하는 함수(B)

말로 표현하기 어렵다. 코드로 보자. 

#### **First class function**
```python
import sys

inputs = sys.stdin.readline
K, V = map(int, input().split(' '))
```

위 코드를 살펴보면 함수 `sys.stdin.readline`을 변수 `inputs`에 저장한 뒤, `map` 함수에서 `inputs()`로 실행 시키는 것을 볼 수 있다. 이런 방식으로 변수에 함수를 저장하는 것이 first class function의 특징이라고 볼 수 있다. 

또 다른 예를 들면, `map` 함수에 인자로 `int, input().split(' ')` 이 있는 것을 볼 수 있다. 여기서 `int` 인자는 사실 함수다. 

즉, 위의 코드를 실행하면 `sys.stdin.readline` 함수로 읽은 유저의 입력 값을 `' '`로 나누어서 array화 시킨 뒤 `map` 함수를 이용해 array안에 모든 element를 `int()` 화 시켜주는 코드다.

이런식으로 함수를 변수처럼 인자로도 주고, 변수에도 저장하고, 다른 함수에서 리턴 값으로도 주고 할 수 있는 것이 바로 first class function인 것이다.

이 모든 것이 가능한 이유는? 

파이썬에서 모든 것은 객체기 때문이다. 

#### **Closure function**
```python
def multiply_by(n):

    def multiplier(x):
        return x * n
    
    return multiplier

times3 = multiply_by(3)

print(times3(9)) # 27
```
위 코드를 잘 보면 방금전에 배운 First class function의 특징을 잘 이용하고 있다. 

일단 `multiplier()` 함수를 먼저 확인해 보자, 이 함수는 `x` 와 `n`을 곱한 값을 리턴해 주는 함수다. `x`는 이 함수의 인자 (parameter)로 받는다. 그럼 `n`은?

`n`은 보다시피 `multiplier` 함수를 감싸고 있는 `multiply_by` 함수의 인자다. 

즉, `multiplier` 함수는 본 함수의 인자 `x` 와 `multiply_by` 함수의 인자 `n`을 사용하는 함수다. 

그리고 `multiply_by` 함수는 `multiplier` 함수를 리턴한다. first class function 특징이다.

밑에 코드를 보자.

`times3 = multiply_by(3)` 이 코드가 실행이 되면 `3` 은 누구의 인자로 가게 될까?

`multiply_by`의 인자가 `3`이 된다. 곧 `n` 은 `3`이 된다.

이후 `times3(9)` 이라는 코드가 있다.

이걸 풀어보면 `multiplier(x=9)`이라는 코드가 되는 것이다.

그러면 당연히 값은 `n` * `x`니까 `3` * `9` = `27` 가 된다.

Closure 함수의 특징은 주어진 것을 기억할 수 있는 것이다. 

심지어 `multiply_by` 함수를 지워도 `times3` 함수는 주어진 `3`의 값을 기억하고 쓸 수 있다.

```python
def multiply_by(n):

    def multiplier(x):
        return x * n
    
    return multiplier

times3 = multiply_by(3)

del multiply_by

print(times3(9)) # 27

print(multiply_by(3)) # NameError: name 'multiply_by' is not defined
```

**정리**
- Closure 함수는 아래와 같은 특징이 있다:
    1. 함수(A)안에 함수(B)가 있고
    2. 함수(B)는 함수(A)가 쓸 수 있는 변수, 객체, 클라스를 사용하고
    3. 함수(A)가 함수(B)를 리턴해야한다

- 언제 쓰는 것 일까? 
    - OOP를 구현하기 위해 사용하기도 하고
    - 특정 함수의 구조를 숨기기 위해서도 사용하며
    - Python Decorator 함수에서도 사용된다

- 무조건적으로 함수(A)에 주어진 값을 숨길 수 있나?
    - 아니다
    - ```python
        >>> times3.__closure__[0].cell_contents
        3
        ```
    - 위와 같이 보고자 하면 closure 함수가 무엇을 기억하고 있는지 볼 수는 있다.

****

# Decorators

고오오급 정보까지는 아니지만 파이썬으로 개발하다가 보면 가끔식 이런 함수를 볼 때가 있다. 

```python
@whatsthis
def someFunc():
    print("이게 뭐야")

someFunc()
# 출력:
# This is the decorator,
# 이게 뭐야
# Decorator ended
```

이런 함수를 Decorator를 사용한 함수라고 표현하는데 위에서 Decorator는 `whatsthis`에 속한다. 

**Decorator란 Closure 함수를 사용해서 특정 함수의 앞, 뒤로 간단하게 기능을 추가해주는 것이다.**

자 `whatsthis` 함수를 한번 보자.

```python
def whatsthis(func):

    def wrapper():
        print("This is the decorator, ")
        func()
        print("Decorator ended")

    return wrapper
```

잘 살펴보면 Closure 함수인 것을 알 수 있다. 
`wrapper`는 `whatsthis` 함수의 `func` 인자를 사용하는 함수고 `whatsthis` 함수는 리턴 값으로 `wrapper` 함수를 준다.

`wrapper` 함수의 `func()` 함수는 우리가 인자로 주는 함수인 것을 알 수 있다. 
이 `func()` 함수가 `@whatsthis` 아래에 작성이 되는 함수인 것이다. 

다시 원문 코드를 한 번 보자.

```python
@whatsthis
def someFunc():
    print("이게 뭐야")

someFunc()
# 출력:
# This is the decorator,
# 이게 뭐야
# Decorator ended
```

`@` 부호를 사용하고 Closure 함수명을 적어준 뒤, 아래 해당 Closure 함수에 사용할 함수를 적어 주게되면 해당 함수는 인자 함수로 사용된다. 

현재 위의 코드를 풀어서 보면, `someFunc()`는 사실 `whatsthis` 함수의 `wrapper` 함수인 것이다. 그리고 `wrapper` 함수 안에 `func()`는 `someFunc` 함수 인 것이다.

이렇게 설명하면 헷갈리니까 `someFunc()`를 변수명에 저장하자

```python
def whatsthis(func):

    def wrapper():
        print("This is the decorator, ")
        func()
        print("Decorator ended")

    return wrapper


@whatsthis
def someFunc():
    print("이게 뭐야")

x = someFunc
x()
# 출력:
# This is the decorator,
# 이게 뭐야
# Decorator ended
```

자, `x`는 `someFunc` 함수다. `someFunc`는 `wrapper` 함수다. `wrapper` 함수는 `whatsthis` 함수의 인자 `func`를 사용한다. `func`는 `someFunc`다.

고로, `x`는 `someFunc` 함수를 사용하는 `wrapper` 함수다.

이번에는 인자를 받을 수 있는 Decorator를 만들어보자 

```python
def whatsthis(func):

    def wrapper(myname):
        print("This is the decorator,", myname)
        func()
        print("Decorator ended")

    return wrapper

@whatsthis
def someFunc():
    print("이게 뭐야")

x = someFunc
x("마이클")

# This is the decorator, 마이클
# 이게 뭐야
# Decorator ended
```

위를 보면 `myname`이라는 변수가 인자로 주어진다. 이 인자를 주는 곳은 함수를 호출 할 때다. `x('마이클')`을 호출하면 마이클이라는 이름이 잘 표현되는 것을 알 수 있다. 

만약 `someFunc()` 함수에서 인자를 받고 싶다면? 

```python
def whatsthis(func):

    def wrapper(myname):
        print("This is the decorator,")
        func(myname)
        print("Decorator ended")

    return wrapper

@whatsthis
def someFunc(name):
    print("이게 뭐야, ", name)

x = someFunc
x("마이클")

# This is the decorator,
# 이게 뭐야, 마이클
# Decorator ended
```

이런식으로 활용하면 된다. `wrapper` 함수의 `myname` 인자가 그대로 `func(myname)`에 인자 값으로 주어진다. `someFunc(name)` 함수의 `name`은 `myname`이 되는 것이다.

`x`는 `wrapper` 함수인 것을 인지하면 이해하기가 쉬워진다.

자 이제 Decorator를 알아보았다. 하지만 Decorator는 중첩이 가능하다. 

```python
@whatisthis
@whatisthis2
def someFunc():
    print("살려줘")

someFunc()
# 출력 :
# This is the decorator,
# Hello There,
# 살려줘
# Bye
# Decorator ended
```

전체 코드를 살펴보자.

```python
def whatisthis2(func):
    def wrapper():
        print("Hello There, ")
        func()
        print("Bye")
    
    return wrapper

def whatsthis(func):

    def wrapper():
        print("This is the decorator,")
        func()
        print("Decorator ended")

    return wrapper

@whatsthis
@whatisthis2
def someFunc():
    print("살려줘")

someFunc()
```

간략히 설명하자면 `@whatsthis` Decorator의 인자 함수는 `whatisthis2` 함수가 된다. 그리고 `@whatisthis2`의 인자 함수는 `someFunc` 함수가 된다. 

그리고 `whatsthis` 의 리턴 함수는 `wrapper` 함수인데, 이 `wrapper` 함수는 `whatisthis2` 의 `wrapper` 함수를 사용하는 것이다. 

즉, `someFunc` 함수를 실행 시키면 사실 아래 코드가 실행되는 것이다

```python
print("This is the decorator,") # whatsthis의 wrapper 함수
print("Hello There")            # whatisthis2의 wrapper 함수
print("살려줘")                 # someFunc 함수
print("Bye")                    # whatisthis2의 wrapper 함수
print("Decorator Ended")        # whatsthis의 wrapper 함수
```

머리가 쿠크다스 마냥 뽀개질거 같은데 왜 쓰는 것일까. 보면 알겠지만 한 번 이해만 한다면 가독성이 뛰어난 방법으로 코드를 짤 수 있기 때문이다. 

예를 들면

```python
@logger
def someFunc():
    pass
```

위와 같이 Decorator 이름만 잘 지어준다면 가독성이 매우 뛰어나다. `@logger`가 내부적으로 정확히 어떻게 돌아가는지 알 필요도 없고 사용법만 알면 되니까 개발자 입장에서는 상당히 편리한 기능이라고 볼 수 있다. 

****

# Decorators 2

Decorator는 함수를 꾸며주는 것이라고 배웠을 것이다. 맞는 말이다. 사실 함수뿐만 아니라 Class도 가능하다. 

```python
def Foo(obj):
    def wrapper():
        print("Creating object")
        print(obj().name)
        print("Object created")

    return wrapper

@Foo
class Bar:
    def __init__(self):
        self.name = "test"
    
Bar()
# 출력 :
# Creating object
# test
# Object created
```

이런식으로 `Bar` 클라스를 `Foo` closure 함수에서 사용을 하는 것이다.

혹은 `Foo` Decorator 자체를 `class` 객체로도 만들수있다. 

```python
class Foo:
    def __init__(self, cls):
        self.x = cls
    
    def __call__(self):
        print("testing")
        self.x().run()
        print("test done")

@Foo
class Bar:
    def __init__(self):
        self.name = "test"
    
    def run(self):
        print(self.name)
    
Bar()
# 출력 :
# testing
# test
# test done
```

이런 경우 Decorator 객체인 `Foo`의 `__call__` 내장 함수를 `wrapper` 함수라고 생각하면 된다. 그리고 `Foo`의 `__init__` 함수의 인자로 `Bar` 가 주어진다고 보면 된다.

이런식으로 Decorator를 쓰는 경우는 드문 것 같다.

마찬가지로, 이런 방식도 가능하다

```python
class Foo:
    def __init__(self, func):
        self.x = func
    
    def __call__(self):
        print("testing")
        self.x()
        print("test done")

@Foo
def someFunc():
    print("Yo")
    
someFunc()
# 출력 :
# testing
# Yo
# test done
```

일반적으로 Decorator를 많이 볼 일이 없을수도 있다. 하지만 개발자라면 언어가 제공하는 개발 방법을 잘 알고 있고 그 방법들을 적재적소에 활용하는 것이 중요하다고 생각해서 포스트를 남겨 보았다. 

****

