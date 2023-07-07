# 인터럽트란?
- CPU가 프로그램을 실행하고 있을 떄, 입출력 하드웨어 등의 장치에 예외상황이 발생하여 처리가 필요한 경우 CPU 에게 알려 처리할 수 있도록 하는 기술
- 우선적으로 처리해야할 일이 발생했을 때 그것을 처리하고 원래 동작으로 돌아온다.


# 인터럽트 종류
## 하드웨어 인터럽트
- 일반적으로 인터럽트를 이르는 말
- CPU 외부로부터 인터럽트 요구 신호에 의해 발생되는 인터럽트
- Maskable interrupt, Non-maskable interrupt 가 있다. (Interrunpt Mask가 가능)
    - Maskable interrupt
       - Interrupt Mask(인터럽트가 발생하였을 때 요구를 받아들일지 말지 지정하는 것)가 가능
       - 인텔CPU 에서 INTR pin으로 신호가 들어옴
       - 입출력 장치가 제기하는 인터럽트 요청과 대부분의 인터럽트가 여기에 해당한다.
    - Non Maskable interrupt
       - Interrupt Mask가 불가능
       - 거부, 무시할 수 없음 (매우 중요함)
       - 정전, 하드웨어 고장 등 어쩔수없는 오류
       - 인텔 CPU에서 NMI pin으로 신호가 들어옴
### 하드웨어 인터럽트 종류
- 입출력 인터럽트 (I/O interrupt)
- 입출력 작업의 종료나 입출력 오류에 의해 CPU의 기능이 요청됨
- 정전,전원 이상 인터럽트(Power fail interrupt) 전원 공급의 이상
- 기계 착오 인터럽트(Machine check interrupt) CPU의 기능적인 오류
- 외부 신호 인터럽트(External interrupt) I/O 장치가 아닌 오퍼레이터나 타이머에 의해 의도적으로 프로그램이 중단된 경우

## 소프트웨어 인터럽트
  - CPU 내부에서 자신이 실행한 명령이나 CPU 명령 실행에 관련된 모듈이 변화하는 경우 발생
  - trap 또는 exception 이라고 한다.
  - 프로그램 오류에 의해 생기는 인터럽트
### 소프트웨어 인터럽트 종류
- 프로그램 검사 인터럽트(Program check interrupt)
  - 0으로 나누는 경우
  - OverFlow/UnderFlow
  - 페이지 부재
  - 부당한 기억장소의 참조
  - 등등
- SVC(Supervisor Call: 감시프로그램 호출)인터럽트
  - 사용자가 프로그램을 실행시키거나 supervisior을 호출하는 동작을 수행하는 경우
  - 프로그래머에 의해 코드로 짜인 감시 프로그램을 호출하는 방식
#### 😱 무슨 말인지 모르겠다
일반적인 응용프로그램은 User Mode 에서 실행되는데, 이 경우 직접 접근할 수 있는 자원에 대한 제한이 있다. 예를 들어 어떤 프로그램이 컴퓨터에 대한 통제권 코드를 갖고 있다고 할 때, 이런 것이 허락도 없이 수행될 수는 없다.
자원에 접근하는 명령어는 supervisior 만 실행할 수 있어야 하고, 이것이 Kernel Mode 가 된다.</br>
즉 응용프로그램은 Supervisior Call을 통해 허락을 맡은 후 Kernel Mode로 변경하고, 해당 명령을 실행 후 다시 User Mode로 변경하는 과정을 거친다. </br>
운영체제가 제공하는 서비스에 대한 프로그래밍 인터페이스를 사용하는 것이 System Call이고, System Call을 실행시키기 위한 CPU 명령어가 SVC이다.


#### 🧐 User Mode ? Kernel Mode?
##### User Mode
- 유저가 접근할 수 있는 영역을 제한적으로 두고, 프로그램의 자원에 함부로 침범하지 못하는 모드
- 우리가 코드를 작성하고, 프로세스를 실행하는 등의 행동을 하는 영역이다.
  
##### Kernel Mode
- 모든 자원에 접근, 명령을 할 수 있다.

<img width="556" alt="스크린샷 2023-07-02 오후 12 25 58" src="https://github.com/amazinguss/cs_study/assets/57309311/f134985f-b18f-47e1-9ca0-aa33da99e394">

      
#### 🧐 System Call?
운영 체제의 커널이 제공하는 서비스에 대해, 응용 프로그램의 요청에 따라 커널에 접근하기 위한 인터페이스이다.
우리가 일반적으로 사용하는 프로그램은 유저레벨로 유저 레벨의 프로그램은 유저레벨의 함수들 만으로 많은 기능을 구현하기 힘들기 때문에 커널에 도움이 반드시 필요하다. 반드시 Kernel에 관련된 것을 Kernel 모드로 전환한 후에 작업을 수행할 권한이 생긴다. Kernel 모드를 통한 작업은 반드시 **시스템 콜** 을 통해 수행해야한다.

