# 1장 개관
## 이장의 목표
1. computer system의 일반적인 구성과 인터럽트의 역할을 기술
2. 현대 다중 처리기 컴퓨터 시스엠의 구성요소에 관해 기술
3. 사용자 모드에서 커널 모드로의 전환에 대해 설명
4. 다양한 컴퓨팅 환경에서 OS가 어떻게 사용되는지 논의
5. 무료 및 공개 소스 운영체제의 예를 제공 

### 1.1 운영체제가 할 일
컴퓨터 시스템은 대개 네가지 구성요소인 하드웨어, 운영체제, 응용 프로그램 및 사용자로 구분할 수 있다.
> OS : 다양한 사용자를 위해 다양한 응용 프로그램 간의 하드웨어 사용을 제어하고 조장
 그리고 컴퓨터 시스템이 동작할 때 이들 자원을 적절하게 사용할 수 있는 방법을 제공

(CPU,MEM,DISK,IO 등등 텀퓨터 시스템을 효율적이고 공정하게 운영할 수 있게 해주도록 자원을 할당 )

> 무어의 법칙 : 집적회로의 트랜지스터 수가 18개월마다 배가되는 것

### 1.2 컴퓨터 시스템의 구성
현대의 범용 컴퓨터 시스템은 하나 이상의 CPU와 구성요소와 공유 메모리 사이의 엑세스를 제공하는 **버스**를 통해
연결된 여러 장치 컨트롤러로 구성된다.

입출력 작어을 위해 device driver는 device controller에 적절한 register값을 적재
~~해서, 결국에, 컨트롤러는 어떻게 다시 device driver에 작업 완료 사실을 알리는가??
-> interrupt
가상적으로 모든 computer의 처리기의 일반적인 처리에 다른 모듈들이 인터럽트를 걸 수 있도록 허용해줌
int == H/w system bus를 통해 CPU에 어떤 일을 알리는 것
운영체제, h/w의 상호작용 방식의 핵심 부분이다.
int 쓰는 이유 = h/w와 운영체제의 상호작용 + 프로세서의 성능을 향상시키기 위해서

cpu int걸리면 cpu는 하는 일을 중단하고 즉시 고정된 위치로 실행을 옮긴다
--> ISR수행 + interrupt 핸들러
인터럽트는 빈번하게 발생하므로 빠르게 처리되기 위해서 ISR에 대한 포인터 테이블들이 있음(인터럽트 벡터)
ISR은 인터럽트 처리 후의 현재의상태를 복원해야 하며 중단되었던 연산이 그대로 다시 시작되어야 함

1. 루틴
device controller가 인터럽트 요청 라인에 신호를 줘서 int 발생시킴 : raise
cpu가 int를 포착 : catch ( 현재 수행중인 명령어는 수행 완료한 후에 , 그리고 catch 했다는 신호를 보냄)
int handler로 dispatch하고
핸들러는 장치를 서비스해서 int를 지운다 == clear
그리고 return from int하면 됨

2.최신에서는
중요한 것을처리하고 있을 대에는 int처리를 연기
적절한 int handler로 효율적으로 dispatch
int간의 우선순위를 따짐

저장 장치 구조
cpu : 메모리에서만 데이터(명령,code)를 load할 수 있으므로 실행하려면 먼저 프로그램을 메모리에 적재해야 함
바이트는 8비트이고, 워드는 해당 컴퓨터 아키텍쳐의 본연의 데이터 단위

main memory : 휘발성
보조 메모리 : 비 휘발성 ( HDD, NVM )
레지스터 캐시 RAM NVM HDD 3차 저장장치(오프라인)
NVM : flash mem

원하는 데이터가 cache에 있는걸 hit rate
cache에 올리는 기준 : 시간적/지역적 locality를 따져서 올림

메모리 : 휘발성 저장장치(레지스터 캐시 메모리 통칭 )
앞으로 NVS : 비휘발성 저장장치

입출력
interrput 방식의 I/O
프로세서가 
NVS IO와 같은 대량 데이터 이동에는 int를 통한 io보다 DMA(direct memory access)가 사용됨
DMA는 cpu 개입 없이 실행되므로 cpu는 다른 일을 처리할 수 있음

