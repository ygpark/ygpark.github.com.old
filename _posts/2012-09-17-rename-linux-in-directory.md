---
layout: post
title: "파일이름을 간편하게 바꾸자. Bash 스크립트 활용"
description: ""
category: Linux
tags: [bash]
---
{% include JB/setup %}

리눅스 쉘에서 파일의 확장자를 바꿔야 할 일이 간혹 있습니다. 저는 md파일을 html파일로 바꿔야 하는데 어떻게 하는지 알아볼까요?

손으로 일일이 타이핑 하기에는 너무 파일이 너무 많고 시간이 오래 걸린다면, 이렇게 Bash 스트립트를 사용해서 시간을 줄여보세요.

	for i in `ls *.md` ; do echo "mv $i ${i%.*}.html" ; done

업무에 지친 심신이 조금이나마 회복되셨나요?

	+ HP 1000
	+ HP 1000
	+ HP 1000

혹시나 몰라서 echo로 명령어를 출력하기만 했어요. 확인해보고 이상 없으면 echo랑 따옴표를 빼고 실행하면 되어요. 어때요? 참 쉽죠잉~~

