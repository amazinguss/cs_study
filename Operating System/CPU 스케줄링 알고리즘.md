# CPU 스케줄링 알고리즘
CPU 스케줄링은 준비 큐에 있는 어느 프로세스에 CPU 코어를 할당할 것인지 결정하는 문제를 다룬다.
## FCFS (First Come First Served) 스케줄링
- 가장 간단한 CPU 스케줄링 알고리즘
- 가장 먼저 요청한 프로세스에 CPU를 할당해주는 방식
- 비선점형(Non-preemptive) 스케줄링
- 작성이 간단하고 이해하기 쉽다.
- 평균 대기 시간(Average Waiting Time)이 길어질 수 있다.(대화형 시스템에 부적절)
- 응답 시간(Response Time)이 길어질 수 있다.
- 반환시간(Turnaround Time) 면에서는 좋을 수 있다.
- Convoy Effect(호위 효과)가 발생할 수 있다. 

**FCFS 예시** </br>
![](https://velog.velcdn.com/images/soyeong125/post/c75a2844-98c5-43e3-a52e-32e8d45e3bdc/image.png)

1. 대기시간(Waiting Time)
	- P1 = 0
    - P2 = 24
    - P3 = 27
2. 반환시간(Turnaround Time)
	- P1 = 24
    - P2 = 27
    - P3 = 30
3. 반응시간(Response Time)
	- P1 = 0
    - P2 = 24
    - P3 = 27
4. 평균 대기 시간
	- (0+24+27)/3 = 17

## SJF(Shortest-Job-First) 스케줄링
- CPU burst 길이가 작은 프로세스부터 순차적으로 CPU 코어를 할당하는 알고리즘
- 비선점형과 선점형이 따로 존재한다. 
- 비선점형에서는 실행되고 있는 프로세스는 끝까지 실행한다.
- 선점형에서는 현재 실행되고 있는 프로세스의 남은 시간보다 도착한 다음 프로세스가 더 빨리 끝날 수 있는 프로세스라면 다음 프로세스를 실행하도록 바꾸게 된다. - SRTF(Shortest Remaining Time First)라고도 부른다. 
- SJF 스케줄링은 주어진 프로세스 집합에 대한 평균 대기 시간을 줄일 수 있다.
- 하지만 다음에 올 프로세스의 CPU burst time을 예측하는 것이 어렵다.

**비선점형 SJF 예시**
 ![](https://velog.velcdn.com/images/soyeong125/post/2f2441d9-35cf-4bec-a698-655108e1bdaa/image.png)
 1. 대기시간(Waiting Time)
	- P1 = 0
    - P2 = 6
    - P3 = 3
    - P4 = 7
2. 반환시간(Turnaround Time)
	- P1 = 7
    - P2 = 10
    - P3 = 4
    - P4 = 11
3. 반응시간(Response Time)
	- P1 = 0
    - P2 = 6
    - P3 = 3
    - P4 = 7
4. 평균 대기 시간
	- (0+6+3+7)/4 = 4
    
**선점형 SJF 예시** </br>
선점형 SJF의 경우 실행 중이던 프로세스보다 도착한 프로세스의 남은 시간이 짧으면 프로세스가 전환된다.
![](https://velog.velcdn.com/images/soyeong125/post/52a027c7-f0f9-451a-9170-32afed9354a4/image.png)
 1. 대기시간(Waiting Time)
	- P1 = 9
    - P2 = 1
    - P3 = 0
    - P4 = 2
2. 반환시간(Turnaround Time)
	- P1 = 16
    - P2 = 5
    - P3 = 1
    - P4 = 6
3. 반응시간(Response Time)
	- P1 = 0
    - P2 = 0
    - P3 = 0
    - P4 = 2
4. 평균 대기 시간
	- (9+1+0+2)/4 = 3   
    
## 우선순위 Priority 스케줄링 </br>
- 우선순위에 따라 CPU에 프로세스를 할당하는 방식
- 각각의 프로세스에 우선순위 넘버가 있다.
- 우선순위를 결정하는 방법은 내부적, 외부적으로 정의한다.
	- 내부적 우선순위 결정: 제한 시간, 기억장소 요청량, 사용 파일 수, 평균 프로세서 버스트에 대한 평균 입출력 버스트의 비율
    - 외부적 우선순위 결정: 프로세스의 중요성, 사용료를 많이 낸 사용자, 작업을 지원하는 부서, 정책적 요인
- 선점형과 비선점형이 나뉜다.
- 선점형은, 준비 큐에 도착한 프로세스의 우선순위와 현재 실행 중인 프로세스의 우선순위를 비교하여 우선순위가 가장 높은 프로세스에 CPU를 할당한다.
- 우선순위가 동일한 프로세스가 준비 큐로 들어오면 FIFO순서로 스케줄링을 한다.
- 비선점형은, 이미 실행중인 프로세스가 있다면 프로세스가 종료후 준비 큐에 있는 프로세스 중 우선순위가 높은 프로세스를 할당한다.
- SJF도 Priority 스케줄링이라고 할 수 있다. (다음 CPU burst time이 우선순위인 것처럼 작동하므로)
- 무한 봉쇄(indefinite blocking) 또는 기아(Starvation) 문제가 발생할 수 있다.
- 무한 봉쇄는 실행 준비는 되어 있으나 CPU를 사용하지 못하는 프로세스는 CPU를 기다리면서 봉쇄 되었다고 한다. (Blocking)
- 기아 상태는 높은 우선순위 프로세스들이 꾸준히 들어와서 낮은 우선순위의 프로세스들이 CPU를 얻지 못하게 된다.(Starvation)
- 기아문제를 해결하기 위해서 노화(aging)를 사용할 수 있다. (매 분마다 대기중인 프로세스의 우선순위를 1씩 증가시킨다. 그럼 현재 프로세서를 점유중인 프로세스보다 우선순위가 높아지는 시점이 생겨 기아 문제를 해결할 수 있다.)
- 우선순위 스케줄링 문제를 해결할 수 있는 다른 방법은 우선순위 스케줄링과 라운드 로빈 스케줄링을 결합하는 방법이다.

> CPU Burst time?
> 프로그램 수행중에 연속적으로 CPU를 사용하는 구간

![](https://velog.velcdn.com/images/soyeong125/post/106e5577-46d2-4a78-bb49-14a5d1b4551a/image.png) </br>
모두 0초에 도착했다고 가정했을 때, 우선순위가 위와 같이 처리된다. </br>
 1. 대기시간(Waiting Time)
	- P1 = 6
    - P2 = 0
    - P3 = 16
    - P4 = 18
    - P5 = 1
2. 반환시간(Turnaround Time)
	- P1 = 16
    - P2 = 1
    - P3 = 18
    - P4 = 19
    - P5 = 6
3. 반응시간(Response Time)
	- P1 = 6
    - P2 = 0
    - P3 = 16
    - P4 = 18
    - P5 = 1
4. 평균 대기 시간
	- (6+0+16+18+1)/5 = 8.2
    
## Round Robin(RR)스케줄링 </br>
- 각각의 프로세스에 동일한 CPU 할당 시간을 부여해서 해당 시간 동안만 CPU를 이용하게 한다.
- 이를 시간 할당량(time quantum), 타임 슬라이스(time slice) 라는 작은 단위의 시간으로 정의한다.
- 할당 시간 내에 처리를 완료하지 못하면 다음 프로세스로 넘어가는 선점형 방식이다.
- n개의 프로세스가 있을 때 할당 시간을 q로 설정하면, 어떤 프로세스도 (n-1)q 시간 이상을 기다리지 않아도 된다.
- 응답 시간을 빠르게 할 수 있다는 장점이 있다.
- q가 커진다면 FCFS처럼 작동한다. 
- q가 매우 작아지면 process sharing이라고 부른다. 이것은 n개의 프로세스가 프로세서 속도의 1/n 씩으로 작동함을 의미한다. 너무 많은 문맥 교환을 야기한다.
- 시간 할당량의 크기는 알고리즘 성능과 비례하므로 적절한 시간 할당량 크기를 설정해야한다.

**RR예시** </br>
모두 0초에 도착했고 할당 시간 q가 20초라고 가정하면 다음과 같다. </br>
![](https://velog.velcdn.com/images/soyeong125/post/740da3d9-a73e-41bd-b005-3749baaef324/image.png) </br>

 1. 대기시간(Waiting Time)
	- P1 = 81
    - P2 = 20
    - P3 = 94
    - P4 = 97
2. 반환시간(Turnaround Time)
	- P1 = 77
    - P2 = 37
    - P3 = 162
    - P4 = 121
3. 반응시간(Response Time)
	- P1 = 0
    - P2 = 20
    - P3 = 37
    - P4 = 57
4. 평균 대기 시간
	- (81+20+94+97)/4 = 73       


## MultiLevel Queue </br>
![](https://velog.velcdn.com/images/soyeong125/post/50029f8d-d3aa-4f01-b1db-688029abc4c3/image.png) </br>
- 준비 큐가 여러 개의 큐들로 나뉜다.
- 프로세스들의 특성에 따라 우선순위가 부여되어 한 개의 큐에 영구적으로 할당된다.
- 항상 가장 높은 우선순위 큐의 프로세스에 CPU를 할당, 우선순위가 낮은 큐에서 작업이 실행중이더라도 상위 단계의 큐에 프로세스가 도착하면 CPU를 빼앗는 선점형 스케줄링
- 각 큐는 독자적 스케줄링 알고리즘을 가지고 있다. 
- 각 큐 사이에서 프로세스들이 이동할 수 없다. 
- 대화형(Foreground), 배치(Background)등의 프로세스 성격에 따라 우선순위를 부여한다.
- 일반적으로 Foreground 프로세스들은 Round Robin 방식을 사용한다.
- Background 프로세스는 FCFS를 사용한다.
- 보통 CPU 시간의 80%는 Foreground의 RR, 20%는 Background의 FCFS에 할당된다.
- 우선순위가 낮은 프로세스가 오랫동안 CPU 할당을 기다리는 기아(Starvation) 문제가 발생할 수 있다.


## Multilevel Feedback Queue(MFQ) </br>
![](https://velog.velcdn.com/images/soyeong125/post/bb6421b5-a975-441e-8c4a-ff7169ba4b47/image.png) </br>
- Multilevel Queue와 비슷하지만, MFQ는 각 큐 간에 프로세스들이 이동할 수 있다. 
- 프로세스 생성 시 가장 높은 우선 순위 준비 큐에 등록되며 등록된 프로세스는 FCFS 순서로 CPU를 할당받아 실행된다. 해당 큐의 CPU 시간 할당량이 끝나면 한단 계 아래 준비 큐에 들어간다.
- 단계가 내려갈수록 시간 할당량이 증가한다.
- CPU burst는 낮은 우선순위의 큐, I/O burst는 높은 우선순위 큐에 배치된다.
- 가장 하위 큐는 FCFS 스케쥴링이다.
- 맨 아래 큐에서 너무 오래 대기하면 다시 상위 큐로 이동한다. Aging을 통한 Starvation을 예방한다.
- 특정 시스템에 부합하도록 구성이 가능함으로 현대 사용되는 CPU 스케줄링 알고리즘 중 가장 일반적인 CPU 스케줄링 알고리즘이다.

> 출처 </br>
https://code-lab1.tistory.com/45 </br>
https://wonit.tistory.com/108 </br>
https://imbf.github.io/computer-science(cs)/2020/10/18/CPU-Scheduling.html </br>
https://cocoon1787.tistory.com/124 </br>
