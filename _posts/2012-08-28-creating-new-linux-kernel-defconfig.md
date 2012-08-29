---
layout: post
title: "Linux Kernel - 새 defconfig 만들기"
description: ""
category: Linux Kernel
tags: [linux, kernel, defconfig]
---
{% include JB/setup %}

초보 리눅스커널 개발자에게 유용한 팁입니다.

우리는 커널 빌드할 때 **make xxx_defconfig** 같은 명령을 입력합니다. 이 명령의 의미는 '미리 만들어 놓은 **.config** 파일을 사용하라' 입니다.

	$ make xxx_defconfig
	$ make

내 커널소스도 이렇게 만들고 싶은데 어떻게 해야할까요? 먼저 커널소스의 루트디렉토리에서 지금 쓰고있는 **.config**를 찾습니다. 그리고 그것을 **arch/arm/configs/** 디렉토리에 복사합니다. 이게 다에요. 다음엔 그냥 **make xxx_defconfig** 명령을 사용하기만 하면 됩니다.

	$ cp .config arch/arm/configs/xxx_defconfig
	$ make xxx_defconfig

이제 마음놓고 옵션들을 바꿀 수 있고 되돌릴 수도 있습니다. make distclean 같은 명령도 내릴 수 있죠. :D