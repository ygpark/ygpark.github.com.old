---
layout: post
title: "alsa-lib-1.0.16 cross compile for arm"
description: ""
category: Linux
tags: [Linux, alsa, arm, cross-compile]
---
{% include JB/setup %}


### 요약

기본적인 크로스컴파일 방법과 configure시 libpython이 없다는 에러가 발생했을 때 대처법입니다.

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux
	$ make

libpython이 없다는 에러가 나면 이렇게 하세요.

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux --disable-python
	$ make



### 경험담


alsa-lib를 컴파일할 일이 생겼습니다. 그런데 configure 에서 에러가 발생합니다.`cannot find -lpython2.6` 뭐라는걸로 보아서 python 라이브러리가 필요한듯 하네요.

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux
	...
	...
	...
    /opt/arm/4.3.1-eabi-armv6/usr/bin-ccache/.........: cannot find -lpython2.6
    collect2: ld returned 1 exit status
    make[3]: *** [smixer-python.la] 오류 1
    make[3]: Leaving directory `/home/ygpark/download/sound/alsa-lib-1.0.16/modules/mixer/simple'
    make[2]: *** [all-recursive] 오류 1
    make[2]: Leaving directory `/home/ygpark/download/sound/alsa-lib-1.0.16/modules/mixer'
    make[1]: *** [all-recursive] 오류 1
    make[1]: Leaving directory `/home/ygpark/download/sound/alsa-lib-1.0.16/modules'
    make: *** [all-recursive] 오류 1

어떻게 해야 할까요? configure 실행중 외부 라이브러리 의존성에 관한 에러가 발생할 경우 `configure --help` 명령으로 옵션을 찾아보면 해답을 찾을 수 있습니다. 여기서는 '--disable-python'이란 옵션이 있네요.


    $ ./configure --help | grep python
      --disable-python        disable the python components
      --with-pythonlibs=ldflags
                              specify python libraries (-lpthread -lm -ldl
                              -lpython2.4)
      --with-pythonincludes=Cflags
                              specify python C header files
                              (-I/usr/include/python)


configure에 '--disable-python'옵션을 적용해서 다시 시도해 봅니다.


	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux --disable-python
	$ make


configure가 완료되었고, 컴파일도 성공했군요. 컴파일된 결과물은 `./src/.libs/` 에 있습니다.


### 참고

- [http://omappedia.org/wiki/ALSA_Setup#ALSA_library](http://omappedia.org/wiki/ALSA_Setup#ALSA_library)


