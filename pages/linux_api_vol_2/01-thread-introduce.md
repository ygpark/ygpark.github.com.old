---
layout: page
title: "리눅스 API의 모든 것 Vol.2"
description: ""
---
{% include JB/setup %}

목차

1. [README](/pages/linux_api_vol_2/00-readme.html)
1. [스레드: 소개](/pages/linux_api_vol_2/01-thread-introduce.html)
1. [스레드: 스레드 동기화](/pages/linux_api_vol_2/02-thread-sync.html)
1. [스레드: 스레드 안전성과 스레드별 저장소](/pages/linux_api_vol_2/03-thread-safety-and-repository.html)
1. [스레드: 스레드 취소](/pages/linux_api_vol_2/04-thread-cancel.html)
1. [스레드: 기타 세부사항](/pages/linux_api_vol_2/05-thread-details.html)
1. [프로세스 간 통신 개요](/pages/linux_api_vol_2/06-ipc.html)
1. [파이프와 FIFO](/pages/linux_api_vol_2/07-pipe-fifo.html)
1. [시스템 V IPC 소개](/pages/linux_api_vol_2/08-system-v-ipc.html)
1. [시스템 V 메시지 큐](/pages/linux_api_vol_2/09-system-v-message-queue.html)
1. [시스템 V 세마포어](/pages/linux_api_vol_2/10-system-v-semaphore.html)
1. [시스템 V 공유 메모리](/pages/linux_api_vol_2/11-system-v-shared-memory.html)
1. [메모리 매핑](/pages/linux_api_vol_2/12-memory-mapping.html)
1. [가상 메모리 오퍼레이션](/pages/linux_api_vol_2/13-virtual-memory-operation.html)
1. [POSIX IPC 소개](/pages/linux_api_vol_2/14-posix-ipc.html)
1. [POSIX 메시지 큐](/pages/linux_api_vol_2/15-posix-message-queue.html)
1. [POSIX 세마포어](/pages/linux_api_vol_2/16-posix-semaphore.html)
1. [POSIX 공유 메모리](/pages/linux_api_vol_2/17-posix-shared-memory.html)
1. [파일 잠금](/pages/linux_api_vol_2/18-file-lock.html)
1. [소켓: 소개](/pages/linux_api_vol_2/19-socket-introduction.html)
1. [소켓: 유닉스 도메인](/pages/linux_api_vol_2/20-socket-unix-domain.html)
1. [소켓: TCP/IP 네트워크 기초](/pages/linux_api_vol_2/21-socket-tcp-ip-network.html)
1. [소켓: 인터넷 도메인](/pages/linux_api_vol_2/22-socket-internet-domain.html)
1. [소켓: 서버 설계](/pages/linux_api_vol_2/23-socket-design-server.html)
1. [소켓: 고급 옵션](/pages/linux_api_vol_2/24-socket-advanced-option.html)
1. [터미널](/pages/linux_api_vol_2/25-terminal.html)
1. [대체 I/O 모델](/pages/linux_api_vol_2/26-alternative-io-model.html)
1. [가상 터미널](/pages/linux_api_vol_2/27-virtual-terminal.html)

<hr/>

#스레드: 소개

##1.1 개요

생략

##1.2 Pthreads API의 세부 배경 지식

#### Pthreads 데이터형

<table border="1px">
<tr>	<td align="center">데이터형</td>					<td align="center">설명</td> </tr>
<tr>	<td>pthread_t</td>				<td>스레드 ID</td> </tr>
<tr>	<td>pthread_mutex_t</td>		<td>뮤텍스</td> </tr>
<tr>	<td>pthread_mutexattr_t</td>	<td>뮤텍스 속성 객체</td> </tr>
<tr>	<td>pthread_cond_t</td>			<td>조건 변수</td> </tr>
<tr>	<td>pthread_condattr_t</td>		<td>조건 변수 속성 객체</td> </tr>
<tr>	<td>pthread_key_t</td>			<td>스레드 고유 데이터의 키(key)</td> </tr>
<tr>	<td>pthread_once_t</td>			<td>1회 초기화 제어 문맥</td> </tr>
<tr>	<td>pthread_attr_t</td>			<td>스레드 속성 객체</td> </tr>
</table>

	주의사항: 이들 변수들을 `==`연산자로 비교하면 안된다.

####스레드와 errno

