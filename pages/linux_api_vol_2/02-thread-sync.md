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

##2.1 공유 변수 접근 보호: 뮤텍스

두 스레드에서 전역 변수를 잘못 증가시키는 예. 좁은 의미에서 보면 공유변수를 보호하기 위한 작업이고, 넓은 의미에서는 어떤 단위의 일(transaction)이 끝날때까지 보호한다는 의미도 가진다. 필자의 경험을 이야기 하자면 뮤텍스는 참 다용도로 활용될 수 있는 고마운 녀석이다.

	  1 #include <stdio.h>
	  2 #include <pthread.h>
	  3 #include <stdlib.h>
	  4 #include <unistd.h>
	  5 
	  6 pthread_t tid1, tid2;
	  7 static int glob = 0;
	  8 
	  9 void* thread(void* arg)
	 10 {
	 11     int cnt = *((int *)arg);
	 12     for(int i=0; i < cnt ; i++) {
	 13         glob++;
	 14     }
	 15     return NULL;
	 16 }
	 17 
	 18 int main(int argc, char *argv[])
	 19 {
	 20     int loop = (argc > 1) ? atoi(argv[1]) : 1000;
	 21     pthread_create(&tid1, NULL, thread, &loop);
	 22     pthread_create(&tid2, NULL, thread, &loop);
	 23     pthread_join(tid1, NULL);
	 24     pthread_join(tid2, NULL);
	 25     printf("glob: %d \n", glob);
	 26     exit(EXIT_SUCCESS);
	 27 }
	 
	결과:
	
	$ ./a.out 
	glob: 2000
	$ ./a.out 100000
	glob: 108221 
	$ ./a.out 100000
	glob: 113245 
	$ ./a.out 100000
	glob: 113365 
	$ ./a.out 100000
	glob: 112098 


##2.1.1 정적으로 할당한 뮤텍스

뮤텍스를 정적으로 초기화 하는 방법은 다음과 같다. 동적으로 초기화 할수도 있는데 2.1.5절에서 다룬다.

	pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;



##2.1.2 뮤텍스의 잠금과 풀림

	#include <pthread.h>

	int pthread_mutex_lock(pthread_mutex_t *mutex);
	int pthread_mutex_unlock(pthread_mutex_t *mutex);
	
			성공하면 0을 리턴하고 에러가 발생하면 에러 번호(양수)를 리턴한다.

####예제 프로그램

	  1 #include <stdio.h>
	  2 #include <pthread.h>
	  3 #include <stdlib.h>
	  4 #include <unistd.h>
	  5 #include <string.h>
	  6 
	  7 pthread_t tid1, tid2;
	  8 static int glob = 0;
	  9 pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;
	 10 
	 11 void* thread(void* arg)
	 12 {
	 13     int cnt = *((int *)arg);
	 14     int s;
	 15     for(int i=0; i < cnt ; i++) {
	 16         s = pthread_mutex_lock(&mtx);
	 17         if(s != 0) {
	 18             printf("pthread_mutex_lock(): %s \n", strerror(s));
	 19             exit(EXIT_FAILURE);
	 20         }
	 21         glob++;
	 22         s = pthread_mutex_unlock(&mtx);
	 23         if(s != 0) {
	 24             printf("pthread_mutex_unlock(): %s \n", strerror(s));
	 25             exit(EXIT_FAILURE);
	 26         }
	 27     }
	 28     return NULL;
	 29 }
	 30 
	 31 int main(int argc, char *argv[])
	 32 {
	 33     int loop = (argc > 1) ? atoi(argv[1]) : 1000;
	 34     pthread_create(&tid1, NULL, thread, &loop);
	 35     pthread_create(&tid2, NULL, thread, &loop);
	 36     pthread_join(tid1, NULL);
	 37     pthread_join(tid2, NULL);
	 38     printf("glob: %d \n", glob);
	 39     exit(EXIT_SUCCESS);
	 40 }
	결과:
	
	$ ./a.out 
	glob: 2000
	$ ./a.out 100000
	glob: 200000
	$ ./a.out 100000
	glob: 200000 

	
####pthread_mutex_trylock()과 pthread_mutex_timedlock()

	#include <pthread.h>

	int pthread_mutex_trylock(pthread_mutex_t *mutex);
	
			성공하면 0을 리턴하고 에러가 발생하면 에러 번호(양수)를 리턴한다.

.

	#include <pthread.h>
	#include <time.h>
	
	int pthread_mutex_timedlock(pthread_mutex_t *restrict mutex,
	const struct timespec *restrict abs_timeout);
	
			성공하면 0을 리턴하고 에러가 발생하면 에러 번호(양수)를 리턴한다.

Pthread API는 pthread_mutex_lock()함수의 두 가지 변종을 제공한다. 

pthread_mutex_trylock()함수는 뮤텍스가 현재 잠겨있으면 에러 EBUSY를 리턴하면서 실패한다는 점을 제외하고는 pthread_mutex_lock()과 같다.

pthread_mutex_timedlock()함수는 호출자가 뮤텍스 잠금을 획득하려고 기다리면서 수면을 취하는 점을 제외하고는 pthread_mutex_lock()과 같다. 호출자가 뮤텍스 잠금을 얻지 못한 상태로 abstime 시간이 지나면, pthread_mutex_timedlock()은 에러 ETIMEDOUT을 리턴한다.

대부분의 잘 설계된 응용 프로그램에서는 뮤텍스가 짧은 시간동안만 잠근다. 그러므로 pthread_mutex_trylock()와 pthread_mutex_timedlock()가 필요한 상황이 생기면 설계가 올바로 되어있나 검토해 볼 필요가 있다. 

##2.1.3 뮤텍스의 성능

뮤텍스의 성능은 대체로 빠른 편에 속한다.


##2.1.4 뮤텍스 데드락