단일 처리기 -> 다중 처리기 시스템(mulitprocessor Systems )
프로세서가 N배가 될떄, 왜 처리량(throughput) 증가의 비율이 N이 아닌가?
여러 프로세서가 하나의 작업에 협력할 때 모든 프로세서가 올바르게 작동하기 위해서는 
오버헤드가 발생 + 공유 자원에 대한 경합

가장 일반적인 멀티 프로세서 시스템
SMP : 다 동등한 processor
오버헤드 + 경합이 적음 but 일이 한개의 processor에게 몰릴 수 있음
다중코어 : 하나의 칩에 두개 이상의 코어를 가지는 설계
코어 = cpu의 기본 계산 단위
프로세서 = 하나 이상의 cpu를 포함하는 물리적인 칩

계산 환경
전통적 계싼 : 포털
모바일 컴퓨팅 : 이동가능 + 가볍다는 물리적 특징
client server computing
현대 네트워크 구조는 서버, 클라이언트 배치를 특징
서버 : 계산 + 파일
계산 : 말그대로 계산이고 파일 : 생성/갱신/R/W을 함 ( 내용은 a~z까지로 다양함)

분산 시스템의 다른 구조는 피어 간 시스템
p2p = peer to peer
클라이언트와 서버가 서로 구별되지 않음, 시스템상의 모든 노드가 피어로 간주되고
node는 서비스를 요청/제공하냐에 따라서 clinet/server로 작동함
cli/ser 구조 : 서버가 병목으로 작용
피어 간 시스템 : 서비스가 네트워크에 의해 분산된 여러 노드에 의해 재공될 수 있음 -> 문제 해결된 상태
자세한 내용은 패스
클라우드 컴퓨팅 : 계산/저장 + 응용까지 네트워크를 통한 서비스로 제공하는 계산 유형

실시간 내장형 시스템 : real time Embedded System
특수목적을 가진 응용 프로그램을 수행시키는 형태
내장형 시스템은 거의 언제나 real time OS를 수행
잘 정의된, 고정된 deadline을 만족시키는것이 최우선(반드시 이루어져야 함, 그렇지 않으면 실패 )

free OS / Opensource OS : 다른 그룹의 사람들이 지지함
전자 : permission + obligation, 모든 무료 소프트웨어는 공개 소스이지만 일부 공개 소스 소프트웨어는 무료가 아님
reverse engineering

77~81 : 패스

88
운영체제 서비스
user interface(UI)제공 : 거의 모든 운영체제는 UI를 제공
GUI, touch screeen interface, CLI(command line interpreter)

프로그램 수행 , 입출력 연산 , 파일 시스템 조작, 통신, 오류탐지

리눅스에서의 CLI : shell에 명령어를 입력 ( 보통의 UNIX )
GUI : 마우스 기반 윈도 메뉴 시스템을 사용 ( 보통의 데스크탑)
터치스크린

시스템 콜
system call : OS에 의해 사용 가능하게 된 서비스에 대한 인터페이스를 제공
API : application programming interface에 따라 프로그램을 설계
(ex : unistd.h)
하나하나 시스템 콜을 부르기보다는 API에 따라 프로그래밍 하는 이유
같은 API를 지원하는 어느 시스템에서건 컴파일 되고 실행되기를 기대
하나하나 syscall 쓰기 보다는 API를 쓰는게 편함

kernel <-> 시스템콜 interface <-> API <-> programmer <-> program <-> user느낌으로 진행

syscall의 종류
프로세스 제어, 파일 조작, 장치 조작, 정보 유지보수, 통신과 보호로 묶을 수 있음

* 프로세스 제어
비정사종료되면 때떄로 메모리 덤프가 행해지고, 이를 디버거를 통해 문제의 원인을 파악 가능
~~108 : 여러 종류에 대한 간략한 설명

2.4는 패스
현대 computer의 hierarchy
h/w , OS, system service, application program

2.5 링커와 로더
링커 : object file들을 모아서 하나의 실행파일로 모아줌
로더 : 이진 실행 파일을 메모리에 적재하는 데에 사용됨

순서 : fork로 새 프로세스를 실행 -> exec로 로더를 호출, exec에 실행파일 이름을 전달
그 후 로더는 새로 생성된 프로세스 주소 공간을 이용하여 지정된 프로그램을 메모리에 적재한다

