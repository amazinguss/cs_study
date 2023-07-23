## 경쟁상태 (Race Condition) 이란?
 
Race의 뜻 그대로, 간단히 말하면 경쟁하는 상태이다. </br>
자세히 말하자면, 동시에 여러 개의 프로세스가 동일한 자료를 접근하여 조작하고, 그 실행 결과가 접근이 발생한 특정 순서에 의존하는 상황(순서에 의해 결과값이 달라질 수 있다)을 경쟁 상황(race condition)이라고 한다.

## 경쟁 상황 예시

<img width="776" alt="image" src="https://github.com/amazinguss/cs_study/assets/103317018/b2a70fa1-d806-4706-9bc9-d6fcff6b6b4f">

위처럼 서로 다른 스레드가 count라는 하나의 변수에 접근한다고 가정해보자. </br>
count에 대하여 각각의 스레드가 count++, count--를 한 번씩 수행한다면, count의 값이 0이 될 것이라고 예상할 수 있다.</br>
하지만 실제로 실행시켜보면 알 수 없는 값이 나오는 것을 볼 수 있다.</br>
이런 결과가 나오는 이유는 다음과 같다.</br>

![image](https://github.com/amazinguss/cs_study/assets/103317018/f3df235b-ebc2-4305-b5ba-363f3252f754)

먼저 기계어에서 count가 연산되는 과정은 다음과 같이 구현된다.

>
1. 레지스터에 count 변수 값 할당
2. 레지스터가 값을 연산
3. 레지스터가 카운터에 값을 반환

만약 2번 과정중에 컨텍스트 스위칭이 발생하여 프로세스 B가 실행된다면,
process A에서 count의 값이 아직 변하지 않은 상태(count=5)에서 process B 레지스터에 count가 할당되고 해당 레지스터가 process A에서 아직 변하지 않은 값에 -1을 연산(5 - 1)한 후 count의 값 4를 반환할것이다.
</br>

이를 막기 위해서는 한번에 하나의 프로세스만 리소스에 접근할 수 있도록 보장해야 하고,
이러한 보장을 위해 어떤 형태로든 프로세스들이 **순서에 따라 실행(동기화)**될 수 있도록 만들어 주어야한다.

## 임계영역 (Critical section)
경쟁상태를 막기위해서는 공유 자원에 대하여 한 번에 하나의 프로세스만이 접근할 수 있도록 통제해야 하는데, 
그러한 통제가 필요한 영역을 임계영역이라고 한다. 아래는 위의 예시에서 count + 1을 하는 프로세스의 코드이다. 공유자원인 count에 접근하는 코드조각이 임계영역이 된다.
```python
while True:
  # entry section
     # critical section
     count += 1
  # exit section
     # remainder section
```
임계구역은 아래 세 가지 요구조건을 만족시켜야 유효하다.
> 1. 상호배제 (Mutual Exclusion) : 하나의 자원에는 하나의 프로세스만 접근할 수 있어야 한다.
> 2. 진행 (Progress) : 임계구역이 비어있어야만 자원을 사용할 수 있다.
> 3. 한계 대기 (Bounded Waiting) : 대기하면 언젠가는 임계구역에 진입할 수 있어야 한다.
 
## 경쟁 상황을 예방할 수 있는 방법

이러한 race condition을 예방할 수 있는 방법으로 Semaphore(세마포어)와 Mutex(뮤텍스)가 있다.

### Mutex(뮤텍스)
동기화를 위한 가장 간단한 도구로 임계영역을 보호해주고 경쟁상황이 발생하는 것을 막아준다. </br>
프로세스는 임계영역에 들어가기전에 lock을 걸고(acquire lock) 임계영역에 벗어나면 lock을 해제한다.(release lock) </br>
acquire()로 lock을 걸고 releases()로 lock을 해제한다. 이 과정에서 잠금 상태를 확인하는 avaliable boolean 변수를 사용한다.

```python
# Mutex Lock 생성
mutex = threading.Lock()

# ...

# Mutex Lock 획득
mutex.acquire() # acquire lock, available 변수를 False로 변경
try:
    # 임계영역 - 공유 자원에 접근
    shared_resource += 1
finally:
    # Mutex Lock 반환
    mutex.release() # release lock, available 변수를 True로 변경
```
하지만 Mutex Locks는 busy waiting(spin lock)의 문제점을 가지고 있다. 즉, 쓸데없이 CPU를 점유하고 있는 경우가 발생해 자원을 낭비한다. </br>
이를 해결하기 위해서는 busy waiting하는 프로세스를 강제로 wait state로 보내면 되는데, 이것이 Semaphore의 방식이다.

### Semaphore(세마포어)
공유된 자원의 데이터 혹은 임계영역 등에 프로세스나 스레드를 나타내는 값을 두어서 상호배제를 구현할 수 있도록 한다. </br>
세마포어는 두 가지 원자연산을 통해서만 접근할 수 있는 정수 변수이며, wait(), signal() or P() V()로 표현한다.

