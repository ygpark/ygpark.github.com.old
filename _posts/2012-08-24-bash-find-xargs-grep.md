---
layout: post
title: "bash에서 검색하기"
description: ""
category: Linux
tags: [Linux, find, xargs, grep]
---
{% include JB/setup %}

#find, xargs, grep의 조합#

리눅스에서 검색을 할 때 find, xargs, grep을 조합해서 쓰는것을 추천한다. 원하는 파일만 골라서 검색할 수 있기 때문이다. 

<br>
<br>

기본적인 사용법은 다음과 같다:

`find . -name <파일명> | xargs grep --color -n <검색어>`

<br>
<br>

리눅스 커널을 개발하는 경우는 아래와 같은 스크립트로 소스코드를 검색한다.

    #!/bin/sh
    # grep-kernel.sh
    
    find . -name "*.[chSs]" -o -name "*.cpp" -o -name "Makefile" -o -name ".config" | xargs grep --color -n $1

<br>
<br>

혹은 이렇게 Kconfig를 검색하기도 한다:

    #!/bin/sh
    # grep-kconfig.sh
    
    find . -name Kconfig | xargs grep --color -n $1

