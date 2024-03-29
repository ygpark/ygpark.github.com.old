---
layout: page
title: "전투 Linux Kernel 개발자 되기"
description: ""
---
{% include JB/setup %}

목차
1. [README](/pages/kernel/00-readme.html)
1. [Kernel 디버깅](/pages/kernel/chapter-kernel-debugging.html)

<hr/>



## JTAG Debugger로 디버깅하기

필자는 JTAG장비로 [제이앤디테크社](http://www.jndtech.com/)의 CodeViser를 사용합니다. 저는 JTAG장비 주로 두가지 용도로 사용합니다. 하나는 Boot Loader를 NAND Flash에 다운로드하고, 다른 하나는 Linux Kernel의 디버깅 용도로 사용하는 것입니다. JTAG은 아주 멋지고 편리한 디버거입니다. 어느정도냐면 Windows의 Visual Studio보다 훨씬 더 좋습니다 :D 몇몇사람들은 JTAG이 다운로드 전용인줄로만 알고 사용하고있어서 한편으로는 안타까울때도 있습니다.

### 디버그용 바이너리 컴파일하기

리눅스 커널 소스코드를 디버그용으로 컴파일하는법을 알아보겠습니다. 

CodeViser는 디버깅 정보가 dwarf2로 컴파일된 소스코드만 디버깅할 수 있습니다. 따라서 컴파일타임에  CFLAGS로 `-gdwarf-2` 를 옵션으로 사용해야만합니다.

아래는 [제이앤디테크社](http://www.jndtech.com/)의 질문답변란에 올라와 있는 내용입니다.

	Q:	리눅스 커널 이미지를 로드했는데 심볼리스트에 아무것도 안보이는데요.
	
	A:	CVD는 dwarf-2 형식의 ELF 파일을 지원합니다. 따라서 리눅스에서 빌드하는 경우,지원되지 않는 
		형식으로 ELF파일이 생성 된 경우 이미지의 심볼 정보가 나타나지 않을 수 있습니다. 
		
		리눅스 커널의 Makefile의 CFLAGS 의 설정정보를 확인하여 -gstab 이나 -ggdb 가 설정되어 있다면 
		삭제 해 주시고, -gdwarf-2 를 추가해 주신 후 다시 빌드해서 테스트 해 보시기 바랍니다.

 사실 리눅스 커널의 Makefile 안에서 FLAGS는 `KBUILD_CFLAGS`와 `KBUILD_AFLAGS`라는 이름으로  사용됩니다. 하나는 C언어용 CFLAG, 다른 하나는 Assembly언어용 AFLAGS입니다. 
 
 여기서는 둘 다 `-gdwarf-2`로 바꾸겠습니다.

 
	ifdef CONFIG_DEBUG_INFO
	KBUILD_CFLAGS   += -gdwarf-2
	KBUILD_AFLAGS   += -gdwarf-2
	endif
 
그리고 눈여겨볼 것이 하나 있는데 두 변수를 둘러싸고있는 `CONFIG_DEBUG_INFO`옵션입니다. 이것은 커널을 디버그모드로 빌드하기위한 옵션입니다. 이 옵션이 y로 설정되어있어야만 위의 FLAG가 적용됩니다. 옵션을 활성화시키려면 `make menuconfig`를 실행하고, 메인화면에서 `Kernel debugging`을 선택한 다음 `Compile the kernel with debug info`를 선택합니다.
 
	$ make menuconfig 

	Kernel hacking
	-> [*] Kernel debugging
	   -> [*] Compile the kernel with debug info

이제 저장하고 나가서 컴파일을 하면 `-gdwarf-2`가 적용된 zImage를 만들 수 있습니다.

참고로 `make menuconfig`의 메뉴에서 `CONFIG_XXX`이 어디에 있는지 아는 방법은 `/`키를 눌러서 `CONFIG_XXX`를 입력하는 것입니다. 또한 메뉴상에서 커서가 놓여있는 위치에서 `?`를 누르면 도움말을 볼 수 있습니다.



### CVD를 이용한 Kernel 디버깅

JTAG을 사용해서 Linux Kernel을 디버깅하는 방법을 알아보겠습니다.

디버깅을 위해서는 Linux Kernel의 소스코드와 `-gdwarf-2` 로 컴파일한 zImage 파일 그리고 vmlinux 파일이 필요합니다. zImage는 Target Board에서 사용되고, vmlinux와 소스코드는 CVD에서 디버깅 정보를 추적하기위해 사용됩니다.

보통은 소스코드를 리눅스 개발서버에서 컴파일하기 때문에 Windows에서 Linux로 접근을 한다면 samba로 연결해서 사용합니다. 여기서는 주제에서 벗어나므로 samba를 설치하는 방법은 설명하지 않겠습니다. 

준비가 되었다면 프로그램을 실행합니다.

![](/images/kernel/20120903-kernel-debug-01.png)

그림 1. CVD 프로그램.  메인화면

메뉴에서 `Program > Load` 을 선택하여 vmlinux 파일을 선택합니다. 이 때, 세가지 체크박스 `No-code`, `Multi-image`, `Keep-state`를 선택합니다. 파일을 로드하는 시간은 수초정도 걸립니다.

![](/images/kernel/20120903-kernel-debug-02.png)

그림 2. CVD 프로그램.  메뉴 > Program > Load

로드가 되었다면 파일이 디버깅 가능한 dwarf2인지 확인해야합니다. 메뉴에서 `Symbol > Program List`를 선택하여 확인할 수 있습니다.

![](/images/kernel/20120903-kernel-debug-03.png)

그림 3. CVD 프로그램.  메뉴 > Symbol > Program List

마지막으로 F5키를 눌러 Target Board에 연결하면 됩니다. 또는 상단의 Connect 아이콘을 클릭합니다.

![](/images/kernel/20120903-kernel-debug-04.png)

그림 4. CVD 프로그램. 메뉴 > Config > Interface

Target Board에 연결되면 현재 실행중인 라인에서 실행이 중지됩니다. 이 때, CVD는 메모리에서 실행되고있는 실제 assembly코드와 C소스코드를 매치시켜 보여줍니다.

![](/images/kernel/20120903-kernel-debug-05.png)

그림 5. CVD 프로그램. 