본문의 내용만 읽고는 리눅스에서 errno를 쓰라는 말인지 쓰지 말라는 말인지 이해하기 힘들었다. 그래서 이해를 돕기위한 테스트프로그램을 작성해 보았다. 만약 현재 시스템이 스레드별로 errno를 공유하는지 아닌지를 확인하고 싶으면 이 예제가 도움이 될 것이다. 결과를 보면 알겠지만 스레드별로 errno가 다르게 찍히는 것을 알 수 있다. 

	  1 #include <stdio.h>
	  2 #include <unistd.h>
	  3 #include <pthread.h>
	  4 #include <errno.h>
	  5 
	  6 pthread_t tid1;
	  7 pthread_t tid2;
	  8 
	  9 void* thread(void*)
	 10 {
	 11     char str_id[256]={0};
	 12     if(pthread_equal(tid1, pthread_self()) == 0) {
	 13         sprintf(str_id, "[1] ");
	 14         errno=30;
	 15     } else if(pthread_equal(tid2, pthread_self()) == 0) {
	 16         sprintf(str_id, "[2] ");
	 17         errno=3;
	 18     }
	 19 
	 20     while(1) {
	 21         perror(str_id);
	 22         usleep(100000);
	 23     }
	 24     pthread_exit(NULL);
	 25     return 0;
	 26 }
	 27 
	 28 int main()
	 29 {
	 30     pthread_create(&tid1, NULL, thread, NULL);
	 31     pthread_create(&tid2, NULL, thread, NULL);
	 32     pthread_join(tid1, NULL);
	 33     pthread_join(tid2, NULL);
	 34 }
	
	결과:
	
	[2] : No such process
	[1] : Read-only file system
	[2] : No such process
	[1] : Read-only file system
	[2] : No such process
	[1] : Read-only file system
	[2] : No such process
	[1] : Read-only file system
	[2] : No such process
	[1] : Read-only file system
	[2] : No such process
	[1] : Read-only file system


####Pthreads 함수의 리턴값

시스템 호출과 일부 라이브러리의 함수에서는 일반적으로 성공하면 0을 리턴하고 실패하면 -1을 리턴하고 errno를 설정해 에러를 알린다. 하지만 Pthreads 함수는 성공하면 0을 리턴하고 실패하면 양수를 리턴한다. 이 양수값은 전통적인 유닉스 시스템 호출에서 errno에 설정되는 값과 같다.

멀티스레드 프로그램에서는 error 참조는 lvalue를 리턴하는 함수를 호출하는 매크로로 정의되기 때문에 error의 사용을 지양해야한다.

lvalue가 언듯 이해되지 않아 인용한다. 써놓고 보니 이해가 되는 것 같다.

	C에서 lvalue란 저장 공간을 가리키는 식(expression)을 말한다. lvalue의 가장 흔한 예는 변수 식별자다. 일부 연산자도 연산 결과로 lvalue를 내놓는다. 예를들어, p가 저장 공간을 가리키는 포인터라면 *p는 lvalue다. POSIX 스레드 API에서 errno는 스레드별 저장 공간을 가리키는 포인터를 리턴하는 함수로 재정의 되어있다(Vol. 2의 3.3절 참조)
	
	< 인용: 리눅스 API의 모든 것 Vol. 2 >

결론적으로, 이 절에서는 'errno의 사용을 자제하라'고 설명하고있다.



####Pthreads 프로그램 컴파일하기

이 절의 타이틀대로 컴파일하기 위해서 본문에서는 제시하는 방법은 `cc -pthread` 옵션으로 컴파일하라는 것이다. 혹은, CFLAGS에 `-D_REENTRANT`를 넣고 LDFLAGS에 `-lpthread` 옵션을 넣는 것이었다. 이 글을 읽고 `_REENTRANT`에 대해서 확실이 알게 된 것 같다.

	cc -pthread옵션이 가지는 효과
	
	- `_REENTRANT` 프리프로세서 매크로가 정의된다. 이를 통해 몇몇 `재진입 가능 함수` 선언을 쓸 수 있게 된다.
	- 프로그램이 libpthread 라이브러리와 링크된다 (-lpthread와 같다).
	
	< 인용: 리눅스 API의 모든 것 Vol. 2 >

인용한 위 구절에서 `재진입 가능 함수`란 무엇일까? 이 말은 `재진입 가능 함수` = `Thread Safe 함수` 라는 뜻으로 해석된다. [위키피디아에서 재진입성](http://ko.wikipedia.org/wiki/%EC%9E%AC%EC%A7%84%EC%9E%85%EC%84%B1)을 참고 바란다.

다음으로 `_REENTRANT` 매크로는 무슨일을 할까? `_REENTRANT` 키워드는 재진입 가능한 함수를 사용하라는 뜻이다. 구글링해 본 결과 과거에는 재진입 가능한 함수와 아닌 함수가 공존했었던 것 같다. 그래서 `_REENTRANT` 키워드를 써서 컴파일러에게 재진입 가능한 함수를 사용하라고 지시하는 것이다. 하지만 무조건 이 키워드에만 의존하지 말고 man page에 thread safe한 함수라고 명시되어 있는지 확인하고 써야한다.

생각해보니 예전에 썼던 외부 라이브러리(OpenCV)가 알 수 없는 에러를 일으켜서 고생한 적이 있었다. 그때는 컴파일러를 교체해서 어떻게 해결은 되었지만 이제와서 생각해보면 외부 라이브러리의 함수들이 재진입 불가 함수였던게 아닐까 조심스럽게 추측해 본다. 만약 그런 경우였다면 mutex로 임계구역을 설정해서 문제를 해결할 수 있을 것 같은 생각이 든다.




##1.3 스레드 생성

	#include <pthread.h>

	int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
					   void *(*start_routine) (void *), void *arg);
	
	성공하면 0을 리턴하고, 에러가 발생하면 에러 번호(양수)를 리턴한다.

