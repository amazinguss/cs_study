# DeadLock
다중 프로그래밍, 여러개의 스레드에서 동시에 일을 진행할 때 발생하는 대표적인 **동시성 문제**중 대표 사례이다.
시스템적으로 한정된 자원을 여러 곳에서 사용하려고 할 때 발생한다. '교착 상태'라고도 하며 프로세스가 자원을 얻지 못해 다음 처리를 하지 못하는 상태이다.

![](https://velog.velcdn.com/images/soyeong125/post/30c3f2fe-bbab-45f3-9278-12714370f86b/image.png)

Thread2가 자원A를 점유한 상태에서 자원B가 필요하여 요청하고 , 
Thread3가 자원B를 점유한 상태에서 자원A가 필요해서 요청하는 상태이다.

서로의 자원을 놓지않고 다른 자원을 기다리면서 무한대기상태로 진입하게 되고 이런 상태를 **DeadLock** 이라고 한다.


## DeadLock이 발생할 수 있는 상황
- 멀티프로그래밍 환경에서 한정된 자원을 사용하려고 경쟁하는 상황이 발생할 수 있다.
- 어떤 프로세스가 자원을 요청했을 자원을 사용할 수 없는 상황이 발생하여 프로세스는 대기상태로 들어가게 된다.대기 상태로 들어간 프로세스들이 실행 상태로 변경될 수 없는 상태이다.

## DeadLock 발생 조건
한 시스템 내에서 아래 4가지 조건이 동시에 성립할 때 발생한다.
- 상호 배제
- 점유 대기
- 비선점
- 순환대기

### 1.  상호 배제 (Mutual exclusion)
- 자원은 한 번에 한 프로세스만이 사용할 수 있어야 한다.
- 상호 배제는 **임계 구역** 에서 구현한다.
- 임계 구역에서 상호 배제를 보장하는 알고리즘은 여러가지가 있다.
    1. 데커의 알고리즘
    	- flag와 turn 변수를 통해 프로세스 중 누가 임계 영역에 진입할 건지 다음차례가 누구인지 나타낸다.
    2. 피터슨 알고리즘
    	- 데커 알고리즘과 유사하나 다른 프로세스/스레드에게 진입 기회를 양보한다.
    3. 다익스트라 알고리즘
    4. 램퍼드 베이커리 알고리즘
    	- 여러 개의 프로세스, 스레드에 대한 처리가 가능하다. 가장 작은 수의 번호표(프로세스에 부여된 고유 번호, 우선순위)를 가지고 있는 프로세스가 임계영역에 진입한다.
- 뮤텍스와 세마포어를 통해 상호 배제 조건을 달성할 수 있다.
    ```
	do {
    wait(mutex);  // 입장 구역
    // 임계 구역
    signal(mutex);  // 퇴장 구역
    // 나머지 구역
	}
	```
- 상호배제는 소프트웨어적 솔루션이기 때문에 단점이 존재한다. 속도가 느리고 구현이 복잡하다. busy waiting이 발생하여 비효율적이다. 계속 무한 루프를 돌면서 최대한 다른 스레드에게 CPU를 양보하지 않는다. 프로세스 실행중 선점될 경우 오버헤드가 발생한다.


### 2. 점유 대기 (Hold and wait)
최소한 하나의 자원을 점유하고 있으면서 다른 프로세스에 할당되어 사용하고 있는 자원을 추가로 점유하기 위해 대기하는 프로세스가 있어야 한다.

### 3. 비선점 (No preemption)
다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 빼앗을 수 없어야 한다.
비선점 스케줄링을 통해 구현하는데, 프로세스는 자원을 스스로 반납할 때까지 계속 자원을 사용할 수 있다.

### 4. 순환 대기 (Circular wait)
- 프로세스의 집합에서 순환 형태로 자원을 대기하고 있어야한다.
- 프로세스의 집합 {P0, P1, ,…Pn}에서 P0는 P1이 점유한 자원을 대기하고 P1은 P2가 점유한 자원을 대기하고 P2…Pn-1은 Pn이 점유한 자원을 대기하며 Pn은 P0가 점유한 자원을 요구해야 한다.
![](https://velog.velcdn.com/images/soyeong125/post/c282a9f0-4aa9-4d6b-82ae-90baf7bc131b/image.png)


점유 대기 조건과 비선점 조건을 만족하면 순환 대기 조건이 따라온다.
    
## DeadLock 처리
### 1. 예방(Prevention)
교착 상태 발생 조건 중 하나를 제거함으로써 발생하기 전에 예방한다.
단점은 자원낭비가 심하고 비용이 많이 든다.

- 상호 배제 부정
여러 개의 프로세스가 공유 자원을 사용할 수 있도록 한다.
- 점유 대기 부정
프로세스가 실행되기 전 필요한 모든 자원을 할당한다. 자원이 점유되지 않을 때만 다른 프로세스에서 요구할 수 있도록 한다.
(자원과다 사용으로 인한 효율성 문제, 프로세스가 요구하는 자원을 파악하는 비용, 자원에 대한 내용을 저장,복원하기 위한 비용, 기아상태, 무한 대기상태) -> 문제가 많다.
- 비선점 부정
자원을 점유하고 있는 프로세스가 다른 자원을 요규할 때 점유하고 있는 자원을 반납하도록 한다.
- 순환 대기 부정
자원에 고유한 번호를 할당하고, 번호 순서대로 자원을 요구하도록 한다.

### 2. 회피(Avoid)
교착 상태가 발생하면 탐지 후 피해나가는 방법이다. 자원이 어떻게 요청될 지에 대한 추가 정보를 제공하도록 요구하여 순환대기가 발생하지 않도록 할당 상태를 검사한다.

#### 안정 상태와 불완전 상태
운영체제는 교착 상태에 빠질 수 있는 가능성이 있는 불안전상태와 안정 상태로 나눈다. 프로세스가 자원을 요청할 때 안전 상태를 유지할 수 있는 요청만 수락하고, 불안전 상태를 초래하는 요청을 거절한다.
- 안전상태
	시스템이 교착 상태를 일으키지 않으면서 각 프로세스가 요구한 최대량 만큼 필요한 자원을 할당해줄 수 있는 상태로 "안정 순서열"이 존재
    > 안전 순서열이란, 현태 상태에서 모든 프로세스에게 결국에는 자원을 할당할 수 있게 해주는 프로세스 실행 순서
- 불안전 상태
	안전 순서열이 존재하지 않는 상태로 교착 상태이기 위한 필요조건(교착상태가 일어날 가능성이 존재한다)
    교착 상태 -> 불안전 상태 (역은 성립 안함)

#### 회피 알고리즘
##### 1. 자원 할당 그래프 알고리즘
각 자원 유형의 단위 자원이 하나 밖에 없는 경우 사용한다. 이 알고리즘은 자원 할당 그래프를 통해 교착 상태를 탐지하고 회피하고자 한다.
![](https://velog.velcdn.com/images/soyeong125/post/78fd5621-efb1-4a70-9626-aa1d66979273/image.png)
P1 -> R1 : 프로세스가 자원을 요청 (실선)
P1 -> R1 : 프로세스가 자원을 요청할 수 있음 (점선)
R1 -> P2 : 자원을 프로세스에 할당

#### 알고리즘 구현 방법
- 프로세스가 자원을 요청
- 요청 간선 추가 (점선)
- 요청 간선을 할당 간선으로 변환 (실선)
- 교착 상태 예측 후 불안전 상태일 경우 대기, 안전 상태의 경우 요청 승인

#### 교착상태 예측 방법
- 그래프에 사이클이 존재하는지 확인한다.
- 사이클이 존재하는 경우, 자원 유형에 몇 개의 사례가 있는지 확인한다.
- 하나의 사례만 있으면 교착상태, 여러 사례가 있다면 교착 상태 가능성이 있다고 판별한다.

아래 그림의 경우 사이클이 있지만 교착상태가 아닌 예시이다.
P1이 작업을 완료하고 R3을 반환하면, P3는 R3를 할당받고 사이클이 해제된다.
![](https://velog.velcdn.com/images/soyeong125/post/aa9299ac-0442-4753-8993-9a14504ff1ee/image.png)

#### 자원 할당 그래프 알고리즘의 단점
자원 요청 시, 탐지 알고리즘을 실행하기 때문에 오버헤드가 발생하여 성능에 영향을 끼칠 수 있다.

##### 2. 은행원 알고리즘
은행이 최소한 한명에게 대출해줄 수 있는 금액을 항상 보유하고 있어야 한다는 개념에서 나왔다. 각 자원 유형의 단위 자원이 여러 개인 경우 사용된다. 운영체제가 최소 하나의 프로세스에게 자원을 줄 수 있는 상태를 항상 유지하는 것이 핵심이다.

##### 은행원 알고리즘의 단점
- 할당할 수 있는 자원의 수가 일정해야 한다.
- 사용자 수가 일정해야 한다.
- 항상 불안전 상태를 방지해야 하기 때문에 자원 이용도가 낮다.
- 최대 자원 요구량을 미리 알아야한다.
- 프로세스들은 유한한 시간안에 자원을 반납해야한다.

### 3. 탐지(Detection)
교착 상태가 발생한 후 회복을 위해 우선 탐지를 해야한다. (탐지 알고리즘 -> 회복 알고리즘)

#### 1. 대기 그래프(Wait for Graph)
각 자원 유형의 단위 자원이 하나일 경우 사용한다. 자원 할당 그래프의 변형으로 자원 할당 그래프에서 자원 노드를 제거하고 프로세스 간의 간선으로 나타낸 그래프이다.
![](https://velog.velcdn.com/images/soyeong125/post/53316ff4-d49f-4def-ac70-ebbe795727c4/image.png)

(a) 자원 할당 그래프
(b) 대기 그래프
P1이 R2를 점유하고 있다. & P4가 R2를 요청하고 있다.
-> P4가 P1을 대기하고 있다.

#### 2. 쇼샤니 포크만 Shoshani & Coffman 알고리즘
각 자원 유형의 단위 자원이 여러 개일 경우 사용한다.

### 4. 회복(Recovery)
교착 상태가 발생한 후, 교착 상태를 일으킨 프로세스를 탐지하거나 종료하거나 할당된 자원을 해제하여 회복한다.

#### 프로세스 종료 방법
교착 상태의 프로세스를 종료하여 자원을 회수한다.
1. 전체 종료: 교착 상태의 프로세스 모두 중지
2. 부분 종료: 교착 상태가 제거될 때까지 하나씩 프로세스를 중지

#### 자원 선점 방법
교착 상태 사이클이 없어질 때까지 자원을 빼앗아 다른 프로세스에게 제공한다.
1. 교착 상태의 프로세스가 점유하고 있는 자원을 선점하여 다른 프로세스에게 할당하며, 해당 프로세스를 일시 정지 시킨다.
2. 우선순위가 낮은 프로세스, 수행된 횟수가 적은 프로세스를 위주로 자원을 선점한다.

### 5. 무시
이 기법을 사용할 경우 성능에 큰 영향을 미친다. 그래서 교착 상태 발생 확률이 낮은 경우에는 별다른 조치를 취하지 않는다. 교착 상태는 자주 발생하지 않으므로 이 방법이 가장 비용이 적게 들고 대부분의 운영체제에서 채택된 방식이다.


> 출처
https://jwprogramming.tistory.com/12
https://steady-coding.tistory.com/520
https://steady-coding.tistory.com/520#%EC%A0%90%EC%9C%A0_%EB%8C%80%EA%B8%B0_(Hold_and_Wait)
    

        
        
    