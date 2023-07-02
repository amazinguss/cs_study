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
일반적인 응용프로그램은 User Mode 에서 실행되는데, 이 경우 직접 접근할 수 있는 자원에 대한 제한이 있다. 예를 들어
어떤 프로그램이 컴퓨터에 대한 통제권 코드를 갖고 있다고 할 때, 이런 것이 허락도 없이 수행될 수는 없다.
자원에 접근하는 명령어는 Supervi

#### 🧐 User Mode ? Kernel Mode?

   
      



#### 🧐 System Call?
운영 체제의 커널이 제공하는 서비스에 대해, 응용 프로그램의 요청에 따라 커널에 접근하기 위한 인터페이스이다.
우리가 일반적으로 사용하는 프로그램은 유저레벨로 

> PC (Program Counter)
> 레지스터의 구성요소로, 다음번에 실행할 명령어 주소를 기억한다.

> 출처
> https://medium.com/@lazypanda43/%EC%9D%B8%ED%84%B0%EB%9F%BD%ED%8A%B8-%EC%A2%85%EB%A5%98%EC%99%80-%EC%B2%98%EB%A6%AC%EA%B3%BC%EC%A0%95%EA%B3%BC-%EC%9A%B0%EC%84%A0%EC%88%9C%EC%9C%84-c95c26909472