너무 익숙한 함수라 자세한 설명은 생략한다.


##1.4 스레드 종료

	#include <pthread.h>

	void pthread_exit(void *retval);

스레드는 다음 방법중 하나로 종료된다.

- 스레드의 시작 함수가 스레드의 리턴값을 지정하며 return을 수행한다.
- 스레드가 pthread_exit()를 호출한다.
- 스레드가 pthread_cancel()을 통해 취소된다. (4.1절에서 설명)
- 스레드 중 아무나 exit()를 수행하거나, 주 스레드가(main() 함수에서) return을 수행해서, 프로세스 내의 모든 스레드가 즉시 종료된다.

첫번째 방법은 안될꺼라 생각하고 예제를 만들었다. 하지만 필자의 예상은 보기좋게 빗나갔다. 이제껏 pthread_exit()함수를 사용했었는데, 왠지 사기당한 기분이다. 

예제에서 (void*) 타입으로 정수를 주고받는데 이것이 가능한 이유는 포인터를 4byte 일반변수처럼 사용했기 때문이다.

	  1 #include <stdio.h>
	  2 #include <pthread.h>
	  3 
	  4 pthread_t tid1;
	  5 
	  6 void* thread(void*)
	  7 {
	  8     return (void*)5;
	  9 }
	 10 
	 11 int main()
	 12 {
	 13     void *res;
	 14     pthread_create(&tid1, NULL, thread, NULL);
	 15     pthread_join(tid1, &res);
	 16     printf("res = %ld\n", (long)res);
	 17 }
	
	결과:
	
	res = 5


##1.5 스레드 ID

pthread_t는 우연히도 unsigned long으로 정의되어 있지만, 구조체일 수도 있다. 따라서 이식성 있게 코드를 작성하려면 pthread_equal를 사용해야 한다. 또한 프로세스별로 스레드 ID가 고유한 경우도 있고 아닌경우도 있으므로 주의하자.

	#include <pthread.h>

	pthread_t pthread_self(void);

	호출 스레드의 스레드 ID를 리턴한다.

.

	#include <pthread.h>

	int pthread_equal(pthread_t t1, pthread_t t2);

	t1과 t2가 같으면 0이 아닌 값(참)을 리턴하고, 그렇지 않으면 0(거짓)을 리턴한다.



##1.6 종료된 스레드와 조인하기

	#include <pthread.h>
	
	int pthread_join(pthread_t thread, void **retval);
	
	성공하면 0을 리턴하고, 에러가 발생하면 에러번호(양수)를 리턴한다.

'스레드 분리'되지 않은 스레드는 조인해야 한다. 그렇지 않으면 좀비 프로세스와 비슷한 스레드가 생겨 시스템 자원을 낭비하게되고, 스레드도 더이상 만들 수 없는 상황이 생긴다.

그렇다면 '스레드 조인'할 필요가 없는 스레드들은 '스레드 분리'를 버릇처럼 하는 것이 좋은 것일까? 생각해 보자.




##1.7 스레드 분리하기

기본적으로 스레드는 조인할 수 있다. 하지만 리턴값에 관심이 없고 종료했을 때 시스템이 자동으로 스레드를 제거하고 뒷정리하기를 바랄 때도 있다. 이 경우, pthread_detach() 호출을 통해 해당 스레드를 분리detach 할 수 있다.

	#include <pthread.h>
	
	int pthread_detach(pthread_t thread);

	성공하면 0을 리턴하고, 에러가 발생하면 에러 번호(양수)를 리턴한다.


pthread_detach()의 사용 예로, 스레드는 다음과 같은 호출을 통해 스스로를 분리할 수 있다. 

	pthread_detach(pthread_self());

스레드가 일단 분리되면 더이상 pthread_join() 으로 리턴 상태를 얻을 수 없고, 다시 조인할 수 있게 만들 수 없다. 단, 스레드를 분리해도 다른 스레드에서 exit()를 호출하거나 주 스레드(main()함수)에서 return을 수행하면 함께 종료된다. 다시말하면, pthread_detach()는 단순히 스레드가 종료된 뒤에 일어날 일들을 제어하는 것이지, 종료되는 방법이나 시기를 제어하는 게 아니다. (만약 그렇다면 IPC를 사용하길 바란다.)





