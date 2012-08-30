---
layout: post
title: "lxr 설치하기 (Ubuntu 10.04)"
description: ""
category: Linux Kernel Tip
tags: [linux, lxr]
---
{% include JB/setup %}

http://lxr.sourceforge.net/에서 내려받을 수 있는 리눅스 교차 참조기인 lxr은 웹 브라우저로 커널 트리를 탐색하도록 커널 심볼 정의와 사용처를 하이퍼링크로 연결한다.


#요구사항

- Apache – Web server
- lxr -Linux Cross Reference
- Perl – Reference generator

#설치

	$ sudo apt-get install lxr perl apache2

#Apache 설정

apache의 설정파일 `etc/apache2/sites-enabled/000-default`을 수정하여 `/usr/share/lxr` 디렉토리를 웹으로 접근할 수 있도록 설정한다.

    $ sudo vi etc/apache2/sites-enabled/000-default

    # Linux Cross Reference Stuff
    Alias /lxr "/usr/share/lxr/"
    <Directory "usr/share/lxr">
            Options All
            AllowOverride All
    </Directory>

그리고나서 서버를 재시작한다.

	$ sudo /etc/init.d/apache2 restart


#lxr 설정

lxr의 설정 파일은 `/usr/share/lxr/http/lxr.conf`이다. `variable: a`, `dbdir`, `glimpsebin` 을 수정해야한다.

    # 여기에 arm을 추가했다.
    variable: a, Architecture, (arm, i386, alpha, m68k, mips, ppc, sparc, sparc64)

    # lxr은 이 경로에서 xref, fileidx 파일을 찾는다. 
	# 만약 db를 못찾으면 경로를 수정해야한다.
	# 주의: 경로의 끝에는 '/'가 없어야한다.
    dbdir: /usr/share/lxr/source/$v

    # 쉘에서 'which glimpse' 명령으로 경로를 찾은다음 넣어준다.
    glimpsebin: /usr/local/bin/glimpse

아래는 수정한 `/usr/share/lxr/http/lxr.conf`의 최종본이다.

    # Configuration file.

    # Define typed variable "v", read valueset from file.
    variable: v, Version, [/usr/share/lxr/source/versions], [/usr/share/lxr/source/defversion]

    # Define typed variable "a".  First value is default.
    variable: a, Architecture, (arm, i386, alpha, m68k, mips, ppc, sparc, sparc64)

    # Define the base url for the LXR files.
    baseurl: http://192.168.0.38/lxr/http/

    # These are the templates for the HTML heading, directory listing and
    # footer, respectively.
    htmlhead: /usr/share/lxr/http/template-head
    htmltail: /usr/share/lxr/http/template-tail
    htmldir:  /usr/share/lxr/http/template-dir

    # The source is here.
    sourceroot: /usr/share/lxr/source/$v/linux/
    srcrootname: Linux

    # "#include <foo.h>" is mapped to this directory (in the LXR source
    # tree)
    incprefix: /include

    # The database files go here.
    dbdir: /usr/share/lxr/source/$v

    # Glimpse can be found here.
    glimpsebin: /usr/local/bin/glimpse

    # The power of regexps.  This is pretty Linux-specific, but quite
    # useful.  Tinker with it and see what it does.  (How's that for
    # documentation?)
    map: /include/asm[^\/]*/ /include/asm-$a/
    map: /arch/[^\/]+/ /arch/$a/




#교차 참조 만들기

i) 만약 `/usr/share/lxr/source` 디렉토리가 없으면 만든다.

ii) `2.6.32` 디렉토리를 그 안에 만든다. - 2.6.32는 교차 참조할 리눅스 소스코드의 버전이다. 

	$ sudo mkdir /usr/share/lxr/source/2.6.32 -p

iii) 리눅스 [커널소스](http://www.kernel.org)를 `linux/`의 서브 디렉토리에 압축을 해제한다. 예를들면 경로는 `/usr/share/lxr/source/2.6.22/linux/` 이고 소스코드는 `linux/`아래에 위치한다.

	$ cd /usr/share/lxr/source/2.6.32
	$ sudo tar zxvf linux-2.6.32.tar.gz

iv) `/usr/share/lxr/source/versions` 파일을 편집하여 `2.6.32`를 입력한다. 입력해야할 버전이 여러개라면 라인 단위로 구분하여 입력한다.

v) defversion라는 이름의 소프트링크를 만든다.

	$ sudo ln -s /usr/share/lxr/source/2.6.32 /usr/share/lxr/source/defversion

vi) `/usr/share/lxr/source/2.6.32/`로 이동한 다음 `genxref linux`명령을 실행한다.

	$ cd/usr/share/lxr/source/2.6.32/linux
	$ sudo genxref

vii) `/usr/share/lxr/source/2.6.32/` 디렉토리에 읽기 권한을 설정한다. 

	$ sudo chmod -R o+r /usr/share/lxr/source/2.6.32/*




