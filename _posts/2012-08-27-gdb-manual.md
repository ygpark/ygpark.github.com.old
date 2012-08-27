---
layout: post
title: "gdb 매뉴얼 - core dump 사용하기"
description: ""
category: Linux
tags: [gdb, debugging, ulimit, core-dump, Linux]
---
{% include JB/setup %}

core dump란 충돌이 발생했던 순간의 memory를 저장한 파일을 말한다. gdb는 core dump 파일을 불러와서 디버깅을 하는 기능을 제공한다. 하지만 그전에 core dump가 저장될 수 있도록 하는 설정이 필요하다.

##준비작업

core dump가 저장될 수 있도록 하는 설정은 `ulimit` 명령을 사용한다. 

    $ ulimit -a
    core file size          (blocks, -c) 0
    data seg size           (kbytes, -d) unlimited
    scheduling priority             (-e) 20
    file size               (blocks, -f) unlimited
    pending signals                 (-i) 16382
    max locked memory       (kbytes, -l) 64
    max memory size         (kbytes, -m) unlimited
    open files                      (-n) 1024
    pipe size            (512 bytes, -p) 8
    POSIX message queues     (bytes, -q) 819200
    real-time priority              (-r) 0
    stack size              (kbytes, -s) 8192
    cpu time               (seconds, -t) unlimited
    max user processes              (-u) unlimited
    virtual memory          (kbytes, -v) unlimited
    file locks                      (-x) unlimited


`ulimit -a`의 출력을 보면, 기본적으로 core file size가 0으로 잡혀있는 것을 확인할 수 있다. 이 상태에서는 core dump 파일이 생성되지 않는다. 따라서 core file size를 설정해 주어야 한다. 여기서는 unlimited로 설정한다.

설정은 `ulimit -c unlimited` 명령으로 한다.

	$ ulimit -c unlimited
	
    $ ulimit -a
    core file size          (blocks, -c) unlimited
    data seg size           (kbytes, -d) unlimited
    scheduling priority             (-e) 20
    file size               (blocks, -f) unlimited
    pending signals                 (-i) 16382
    max locked memory       (kbytes, -l) 64
    max memory size         (kbytes, -m) unlimited
    open files                      (-n) 1024
    pipe size            (512 bytes, -p) 8
    POSIX message queues     (bytes, -q) 819200
    real-time priority              (-r) 0
    stack size              (kbytes, -s) 8192
    cpu time               (seconds, -t) unlimited
    max user processes              (-u) unlimited
    virtual memory          (kbytes, -v) unlimited
    file locks                      (-x) unlimited

위와 같이 설정이 되었다면, 프로그램이 죽을 때 현재 디렉토리($PWD)에 core dump 파일이 생긴다. 

##사용법

core dump를 이용한 디버깅 방법은 다음과 같다:

	$ gdb [프로그램명] [덤프파일명]

gdb는 실행직후 core dump 파일을 로드하여 종료 직전의 메모리 상태를 만든다. 이 때 bt(backtrace)같은 gdb의 명령어셋을 사용하여 디버깅을 시작한다.



