---
layout: post
title: "윈도우8에 ADB/fastboot 드라이버 설치하기"
description: ""
category: Android
tags: [android, windows8, adb, fastboot]
---
{% include JB/setup %}

![안드로이드 이미지](/images/blog/android.png)
<img src="/images/blog/plus.png" width="50px" style="margin:60px 20px" alt="plus 이미지"/>
![윈도우8 이미지](/images/blog/windows8.png)

안드로이드 개발하려면 윈도우에 adb/fastboot 드라이버가 설치되어 있어야 합니다. 그런데 윈도우8에는 드라이버가 설치되지 않고 장치관리자에 느낌표가 붙은 `Android 1.0`로 보입니다. 이 경우 다음 순서대로 진행하면 드라이버를 설치할 수 있습니다.

1. ADB driver를 설치합니다. <br/>
[여기서](http://ygpark-data.tistory.com/1) 다운로드 받으세요.<br/>
이 파일은 압축파일입니다. 압축을 풀고, 안에 들어있는 inf파일들을 설치합니다.

1. USB Driver를 설치합니다. <br/>
[여기서](http://ygpark-data.tistory.com/2) 다운로드 받으세요.<br/>
이 파일은 설치파일입니다. 압축을 풀고 USBDrv.exe파일을 실행시켜 드라이버를 설치합니다.

1. 모든 설치가 완료되었습니다. <br/>
이제 장치관리자에서 `Android 1.0`이 사라진 것을 확인할 수 있습니다.


##참조

- [http://forum.xda-developers.com/showthread.php?t=1583801](http://forum.xda-developers.com/showthread.php?t=1583801)