linking : static / dynamic
dynamic : 프로그램이 load 될 떄 동적으로 링크되고 적재될 수 있도록 relocation정보를 삽입함
-> 메모리 사용을 절약
ELF : executable and linkable format

각 OS 는 고유한 syscall 집합을 제공, 그래서 응용프로그램 하나로 여러가지 os에서 돌릴 수 없음
그런데도 그동안 가능했떤 이유?
1. 응용프로그램이 인터프리터 언어 (python / Ruby)로 작성된 경우
source program의 각 line을 읽어서 기계어 명령을 실행하고 해당 OS의 syscall을 호출
interpreter언어는 OS기능의 일부만 제공하므로 관련 응용 프로그램의 기능이 제한될 수 있다

2.  실행중인 응용 프로그램을 포함하고 있는 가상 머신을 가진 언어로 작성

3. 프로그래머가 기기/OS의 고유 이진 파일 또는 표준 언어 또는 API를 사용
112p 다시


114
OS 설계 및 구현
1. 설계 목표
system의 목표, 명세를 정의
이것은 매번 다르지만, 소프트웨어 공학 분야에서 개발된 일반적인 원칙들은 존재
한가지 중요한 원칙은 기법으로부터 정책을 분리
기법 : 어떤 일을 어떻게 할 것인가를 결정
정책 : 무엇을 할 것인가를 결정
구현 패스

2.8 운영체제 구조 : 패스
모놀라식 / 계층적

운영체제 빌딩과 부팅
OS는 보통 

운영체제 디버깅
디버깅 : h/w, s/w에서의 시스템의 오류를 발견하고 수정하는 행위
성능 문제는 버그로 간주되어서 병목현상을 제거하여 성능을 향상시키는 성능조절도 디버깅에 포함됨

2.10.1 장애 분석
만약 프로세스가 실패 ->OS가 오류 정보를 로그 파일에 기록
또한 OS는 core dump를 통해 차후 분석을 위해 파일로 저장
사용자 수준 프로세스 코드 디버깅은 매우 어려움
OS 커널을 디버깅하는건 훨씬 어려움
커널 장애는 crash, 프로세스 장애와 마찬가지로 크래시 덤프에 저장됨
두개의 디버깅(process / kernal )

2.10.2 성능 관찰 및 조정
성능 조정은 처리 병목 지점을 제거함으로써 성능을 향상시키려 한다고 언급
프로세스별 / 시스템 전체에 관해서
카운터 / 추적의 두가지 접근방식중 한가지를 사용할 수 있음

클릭
int 발생 -> 메모장으로 프로세스가 바뀜 -> 

카운터
OS가 일련의 카운터를 통해 호출된 시스템 콜 횟수 또는 네트워크 장치 또는 디스크에 수행된 작업 수와 같은
시스템 활동을 추적

추적
시스템 콜과 관련된 단계와 같은 특정 이벤트에 대한 데이터를 수집

BCC : 시스템을 위한 추적 기능을 제공되는 도구
와 이 진짜 머라는지 모르겠네

***3장
job = process
process = program in execution
프로세스의 현재 활동 상태 : PC값과 processor register의 내용으로 나타낸다
프로세스 : 크게 스택, 힙, 데이터, 텍스트 로 구성됨
text : 실행 코드
data : 전역변수
heap : 프로그램 실행 중에 동적으로 할당되는 메모리
stak : 함수를 호출할 때 임시 데이터 저장장소(매개변수 + 복귀주소, 전 proc의 register들...)
따라서 text,data는 실행시간동안 크기가 변하지 않음
같은 프로그램일지라도 다른 pid를 가진 process가 될 수 있고, 데이터,힙 스택은 다를 수 있다.

프로세스 상태
new : 새로 생성된 proc
ready : 프로세스가 프로세서에 할당되기를 기다림
run : 명령어들이 실행됨
waiting : 특전 event를 기다리고있음
terminated : 실행이 종료
하나의 처리기에서는, 한개의 proc만 실행될 수 있음

PCB : process control block == TCB
프로세스 상태, pid,PC(다음에 실행할 명령어 주소), register(int 발생시 저장되어야 함),
cpu 스케쥴링 정보(우선순위), open file list 

