---
layout: post
title: "Linux Kernel - 새 defconfig 만들기"
description: ""
category: Linux Kernel
tags: [linux, kernel, defconfig]
---
{% include JB/setup %}

대부분의 리눅스 커널을 개발할 때 최초에 아래와 같은 명령으로 컴파일하는것을 볼 수 있다. 이 명령들 중 make xxx_defconfig 는 해당 타겟용으로 미리 정의된 .config 파일을 커널의 루트 디렉토리로 복사하는 용도로 사용된다.

	$ make xxx_defconfig
	$ make

이것은 어떻게 가능한 것인가? 

먼저 make menuconfig 명령으로 .config파일을 만든다. 그리고나서 아래처럼 arch/arm/configs/ 디렉토리에 복사하면 끝난다.

	$ cp .config arch/arm/configs/product_defconfig

이제 마음놓고 make distclean 명령을 내릴 수 있다. :D