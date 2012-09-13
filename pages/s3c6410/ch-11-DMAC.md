---
layout: page
title: "S3C6410 USER's MANUAL"
description: ""
---
{% include JB/setup %}

#11. DMA CONTROLLER

이 챕터는 6410 RISC 마이크로 프로세서의 DMA컨트롤러에 대해 설명한다.

##11.1 OVERVIEW

6410에는 DMA 컨트롤러가 포함되어있다. 각 DMA 컨트롤러는 8개의 전송채널로 구성되어있다. 각 채널은 AHBtoAXI 브릿지에 의하여 디바이스간에 데이터를 전송할 수 있다(AXI SYSTEM 버스와 AXI PERIPHERAL버스로 연결된). 즉, 각 채널이 아래 4가지 경우에 를 처리할 수 있다.

1. Source와 Destination은 SYSTEM 버스에 있는 경우.
1. Source는 SYSTEM 버스에 있고, Destination은 PERIPHERAL 버스에 있는 경우.
1. Source가 PERIPHERAL에 있고, Destination은 SYSTEM 버스에 있는 경우.
1. Source와 Destination 모두 PERIPHERAL 버스에 있는 경우.

6410 DMA 컨트롤러로 사용되는 것은 ARM PrimeCell DMA 컨트롤러 PL080이다. DMAC는 ARM전용으로 AMBA(Advanced Microcontroller Bus Architecture)와 호환되도록 개발 및 테스트되거 라이선스가 걸려있는 SoC(System-on-Chip) 주변장치이다. DMAC는 AMBA AHB module로써  AHB(Advanced High-performance Bus)와 연결한다.

DMA의 주요 장점은 DMA가 CPU의 개입없이 데이터를 전송할 수 있다는 것이다. DMA는 S/W에 의해서 초기화되거나, 내부 페리페럴의 요청에 의해서 초기화 될 수 있다. 또한 외부 PIN에 의해서도 초기화가 가능하다.

##11.2 FEATURES

DMA 컨트롤러는 다음과 같은 기능을 제공한다.

- 6410은 4개의 DMA 컨트롤러를 제공한다. 각 DMA 컨트롤러는 8개의 전송채널로 구성된다. 각 채널은 단방향 전송을 지원할 수 있다.
- 각 DMA 컨트롤러는 16개의 페리페럴 DMA 요청 라인(Line)을 제공한다.
- DMAC에 연결된 각 페리페럴은 `burst DMA 요청` 또는 `single DMA 요청` 으로 정해서 사용할 수 있다. DMA burst size는 DMAC를 프로그래밍하여 설정할 수 있다.
- Memory-to-memory, memory-to-peripheral, peripheral-to-memory, 그리고 peripheral-to-peripheral의 전송을 지원한다.
- DMA는 데이터를 뿌리거나 모을 때 링크드 리스트(linked list)를 사용한다.
- 하드웨어 DMA가 높은 우선순위로 전달된다. 각 DMA 채널은 고유한 하드웨어 우언순위를 가진다. DMA channel 0 는 가장 높은 우선순위를 가지고 channel 7은 가장 낮은 우선순위를 가진다. 만약 동시에 두개의 채널이 active되면 우선순위가 높은 것이 먼저 서비스된다.
- DMAC는 AHB slave interface를 통해서 DMA control register에 값을 써서 프로그래밍할 수 있다. 
- 두개의 AXI bus master는 AHB to AXI 브릿지를 통해서 데이터를 전송한다. 이 인터페이스는 DMA 요청이 active가 되면 데이터를 전송하기위해 사용된다.
- source와 destination의 주소는 증가하거나 증가하지 않을 수 있다.
- 프로그래밍 가능한 DMA burst size. DMA burst size는 데이터 전송의 효율성을 높이기 위해서 프로그램될 수 있다. 보통 burst size는 페리페럴의 FIFO 사이즈의 절반으로 설정한다.
- 내부적으로 채널당 4워드 크기의 FIFO가 있다.
- 8, 16, 32bit 크기의 처리량.
- DMA error와 DMA count interrupt는 분류되거나 묶여있다.
DMA error가 발생하면 프로세서로 인터럽트가 발생할 수 있다. 또는 DMA count가 0이 된다(보통의 경우 0은 전송완료를 뜻한다). 이를 위하여 3개의 인터럽트 요청 signal이 사용된다:
 - 전송이 완료되면 **DMACINTTC** 신호를 보낸다.
 - 에러가 발생하면 **DMACINTERR** 신호를 보낸다.
 - **DMACINTR** 는 **DMACINTTC**와 **DMACINTERR** 인터럽트 요청 신호를 하나로 묶은 것이다. **DMACINTR** 인터럽트 요청은 시스템의 인터럽트 컨트롤러 요청 입력이 모자를 경우  사용할 수 있다. 
