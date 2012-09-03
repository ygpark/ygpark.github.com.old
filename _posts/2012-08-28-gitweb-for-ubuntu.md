---
layout: post
title: "gitweb 설치"
description: ""
category: Git
tags: [git, gitweb, linux, ubuntu]
---
{% include JB/setup %}

이 문서는 Git 사용자에게 꼭 필요한 필수 유틸리티인 gitweb의 설치에 대해서 설명한다.

![](/images/gitweb/gitweb.png)

그림 1. kernel.org 사이트의 gitweb



###gitweb 설치

이렇게 입력하면 웹으로 gitweb에 접근할 수 있다.

	$ sudo apt-get install apache2 git-core gitweb

	$ cd /var/www/
	$ mkdir gitweb
	$ cd gitweb
	$ ln -s /usr/share/gitweb/* .

###프로젝트 설정

다음으로 gitweb에 프로젝트를 추가하는 방법이다.

기본적인 사용법은 아래와 같고

	$ cd /var/cache/git
	$ ln -s /path/to/repo1/.git repo1.git

다음과 같이 디렉토리를 만들어서 사용자/프로젝트별로 구분할 수도 있다.

	$ cd /var/cache/git
	$ mkdir username
	$ cd username

	$ ln -s /path/to/repo2/.git repo2.git
	$ ln -s /path/to/repo3/.git repo3.git

###참조

- [http://wiki.kldp.org/wiki.php/gitweb](http://wiki.kldp.org/wiki.php/gitweb)
- [http://kldp.org/node/100726](http://kldp.org/node/100726)