thread
단일 제어 스레드 : 프로세스가 한번에 한 가지 일만 수행할 수 있게 함
다중 프로그래밍의 목적 : CPU의 이용률을 최대화하기 위해서
시분할의 목적 : 각 프로그램이 실행되는 동안 사용자가 상호작용할 수 있도록 프로세스들 사이에서 cpu코어를 빈번하게 	
			교체하는 것
대부분의 프로세스를 2개로 구분하자면, I/O bound와 CPU bound 프로세스로 구분

swapping : 메모리에서 제거하고 디스크로 swap-out을 하고 필요할 때 swap-in을 함
-> 메모리가 초과 사용됭 가용공간을 확보해야 할 때 필요

context switch
int떄문에 context switch가 발생하면, 현재 실행중인 process의 context는 해당 PCB에 저장됨
state save / state restore

프로세스 : 트리 형태, 부모, 자식프로세스가 있음
각 proc = 고유한 pid를 가짐-> 식별자,index역할
리눅스에서 init proc == pid 1의 시스템 부팅시 생성되는 첫 번쨰 프로세스

좀비 ,고아 프로세스

제한된 메모리와 같은 자원 제약 때문에 모바일 OS는 기존 프로세스를 종료시켜야 할 때도 있음
이를위한 중요도 계층
foreground / visible / service / background / empty

IPC
proc가 스세템에서 실행중인 다른 프로세스들에 영향을 주거나 영향을 받으면 이것은 협력적이라 할 수 있음
협력적 <-> 독립적
협력을 지향하는 이유 : 정보 공유, 계산 가속화, 모듈성
IPC의 방법 : shared memory + message passing

shared mem을 사용하려면 영역이 필요함
그리고 쓰려는 proc들이 얘를 자신의 영역에 추가해야함
***생산자 소비자 패러다임
unbounded buf / bounded buf
unbounded : 소비자는 생산되기를 기다리고,생산자는 계속 생산
bounded : 소비자는 생산되기를 기다리고, 생산자는 비워지기를 기다림

message passing
동일한 주소 공간을 공유하지 않고도 프로세스들이 통신을 하고, 그들의동작을 동기화할 수 있도록 허용하는 기법을 제공
그래도 보내려면 communication link가 있어야함
mailbox, message queue, 

pipe 
두 프로세스가 통신할 수 있게 하는 전달자
네트워크간 통신은 sockey을 이용
TCP,UDP


4장
쓰레드 : CPU 이용의 기본 단위
tid,PC,register,stack으로 구성
코드, 데이터, open file table 등을 공유
전통적 : 단일 스레드
-> 현대의 다중 스레드

프로세스 생성 cost >> thread 생성 cost
그리고 mem 공간도 proc가 더많이 차지

암묵적 스레딩
다중 코어 처리의 지속적 성장에 따라 수백 또는 수천개의 스레드를 가진 응용 프로그램이 등장
개발자의 부담이 커짐 -> 스레디의 생성과 관리 책임을 응용 개발자에서 컴파일러와 실행시간 라이브러리에 넘겨주는 
암묵적 스레딩이 점점 널리 퍼지고 있음


TLS : 각 스레드가 자신만 접근할 수 있는 데이터를 가지려면, TLS에 저장하면됨
문맥교환/사용자 모드로 전환은 dispatcher에 의해서

FCFS : first come, first served -> FIFO
SJF(== shortest remaining time first) : throughut을 늘릴수 있음, 최소의 평균 대기 시간
round robin : 시간 할당량(time quantum / time slice )만큼의 단위동안 모두 serve해줌
priority scheduling : 가장 높은 우선순위를 가진 프로세스 먼저
주요한 문제 : starvation
-> 노화(aging)으로 해결가능하긴함
multilevel queue scheduling
먼지 모르겠네

soft real time OS / hard real time OS
RTOS에서의 스케쥴링 알고리즘
priority기반이고
rate monotonic : 주기에 따라서 ( 주기가 짧을 수록 우선순위 높아짐 ) -> 정적(우선순위 고정되어있음)
EFD : 마감시간이 빠를수록 우선순위가 높아짐 + 실행가능해지면 마감시간을 시스템에 알림 -> 동적
