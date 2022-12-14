# 03-01 프로세스 개요

운영체제에서 프로세스는 하나의 작업 단위이다. 프로그램을 실행하면 그 프로그램은 프로세스가 된다.

---

## 1 프로세스의 개념

* 프로그램  
: 어떤 데이터를 사용하여 어떤 작업을 할지 그 절차를 적어 놓은 작업 지시서. 저장장치에 저장된 정적인 상태.

* 프로세스  
: 작업 지시서를 수행하기 위해 메모리에 동적으로 올라간 상태.

---

## 2 요리사 모형에의 비유

운영체제에서 각 프로세스는 여러 상태를 거친다. 프로세스가 어떻게 작동하는지 이해하기 위해 레스토랑에서 요리를 주문하여 나오기까지의 과정을 살펴보자.

### 2.1 주문서의 역할

요리를 시작하려면 종업원이 손님에게 주문을 받아 주문서를 요리사에게 전해주어야 한다.

주문서에는 여러 가지 내용이 담겨 있다. 주문 순서를 알려주는 일련번호와 어떤 테이블에서 주문했는지 알려주는
테이블 번호도 있다. 여럿이 함께 왔다면 음식이 비슷한 시간에 나와야 하기 때문에 한 테이블에 몇 명인지도 표시되어 있다.  

주문서는 요리의 전반적인 조리 방법과 요리가 나가는 순서를 결정하는 중요한 자료이다. 주문서가 주방에 전달되면 요리사는 주문서에 적힌 대로 요리를 하여 손님에게 제공한다.


### 2.2 일괄 작업 방식의 요리

가장 간단한 요리 제공 방식은 1장에서 배운 **일괄 작업 시스템**이다. 일괄 작업 시스템은 한 번에 하나의 작업만 처리하므로, 레스토랑에 테이블이 하나만 있는 것과 다름없다. 테이블이 하나뿐이니 그 테이블만 손님을 받을 수 있으며, 손님이 식사를 마치고 나간 후에야 다음 손님을 받을 수 있다.  
한 번에 한 가지 요리만 만들면 되기 때문에 요리가 제공되는 순서가 단순하고, 손님이 식사하는 동안 쉬는 시간이 생기며, 손님의 식사가 끝나야 다음 손님을 받을 수 있어 작업 효율이 떨어진다.

### 2.3 시분할 방식의 요리

**시분할**(**time sharing[slicing]**) 
: 하나의 처리 장치에 두 가지 이상의 처리를 시간적으로 교차ㆍ배치하는 컴퓨터 시스템 조작 기법.

테이블이 여러 개 있는 레스토랑에서 일괄 작업 방식으로 요리를 하면 비효율적이다. 시간을 절약하기 위해 요리사는 손님이 식사하는 동안 다른 손님의 요리를 만들어야 한다.

### 2.4 시분할 방식에서의 예상치 못한 상황 처리

---

## 3 프로그램에서 프로세스로의 전환

프로세스는 컴퓨터 시스템의 작업 단위로 태스크(task)라고도 부른다. 앞서 소개한 레스토랑의 예를 통해 시분할 방식 시스템에서 **프로그램이 프로세스로 전환될 때 어떤 일이 일어나는지** 살펴보자.

우선 운영체제는 프로그램을 메모리의 적당한 위치로 가져온다. 그와 동시에 주문서에 해당하는 작업 지시서를 만드는데, 이 작업 지시서가 **프로세스 제어 블록**(Process Control Block, **PCB**)이다. 프로세스 제어 블록이 없으면 프로그램이 프로세스로 전환되지 못한다. 어떤 프로그램이 프로세스가 되었다는 것은 운영체제로부터 프로세스 제어 블록을 받았다는 의미이다.  
프로세스 제어 블록에 있는 다양한 정보 중 대표적인 세 가지는 다음과 같다.

* **프로세스 구분자:** 메모리에는 여러 개의 프로세스가 존재하므로 각 프로세스를 구분하는 구분자(Identification)가 필요하다. 레스토랑의 주문서에 일련번호가 있듯이 프로세스를 구분하기 위해 프로세스 구분자(Process Identification, PID)가 있다.

* **메모리 관련 정보:** CPU는 실행하려는 프로세스가 메모리 상 어디에 위치하고 있는지를 알아야 작업을 할 수 있다. 이를 위해 PCB에는 프로세스의 메모리 위치 정보가 담겨 있으며, 메모리 보호를 위해 경계 레지스터와 한계 레지스터도 포함되어 있다.

* **각종 중간값:** 시분할 시스템에서는 여러 프로세스가 번갈아가며 실행되기 때문에 각 프로세스는 일정 시간 작업을 한 후 다른 프로세스에 CPU를 넘겨준다. 이를 위해 프로세스 제어 블록에는 다음에 작업해야 할 코드의 위치가 담긴 레지스터인 **프로그램 카운터**가 저장된다. 또한 다른 중간값을 보관 중인 각종 레지스터도 같이 저장된다. 이렇게 저장된 값은 작업을 이어서 진행할 수 있게 해준다.