##### 시스템 콜의 종류
- 프로세스 컨트롤
  - 프로셋 생성 및 종료
  - 메모리에 로드 및 실행
  - 메모리 할당
 - 파일 매니지먼트
   - 파일 생성, 삭제
   - 열기 및 닫기
   - 읽기 및 쓰기
- 등등

# 프로세스 상태로 보는 인터럽트 과정
<img width="716" alt="스크린샷 2023-07-02 오전 11 53 21" src="https://github.com/amazinguss/cs_study/assets/57309311/93c111c5-03d8-40c7-b6f9-340d1194032d">

- 프로세스가 실행중에 I/O event 가 발생하면 (게임 도중 로그인을 해야한다면)
- 실행중인 프로세스가 Waiting 상태로 가게된다.
- 입출력이 끝나면 인터럽트가 발생해서 완료되었다고 신호를 보낸다.
- 프로세스가 실행 대기 중인 Ready로 가게 되고 CPU 스케쥴링에 의해 실행된다.

# 인터럽트 과정
<img width="534" alt="image" src="https://github.com/amazinguss/cs_study/assets/57309311/ffe05abe-65b9-4834-b5bc-0a5a4002907b">

</br>

Process A 실행 중 디스크에서 어떤 데이터를 읽어오라는 명령을 받았다고 가정해보자.</br>
- process A는 system call을 통해 인터럽트를 발생시킨다.
- CPU는 현재 진행 중인 기계어 코드를 완료한다.
- 현재까지 수행중이었던 상태를 해당 process의 PCB(Process Control Block)에 저장한다. (수행중이던 MEMORY주소, 레지스터 값, 하드웨어 상태 등...)
- PC(Program Counter, IP)에 다음에 실행할 명령의 주소를 저장한다.
- 인터럽트 벡터를 읽고 ISR 주소값을 얻어 ISR(Interrupt Service Routine)로 점프하여 루틴을 실행한다.
- 해당 코드를 실행한다.
- 해당 일을 다 처리하면, 기존 수행중이던 레지스터를 복원한다.
- ISR의 끝에 IRET 명령어에 의해 인터럽트가 해제 된다.
- IRET 명령어가 실행되면, 대피시킨 PC 값을 복원하여 이전 실행 위치로 복원한다.

> IRET Interrupt Return
> 이전 테스크로 다시 돌아가는 어셈블리 명령어로 ISR의 마지막 명령어

> ISR Interrupt Service Routine
> 인터럽트 핸들러 Interrupt handler 라고도 함
> 인터럽트가 접수되면 각각의 인터럽트에 대응하여 특정 기능을 처리하는 기계어 코드 루틴(커널이 실행)
> ex) 키보드 자판을 눌러 키보드 인터럽트가 발생하면 이에 해당하는 인터럽트 서비스 루틴이 실행됨

> 인터럽트 벡터 Interrupt Vector
> 여러가지 인터럽트에 대해 해당 인터럽트 발생시 처리해야 할 루틴(ISR)의 주소를 보관하고 있는 공간
> 대부분의 CPU들은 인터럽트 벡터 테이블을 가지고 있음
> 인텔x86에서는 이를 IDT(Interrupt Descriptor Table)이라고 한다.
   
> PC (Program Counter)
> 레지스터의 구성요소로, 다음번에 실행할 명령어 주소를 기억한다.

# 인터럽트 우선순위
<img width="590" alt="스크린샷 2023-07-02 오전 11 17 00" src="https://github.com/amazinguss/cs_study/assets/57309311/b96c56ce-4d9d-4092-ab2e-7f56ece5abd1">
<img width="578" alt="스크린샷 2023-07-02 오전 11 17 21" src="https://github.com/amazinguss/cs_study/assets/57309311/dec9a89d-b46d-480e-bf9f-8ebe4506967d">


> 출처 </br>
> https://medium.com/@lazypanda43/%EC%9D%B8%ED%84%B0%EB%9F%BD%ED%8A%B8-%EC%A2%85%EB%A5%98%EC%99%80-%EC%B2%98%EB%A6%AC%EA%B3%BC%EC%A0%95%EA%B3%BC-%EC%9A%B0%EC%84%A0%EC%88%9C%EC%9C%84-c95c26909472 </br>
> https://doh-an.tistory.com/31</br>
> https://fjvbn2003.tistory.com/306</br>
> https://velog.io/@adam2/%EC%9D%B8%ED%84%B0%EB%9F%BD%ED%8A%B8
