### 📌 시스템 콜(System Call)란?
---
![image](https://github.com/amazinguss/cs_study/assets/39427152/dec3b4c4-4bf7-4bad-b617-743047651e0e)
- 운영 체제는 다양한 서비스를 수행하기 위해 하드웨어를 직접적으로 관리한다.
- 반면 **응용 프로그램**은 **운영 체제가 제공하는 인터페이스를 통해서만 자원을 사용**할 수 있다.
- 즉, 시스템 콜은 **운영 체제의 커널이 제공하는 서비스를 응용 프로그램의 요청**에 따라 **커널에 접근하기 위한 인터페이스**이다. 

<br>

### 📌 그렇다면 왜 시스템 콜이 필요할까?
---
![image](https://github.com/amazinguss/cs_study/assets/39427152/aa929fa1-56c2-4cc1-9932-fbb85044c4b2)
- 유저 레벨의 프로그램은 유저 레벨의 함수들만으로는 많은 기능이 필요하기 때문이다.
- 응용 프로그램으로 대표되는 **유저 프로세스(User Process) 유저 모드에서는 수행**할 수 없다.
- 그렇기 때문에 커널에 관련된 것은 **커널 모드로 전환**한 후에 해당 작업을 **수행할 수 있는 권한**이 생긴다.
- 커널 모드를 통한 작업은 반드시 **시스템 콜(System Call)을 수행**하도록 설계되어 있다.
- **해커가 피해를 입히기 위해 악의적으로 시스템 콜을 사용하는 경우나 초보 사용자가 하드웨어 명령어를 잘 몰라서 아무렇게 함수를 호출했을 경우** 시스템 전체를 망가트릴 수 있기 때문에 **권한이 필요**하다.
- 이런 명령어는 커널 모드에서만 실행할 수 있게 설계되었고, **유저 모드에서 시스템 콜을 호출할 경우 운영 체제에서 불법적인 접근**이라고 여기고 **trap을 발생**시킨다.

<br>

### 📌 시스템 콜의 유형은?
---
#### 1) 프로세스 제어 (Process Control)
- 프로세스 생성(create process)
- 끝내기(end), 중지(abort)
- 적재(load), 실행(execute)
- 프로세스 속성 획득과 설정(get process attribute and set process attribute)
- 시간 대기(wait time)
- 사건 대기(wait event)
- 사건을 알림(signal event)
- 메모리 할당 및 해제 (malloc, free)

#### 2) 파일 조작 (File Manipulation)
![image](https://github.com/amazinguss/cs_study/assets/39427152/ff3f4af2-d501-4a74-898e-5470819df5a1)
- 파일 생성 및 삭제 (create, delete)
- 열기(open), 닫기 (close)
- 읽기 (read), 쓰기 (write), 위치 변경(reposition)
- 파일 속성 획득 및 설정 (get file attribute and set file attribute)

#### 3) 장치 관리 (Devide Management)
- 장치를 요구 및 방출 (request devices and release devices)
- 읽기, 쓰기, 위치 변경
- 장치 속성 획득 및 속성 설정
- 장치의 논리적 부착 (attach), 분리 (detach)

#### 4) 정보 유지 (Information Maintenance)
- 시간과 날짜의 설정과 획득 (time)
- 시스템 데이터의 설정과 획득 (daate)
- 프로세스 파일, 장치 속성의 획득과 설정
- getpid(), alarm(), sleep()

#### 5) 통신 (Communicate)
- 통신 연결의 생성 및 제거
- 메시지의 송신 및 수신
- 상태 정보 전달
- 원격 장치의 부착 및 분리
- pipe(), shm_open(), mmap()

![image](https://github.com/amazinguss/cs_study/assets/39427152/4992508a-7e10-494c-833d-b760a60969d9)

  
