---
layout: post
title:  "Context Switching 이란"
tags: CS
comments: true
---
출처: [Context Switch - 위키피디아](https://en.wikipedia.org/wiki/Context_switch)

Context Switching에 대해 알아보자. 줄여서 `CS`라고 하겠다.

CS는 process 혹은 thread의 상태를 저장해서 추후에 다시 저장한 곳을 불러와 작업을 이어가는 것을 뜻한다. 즉 CS는 컴퓨터가 1개 이상의 process나 thread를 번갈아가면서 작업할 때 사용되는 process/thread 관리 기법이다.

이 밖에 interrupt의 형식으로도 일어난다. 예를 들면 task가 dist storage를 사용해야 할 때, CPU를 다른 task가 쓸 수 있도록 해준다.

어떤 OS들은 user mode 와 kernel mode task간 CS를 해야한다.

기본적으로 CS는 overhead가 발생한다. Processor 간 CS가 Thread 간 CS보다 overhead가 크다. 이유는 Process Control Block (PCB)가 Thread Control Block (TCB) 보다 더 많은 데이터를 다루고 있기 때문이다.