##1.8 스레드 속성

필자는 스레드 속성을 실무에서 써본적이 없다. 바꿔말하면, 일반적인 환경에서는 기본값으로도 충분히 Pthreads를 사용하는데 어려움이 없다는 말이다. 그래도 혹시 모르니까 어떤 것들이 있는지만 알아보고 넘어가자.

<ul>
	<li>스레드 스택의 위치와 크기</li>
	<li>스레드 스케줄링 정책과 우선순위</li>
	<li>스레드가 조인할 수 있는지 또는 분리됐는지에 대한 정보</li>
</ul>

예제코드다. 굳이 실행시켜 볼 필요는 없을 것 같다. 

	pthread_t thr;
	pthread_attr_t attr;
	int s;

	s = pthread_attr_init(&attr);
	if (s != 0)
		errExitEN(s, "pthread_attr_init");
		
	s = pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
	if (s != 0)
		errExitEN(s, "pthread_attr_setdetachstate");

	s = pthread_create(&thr, &attr, threadFunc, (void *) 1);
	if (s != 0)
		errExitEN(s, "pthread_create");

	s = pthread_attr_destroy(&attr);
	if (s != 0)
		errExitEN(s, "pthread_attr_destroy");



##1.9 스레드와 프로세스의 비교

####스레드의 장점

<ul>
	<li>스레드 간에는 데이터 공유가 쉽다.</li>
	<li>스레드 생성이 프로세스 생성보다 빠르다.</li>
</ul>

####스레드의 약점

<ul>
	<li>스레드 간에는 데이터 공유가 쉽다.</li>
	<li>스레드 생성이 프로세스 생성보다 빠르다.</li>
	<li>호출하는 함수가 Thread-Safe한지 또는 Thread-Safe하게 호출하는지 확인해야 한다.</li>
	<li>한 스레드의 버그가 프로세스 내의 모든 스레드에 피해를 줄 수 있다. </li>
	<li>각 스레드가 호스트 프로세스의 유한한 가상 주소 공간을 사용하려고 경쟁한다.</li>
</ul>


####기타
<ul>
	<li>멀티스레드 응용 프로그램에서의 시그널 처리는 세심한 설계가 필요하다. (시그널을 피하는 것이 좋다)</li>
	<li>멀티스레드 응용 프로그램에서은 하나의 프로그램으로만 동작한다. 멀티 프로세스 응용 프로그램은 여러개로 동작한다.</li>
	<li>스레드는 데이터 외의 정보도 공유할 수 있다. (파일 디스크리터, 시그널 속성, 현재 작업 디렉토리, 사용자/그룹 ID)</li>
</ul>



##1.10 정리

생략


##1.11 연습문제

	1-1 스레드가 다음 코드를 실행하면 어떤 결과가 발생하는가?

	pthread_join(pthread_self(), NULL);

	리눅스에서 실제로 무슨 일이 일어나는지 보여주는 프로그램을 작성하라. 스레드 ID를 담고 있는 변수 tid가 있다면, 스레드가 위의 실행문과 동일한 `pthread(tid, NULL)` 호출을 하지 않도록 어떻게 막을 것인가?
	
	<정답>
	리눅스에서는 EDEADLK(35:Resource deadlock avoided)을 리턴한다. 또는 다른 시스템의 경우 데스락이 걸릴 수 있다.
	
	이를 회피하려면 다음과 같은 방법을 사용해야 한다. 
	
	if(!pthread_equal(pthread_self(), tid)
		pthread_join(tid, NULL);
			
		

	1-2 에러 검사와 여러가지 변수 및 구조체 선언이 없다는 점을 제외하고, 다음 프로그램의 문제는 무엇인가?
	
	static void *
	threadFunc(void *arg)
	{
		struct someStruct *pbuf = (struct someStruct *)arg;
		/* 'pbuf'가 가리키는 구조체를 가지고 어떤 작업을 한다.
	}
	
	int
	main(int argc, char *argv[])
	{
		struct someStruct buf;
		
		pthread_create(&thr, NULL, threadFunc, (void *) &buf);
		pthread_exit(NULL);
	}
	
	
	<정답>
	실제로 이렇게 프로그램하지는 않을테지만, 필자의 생각으로는 응용 프로그램이 시작되자마자 종료될 것이라고 생각했었다. 하지만 실제로 주 스레드는 죽고 thr 스레드는 계속 살아있는 일이 일어났다. 잠시동안 테스트 프로그램을 작성할 때 요긴하게 써먹을 수 있겠다고 생각했지만 잘못된 생각이었다. thr 스레드는 종료된 주 스레드의 스택을 계속 사용하기 때문에 알 수 없는 결과를 초래할 수 있다고 한다.
	
	
	