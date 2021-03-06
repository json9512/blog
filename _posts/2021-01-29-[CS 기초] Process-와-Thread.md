---
layout: post
title:  "Process 와 Thread"
tags: CS
comments: true
---
출처: [프로세스 - 위키피디아](https://en.wikipedia.org/wiki/Process_(computing)), [스레드 - 위키피디아](https://en.wikipedia.org/wiki/Thread_(computing))

프로세스와 스레드에 대해 알아보기 전에 프로그램이 어떻게 실행되는지를 알아보자.

컴퓨터에서 크롬을 실행시킨다고 생각해보자.

크롬은 프로그램이고 프로그램이란 특정 task를 수행하기 위한 컴퓨터 명령어들의 집합체다. 

프로그램은 프로그래밍 언어로 쓰여지고 Disk나 비휘발성 메모리에 저장된다. 크롬은 C++을 기반으로 쓰여졌다.

이후 이 명령어들의 집합체를 0과1로 컴퓨터가 이해 할 수 있도록 저장시켜놓는 방식 (compiled)이 있고 프로그램이 실행 될 때 0과1로 변환시키는 방식(interpreted)이 있다.

아무튼, 크롬을 클릭하면 컴퓨터는 크롬이란 프로그램의 명령어들을 이진수의 형태로 컴퓨터의 메모리에 저장하게된다.

# 프로세스

메모리에 저장된 이진수 코드를 실행 시키기위해 OS는 프로세스라는 것을 만든다. 프로그램을 실행 시키기 위해 복수의 프로세스가 필요할 수도 있다.

OS는 프로세스에게 프로세스 정보, 레지스터, 카운터, 스택, 힙, 실행될 코드를 배분해준다. 이를 PCB (Process control block)라고도 한다.

- 프로세스 정보: 
    1. 프로세스 아이디
    2. 프로세스 상태

- 레지스터: CPU와 관련된 정보를 담고있다. Instruction, storage address 등 프로세스와 관련된 정보를 가장 빠르게 읽어 볼 수 있는 위치에 저장한다.

- 카운터: 포인터라고도 한다. 현재 프로그램이 어디쯤 실행됐는지 혹은 다음 실행 구간이 어딘지를 가르킨다.

- 스택: 함수, 함수 인자, 리턴 값, 로컬 변수 등 빠르게 읽고 쓰여야 할 정보를 저장한다.

- 힙: dynamic allocated 메모리다.

- 실행될 코드: 프로세스가 실행해야할 프로그램의 코드 일부분이다.

이 외에도 I/O 상태 정보나 스케쥴링 정보 등 더 있다. [위키피디아](https://en.wikipedia.org/wiki/Process_control_block)를 참고하자.

각 프로세스는 다른 프로세스와 분리되어 있다.

프로세스간 스위칭을 하게 될 때, 프로세스의 상태, 자원 등을 저장하고 로딩해야 되기 때문에 일정 시간이 소요된다.

프로세스는 경우에 따라 Concurrent 또는 Parallel하게 실행 될 수 있다.

>### Concurrency (병행성) 과 Parallel (병렬)
Concurrency는 논리적 (코드로 인해)으로 하나의 코어/스레드에서 Context switching을 하면서 일렬의 명령어를 실행하는 것. <br/><br/>
Eg. 한 명의 고수 체스 플레이어가 20명의 상대를 상대한다고 생각해보자. 20개의 테이블에 고수가 직접 돌아다니면서 20개의 게임을 5초씩 처리하는 것이라고 보면 된다.<br/><br/>
Parallel 처리는 물리적으로 복수의 코어/스레드가 *동시에*  Task를 수행하는 것이다.<br/>
Eg. 20명의 고수가 20명의 선수를 각 각 상대하는 것.

다시 크롬을 생각해보자. 이제 크롬 창이 열렸다고 생각하고 새로운 탭을 켜면, 새로운 프로세스가 시작되는 것이다. 

프로세스간 분리되어 있어야하는 이유는 특정 프로세스가 먹통이 되면 전체 프로그램을 먹통으로 만들게 될 수 있기 때문이다.

만약 크롬 탭 중에 하나가 응답이 없다고 나머지 탭들도 응답을 하지 않는다면 상당히 짜증날 것이다. (IE 7까지만 해도 흔한 상황이었다.)

# 스레드
크롬에서 구글을 사용한다고 가정해보자. 크롬 창에 보여지는 탭과 옵션, 북마크, 크롬 익스텐션, 네트워크 통신 기능 등 크롬이란 프로그램에는 여러가지 기능들이 있다.

이 프로그램에는 여러개의 프로세스가 돌아가고 있다. 그리고 각 프로세스는 스레드라는 것이 있다.

프로세스 내에 스레드의 개수는 최소 하나다.

스레드는 프로세스의 자원을 공유한다. 각 스레드는 자기만의 레지스터와 스택을 가지게 되고 힙 메모리와 프로세스에 할당 된 실행코드를 나눠쓰게 된다.

스레드는 힙 메모리를 공유하고 있기 때문에 하나의 스레드에서 실행되는 코드가 다른 스레드의 코드에 영향을 줄 수 있다. 이는 장점이 되기도하고 단점이 되기도 한다.

| Process | Thread |
|:-------:|:------:|
|실행까지 소요시간이 상대적으로 길다|실행까지 소요시간이 상대적으로 짧다|
|프로세스는 자기만의 메모리 공간을 가진다|스레드는 프로세스가 사용하는 메모리 공간을 사용한다|
|프로세스간 통신은 상대적으로 느리다|스레드간 통신은 상대적으로 빠르다. 메모리를 공유하기 있기 때문|
|Context Switching시 더 많은 시간과 자원이 필요하다|Context Switching시 더 적은 시간과 자원이 필요하다|
|프로세스간 메모리를 공유하지 않는다|같은 프로세스에 있는 스레드간 메모리를 공유한다|

