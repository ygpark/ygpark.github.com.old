---
layout: post
title: "bash에서 검색하기"
description: ""
category: Linux
tags: [Linux, find, xargs, grep]
---
{% include JB/setup %}

리눅스에서 일반적으로 파일을 찾을 때에는 find명령을, 텍스트를 걸러낼 때에는 grep명령을 사용합니다. 이 두 명령어는 없어서는 안될 중요한 명령입니다. 하지만 이 두 명령을 조합해서 쓴다면 더 막강한 힘을 발휘합니다. 바로 xargs으로 명령어들을 연결해주면 됩니다.

###find, xargs, grep의 조합

find, xargs, grep명령을 조합하여 사용방법입니다:

	find . -name <파일명> | xargs grep --color -n <검색어>

이것이 가능한 이유는 find의 결과로 나오는 목록을 xargs가 grep의 마지막 인자로 넣어주기 때문입니다.

리눅스 커널을 개발하는 경우는 아래와 같은 스크립트로 소스코드를 검색하곤 합니다.

    #!/bin/sh
    # grep-kernel.sh
    
    find . -name "*.[chSs]" -o -name "*.cpp" -o -name "Makefile" -o -name ".config" | xargs grep --color -n $1

혹은 이렇게 Kconfig를 검색하기도 하죠.

    #!/bin/sh
    # grep-kconfig.sh
    
    find . -name Kconfig | xargs grep --color -n $1