- 인터럽트 마스킹. DMA error와 DMA count 인터럽트 요청은 마스크될 수 있다. 
- 저수준 인터럽트 상태(raw interrupt status). DMA error와 DMA count의 저수준 인터럽트 상태는 마스킹을 위하여 우선적으로 읽혀질 수 있다. 



##11.3 BLOCK DIAGRAM

![Figure 11 DMAC block diagram](/images/s3c6410/figure-11-1-dma-block-diagram.png)




##11.4 DMA SOURCES

6410은 64개의 DMA source를 아래 테이블과 같이 지원한다. 시스템 컨트롤러에 있는 SDMA_SEL 레지스터의 리셋값은 `0x0` 이고, SDMA 선택이라는 의미이다. 그래서 설정은 일반적인 DMA를 사용하기위해서 1로 설정해한다. 더 많은 정보는 시스템 컨트롤러 파트를 참고하기 바란다. 

![figure-11-dma-source.png](/images/s3c6410/figure-11-dma-source.png)



##11.5 DMA INTERFACE



###11.5.1 DMA REQUEST SIGNALS
DMA 요청 신호는 페리페럴이 데이터 전송을 요청하기 위해서 사용한다. DMA 요청 신호는 다음을 뜻한다.

- 하나의 word 또는 burst(다수의 word)의 데이터 전송을 요청할 때
- 데이터 패킷의 마지막 전송인 경우

DMA요청은 각 페리페럴을 위하여 DMA컨트롤러에게 아래와 같은 것들을 신호한다:

- DMACxBREQ: burst 요청 신호. 이경우 프로그램된 워드의 크기만큼 전송한다.
- DMACxSREQx: 단일 전송 요청 신호. 이 경우 하나의 word를 전송한다.




###11.5.2 DMA RESPONSE SIGNALS

DMA응답신호는 DMA요청신호로 시작된 전송이 완료되었는지를 나타냅니다. 또한 응답신호는 완료패킷이 전송됐는지 알리기위해 사용될 수 있습니다.

각 페리페럴을 위하여 DMA컨트롤러로가 보낸 DMA응답신호는 다음과 같다:

- DMACxCLRx: DMA clear 혹은 acknowledge 신호
- DMACxTC: DMA terminal count 신호

DMACxCLRx 신호는 DMA컨트롤러가 페리페럴이 보낸 DMA요청에 응답하기 위해서 사용된다.

DMACxTC 신호는 DMA컨트롤러가 페리페럴에게 DMA전송이 완료되었음을 알리기위해 사용된다. 



###11.5.3 TRANSFER TYPES

DMA컨트롤러가 지원하는 4가지 전송유형:

- memory-to-peripheral
- peripheral-to-memory
- memory-to-memory
- peripheral-to-peripheral

각 전송유형은 페리페럴과 DMA컨트롤러 어느쪽이든 흐름제어를 할 수 있다. 따라서 4가지 제어 시나리오가 존재한다. 



###11.5.4 Peripheral-to-memory transaction under DMA controller flow control

전송을 위해서(burst 크기의 배수는 아니다) 표 11-2에서 처럼 burst와 single요청 신호가 사용된다. 

![figure-11-2-p-to-m-transaction.png](/images/s3c6410/figure-11-2-p-to-m-transaction.png)

두 요청신호는 

![figure-11-3-m-to-p-transaction.png](/images/s3c6410/figure-11-3-m-to-p-transaction.png)



![figure-11-4-m-to-m-transaction.png](/images/s3c6410/figure-11-4-m-to-m-transaction.png)
![figure-11-5-p-to-p-transaction.png](/images/s3c6410/figure-11-5-p-to-p-transaction.png)










