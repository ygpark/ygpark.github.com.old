---
layout: post
title: "alsa-util cross compile"
description: ""
category: Porting
tags: [alsa, cross-compile]
---
{% include JB/setup %}


ALSA util cross-compile 하는 방법입니다.


##컴파일 하는법

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux --with-curses=ncurses
	$ make


##에러 메시지

에러상황을 기록하고자 남깁니다. 저의 경우에는 curses 라이브러리를 찾지 못해서 configure 에러가 났었습니다.

	CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux
	configure: WARNING: if you wanted to set the --build type, don't use --host.
	    If a cross compiler is detected then cross compile mode will be used
	checking for a BSD-compatible install... /usr/bin/install -c
	checking whether build environment is sane... yes
	checking for i686-linux-strip... no
	checking for strip... strip
	checking for a thread-safe mkdir -p... /bin/mkdir -p
	checking for gawk... no
	checking for mawk... mawk
	checking whether make sets $(MAKE)... yes
	checking whether NLS is requested... yes
	.....중략.....
	checking for stdint.h... yes
	checking for unistd.h... yes
	checking panel.h usability... no
	checking panel.h presence... yes
	configure: WARNING: panel.h: present but cannot be compiled
	configure: WARNING: panel.h:     check for missing prerequisite headers?
	configure: WARNING: panel.h: see the Autoconf documentation
	configure: WARNING: panel.h:     section "Present But Cannot Be Compiled"
	configure: WARNING: panel.h: proceeding with the compiler's result
	checking for panel.h... no
	configure: error: required curses helper header not found

