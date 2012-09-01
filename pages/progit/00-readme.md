---
layout: page
title: "Pro Git"
description: ""
---

{% include JB/setup %}


<hr/>

목차
1. [README](/pages/progit/00-readme.html)
1. [시작하기](/pages/progit/01-introduction/01-chapter1.html)
1. [Git의 기초](/pages/progit/02-git-basics/01-chapter2.html)
1. [Git 브랜치](/pages/progit/03-git-branching/01-chapter3.html)
1. [Git 서버](/pages/progit/04-git-server/01-chapter4.html)
1. [분산 환경에서의 Git](/pages/progit/05-distributed-git/01-chapter5.html)
1. [Git 도구](/pages/progit/06-git-tools/01-chapter6.html)
1. [Customizing Git](/pages/progit/07-customizing-git/01-chapter7.html)
1. [Git과 다른 VCS](/pages/progit/08-git-and-other-scms/01-chapter8.html)
1. [Git의 내부구조](/pages/progit/09-git-internals/01-chapter9.html)

<hr/>

Pro Git 책의 내용
=====================

이 저장소는 Pro Git의 source code이고 라이센스는 Creative Commons Attribution-Non Commercial-Share Alike 3.0를 따름니다. 필자는 여러분이 즐겁게 git을 배웠으면 좋겠습니다. 만약 Amazon에서 책을 구입해주신다면 저와 Apress는 깊은 감사를 드릴 것입니다:

http://tinyurl.com/amazonprogit

Ebook 만드는 법
=====================

페도라에서는 다음과 같이 하면 됩니다:

    $ yum install ruby calibre rubygems ruby-devel rubygem-ruby-debug 
    $ gem install rdiscount
    $ FORMAT=epub ./makeebooks ko # FORMAT이 없으면 mobi파일이 생성됩니다.

Mac에서는 먼저 Calibre를 내려받아 설치하고 `ebook-convert` 명령을 실행경로에 넣어 줘야 합니다. 예를 들어 `~/bin/`을 실행경로로 사용하고 있다면 다음과 같이합니다:

    $ ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert ~/bin/ebook-convert

그리고 gem을 설치하고 만들면 됩니다:

    $ sudo gem install rdiscount ruby-debug
    $ ./makeebooks en  # will produce a mobi

Pdf 만드는 법
=====================

Mac에서는 먼저 Pandoc과 MacTex 패키지를 설치합니다. pdf를 빌드하렴 xetex가 필요한데 MacTex가 가장 쉽습니다. MacTex(2011 패키지 1.8G)는 용량이 크지만 전부 설치합니다.

그리고 makepdf명령으로 만들 수 있습니다:

    $ ./makepdfs ko

오류
=====================

틀린 부분이나 개선해야 할 부분이 있으면 주저 말고 저에게 메일을 보내주세요.
(schacon at gmail dot com)

번역
=====================

이 책을 번역해서 알려주시면 제가 progit.org에 번역본을 올릴 것입니다. 적당히(이탈리아어로 번역한다면 `it`라는 디렉토리를 만든다) 하위 디렉토리를 만들고 번역을 완성하고 나서 필자에게 pull 요청을 해주세요.

역자 정보
=====================

번역, 박창우(Changwoo Park), pismute at gmail dot com, https://github.com/pismute
번역, 이성환(Sean Lee), lethee at gmail dot comt, https://github.com/lethee