프로세스가 사용자 영역에 위치하는 것과 달리, PCB는 운영체제가 해당 프로세스를 위해 관리하는 데이터 구조이기 때문에 운영체제 영역에 만들어진다.  

지금까지의 내용을 종합하면 프로세스와 프로그램의 관계를 다음과 같이 정의할 수 있다.

> 프로그램이 프로세스가 된다는 것은 운영체제로부터 PCB를 얻는다는 뜻이고, 프로세스가 종료된다는 것은 해당 PCB가 폐기된다는 뜻이다.

강조하고 싶은 점은 컴퓨터 내에 사용자가 실행한 프로세스만 있는 것이 아니라는 사실이다. 운영체제도 프로그램이기 때문에 프로세스 형태로 실행되어야 한다. 2장에서 부팅 과정을 설명하면서 컴퓨터의 전원을 켰을 때 운영체제 프로세스를 메모리에 올리는 프로그램인 부트스트랩을 소개했다. 부트스트랩이 수많은 운영체제 관련 프로세스를 실행한 후 일반 프로세스가 실행되므로 컴퓨터에는 일반 사용자의 사용자 프로세스(user process)와 운영체제의 커널 프로세스(kernel process)가 섞여서 실행된다.

---

## 4 프로세스의 상태

운영체제에서는 여러 가지 이유로 프로세스 상태(process status)가 변화된다.  

이제 막 프로세스가 되었거나 CPU를 사용하다가 쫓겨난 프로세스는 준비 상태에서 자기 순서를 기다린다.

### 4.1 프로세스의 네 가지 상태

<img src="https://www.d.umn.edu/~gshute/os/images/process-diagram.png" width="50%" height="50%">

* 생성 상태(**create status**): 프로세스가 메모리에 올라와 실행 준비를 완료한 상태이다. 프로세스를 관리하는 데 필요한 프로세스 제어 블록이 생성된다.

* 준비 상태(**ready status**): 생성된 프로세스가 CPU를 얻을 때까지 기다리는 상태이다. 레스토랑 예시에서 주문 목록이 준비 상태에 해당한다.

* 실행 상태(**running status**): 준비 상태에 있는 프로세스 중 하나가 CPU를 얻어 실제 작업을 수행하는 상태로 'execute status'라고 표현하기도 한다. 만약 주어진 시간을 다 사용하고도 작업이 끝나지 않았다면 프로세스는 준비 상태로 돌아와 다음 차례를 기다린다.

* 완료 상태(**terminate status**): 실행 상태의 프로세스가 주어진 시간 동안 작업을 마치면 완료 상태로 진입한다. 완료 상태는 프로세스 제어 블록이 사라진 상태이다.

준비 상태에 있는 여러 프로세스 중 다음에 실행할 프로세스를 선정하는 일은 CPU 스케줄러(CPU scheduler)가 담당한다. 준비 상태의 프로세스 중 하나를 골라 실행 상태로 바꾸는 CPU 스케줄러의 작업을 **디스패치**(dispatch)라고 한다.

CPU 스케줄러에 의해 선택된 프로세스는 실행 상태에서 일정 시간 동안 작업을 하는데, 프로세스에 배정된 작업 시간을 타임 슬라이스 또는 타임 퀀텀이라고 부른다. 프로세스는 자신에게 주어진 하나의 타임 슬라이스 동안 작업을 끝내지 못하면 다시 준비 상태로 돌아가는데 이를 **타임아웃**(time out)이라고 한다.

새로운 프로세스가 실행 상태로 들어오면 CPU는 일정 시간(타임 슬라이스)이 흐른 뒤 알려달라고 클록에 요청한다. 이는 알람을 맞추어놓는 것과 같다. 일정 시간이 다 되면 클록은 **인터럽트**(interupt)를 사용하여 일정 시간이 흘렀다고 CPU에 알려준다.

### 4.2 프로세스의 다섯 가지 상태

프로세스는 생성, 준비, 실행, 완료라는 네 가지 상태만으로 작업을 진행하는 데 큰 문제가 없다. 그러나 오늘날 운영체제의 효율성을 고려하여 한 가지 상태를 더 만들었다. 어떤 프로세스가 실행 상태에서 입출력을 요구했다고 가정해보자. 2장에서 설명했듯이 언터럽트 시스템에서 프로세스가 입출력을 요구하면 CPU가 직접 데이터를 가져오지 않고 입출력 관리자에게 명령을 내린다.

입출력을 요구한 프로세스가 입출력이 완료될 때까지 기다리는 상태를 대기 상태(blocking status)라고 한다. 이는 작업의 효율성을 높이기 위해 입출력을 요청한 프로세스를 실행 상태에 두지 않고 대기 상태로 옮기는 것이다. 입출력을 요청한 프로세스가 대기 상태로 옮겨지면 CPU 스케줄러는 준비 상태에 있는 프로세스 중 하나를 가져다 실행 상태로 만든다.

대기 상태의 프로세스는 요청한 입출력이 완료되면 입출력 관리자로부터 인터럽트를 받는다.

### 4.3 휴식 상태와 보류 상태

* 휴식 상태(pause status)

* 보류 상태(suspend status)