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


**목차**

1. **[Memory Management](#memory-management)**
2. **[Global Interpreter Lock](#global-interpreter-lock)**
3. **[더 업데이트 할 계획](#)**

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