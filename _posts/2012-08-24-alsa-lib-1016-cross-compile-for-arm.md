---
layout: post
title: "alsa-lib-1.0.16 cross compile for arm"
description: ""
category: Linux
tags: [Linux, alsa, arm, cross-compile]
---
{% include JB/setup %}


### 방법

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux
	$ make

만약, libpython이 없다면:

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux --disable-python
	$ make



### 경험담


첫번째 configure 에서 에러가 발생했다. `cannot find -lpython2.6` 뭐라는걸로 보아서 python 라이브러리가 필요한듯 하다. 

	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux
	
	<중략>
	
    /opt/arm/4.3.1-eabi-armv6/usr/bin-ccache/../lib/gcc/arm-samsung-linux-gnueabi/4.3.1/../../../../arm-samsung-linux-gnueabi/bin/ld: cannot find -lpython2.6
    collect2: ld returned 1 exit status
    make[3]: *** [smixer-python.la] 오류 1
    make[3]: Leaving directory `/home/ygpark/download/sound/alsa-lib-1.0.16/modules/mixer/simple'
    make[2]: *** [all-recursive] 오류 1
    make[2]: Leaving directory `/home/ygpark/download/sound/alsa-lib-1.0.16/modules/mixer'
    make[1]: *** [all-recursive] 오류 1
    make[1]: Leaving directory `/home/ygpark/download/sound/alsa-lib-1.0.16/modules'
    make: *** [all-recursive] 오류 1

</br>
</br>

tool-chain에 libpython이 없나보다. python을 안쓰는 방향으로 해법을 찾아보았다.


    $ ./configure --help | grep python
      --disable-python        disable the python components
      --with-pythonlibs=ldflags
                              specify python libraries (-lpthread -lm -ldl
                              -lpython2.4)
      --with-pythonincludes=Cflags
                              specify python C header files
                              (-I/usr/include/python)

</br>
</br>

다행히 configure의 help에서 `--disable-python`이란게 나왔다. 다시 configure 시도한다.


	$ CC=arm-linux-gcc ./configure --target=arm-linux --host=i686-linux --disable-python
	$ make


컴파일이 모두 완료되었다. 컴파일된 결과물은 `./src/.libs/` 에 존재한다.


### 참고

- [http://omappedia.org/wiki/ALSA_Setup#ALSA_library](http://omappedia.org/wiki/ALSA_Setup#ALSA_library)


