---
title: OSEK/VDX 실습 (1) - ERIKA3 설치하기
date: 2023-11-25 00 :00:00 +0900
categories: [Automotive SW, OSEK]
tags: [automotive, embedded, rtos, osek, erika3]     # TAG names should always be lowercase
---

## ERIKA

ERIKA는 OSEK/VDX 표준을 만족하는, 임베디드 시스템을 위한 실시간 운영체제입니다.

### OSEK/VDX

벌써 생소한 용어가 나오기 시작했습니다.
OSEK은 독일어 'Offene Systeme und deren Schnittstellen für die Elektronik in Kraftfahrzeugen', 영어로는 Open Systems and thier interfaces for the Electronics in Motor Vehicles의 약자로, <u>차량용 임베디드 시스템의 운영 체제, 통신 스택, 네트워크 관리 프로토콜에 대한 표준, 또는 이 표준을 관리하는 단체</u>를 의미합니다.
용어가 독일어인 것에서 알 수 있듯이, 처음에는 독일의 자동차 회사들이 모여서 시작하였지만, 이후 프랑스의 자동차 회사들이 진행하던 VDX(Vehicle Distributed eXecutive)라는 프로젝트와 합쳐져서 OSEK/VDX라는 프로젝트가 되었습니다.

자세한 정보는 아래 OSEK/VDX 공식 홈페이지에서 확인 가능합니다.  
<https://www.osek-vdx.org/>

### 실시간 운영체제

**실시간 운영체제**(Real-Time Operating System, **RTOS**)란, **실시간성이 보장되어야 하는 컴퓨팅 환경에서의 운영 체제**를 말합니다. 여기서 말하는 실시간성이란, 어떠한 작업을 주어진 시간 내에 무조건 처리해 내는 것을 말합니다.

RTOS를 이렇게만 정의한다면, 이렇게 반론할 수도 있겠죠.  
> 내가 사용하고 있는 Windows도 웬만한 건 다 빠르게 처리하는데, Windows도 실시간 운영체제라고 할 수 있는거 아니냐?

하지만, Windows 같은 운영체제는 어떤 작업이 끝나는 시간이 언제인지 절대 예측할 수 없습니다. 프로세스가 찾는 메모리 주소가 캐시에 있는지, 메모리에 있는지, 메모리에도 없어서 페이지 파일을 뒤져야 되는지, 운영 체제는 실제로 그 주소를 참조하기 전까지는 모릅니다. 게다가 다른 프로세스가 컴퓨팅 자원을 뜯어먹고 있다면, 당연히 이 프로세스의 실행 속도는 더 느려지겠죠. Windows 같은 운영체제에서는 같은 프로세스를 시스템이 켜지자마자 실행했을 때는 1초가 걸렸는데, 음악도 듣고, 유튜브도 보고, 동영상 인코딩도 하면서 실행하니까 10초가 걸리는 일이 충분히 발생할 수 있습니다. 하지만 RTOS에서는 이런 것을 용납할 수 없다는 말이죠. 예를 들어, 여객기의 착륙 장치가 원래는 1초만에 그 값을 계산해서 고도와 속도를 알맞게 설정하는데, 어느 날 알 수 없는 이유로 이 연산이 10초가 걸렸다고 생각해 봅시다. 상상하기도 싫은 일이 벌어지겠죠. 1초와 10초로 비교하니 '에이 설마 그런 일이 일어나겠어?' 라고 생각할 수도 있지만, 당연하게도, 실제 제어기에서는 이보다 훨씬 더 높은 실시간성을 요구합니다.

위에서 계속 'Windows 같은 운영체제'라는 말을 사용했는데, 이를 일컫는 용어도 따로 있습니다. 바로 **범용 운영체제(General Purpose Operating System, GPOS)** 또는 **Rich OS** 입니다. Rich OS가 주로 PC, 스마트폰 같은 범용 컴퓨터에 탑재된다면, RTOS는 차량, 항공기 등의 제어만을 담당하는 제어기에 탑재됩니다. Rich OS와 RTOS 사이의 관계에 대해서도 알아보면 좋은 내용들이 많지만, 이번 글의 내용에서 조금 벗어나므로 다음에 기회가 된다면 다루어 보도록 하겠습니다.

## RT-Druid 다운로드

참고: <https://www.erika-enterprise.com/wiki/index.php/Bare-metal_x86-64_image>

> 리눅스 환경 기준입니다. 여기서는 Ubuntu 22.04 버전을 사용되었습니다.
{: .prompt-info}

> Windows 환경에서 진행하기 위해서는 Hyper-V 가상 환경에 Ubuntu 등 리눅스 운영체제를 설치해서 사용해야 합니다.  
> Hyper-V 사용 시, KVM 가상화의 실행을 위해서는 사전 작업이 필요합니다. [이곳](https://superuser.com/questions/1330271/configuring-kvm-inside-ubuntu-hyper-v-client)을 참조해주세요.
{: .prompt-info}

> WSL (Windows Subsystem for Linux)이나 VMWare, VirtualBox 등은 사용이 불가능합니다.
{: .prompt-warning}

RT-Druid는 ERIKA3을 위한 eclipse 기반의 IDE입니다. 개발을 많이 해보신 분이라면 이미 눈치채셨겠지만, eclipse 기반이기 때문에 java를 필요로 합니다.  
그러니 먼저 jre를 설치해 줍니다. jre나 jdk가 이미 설치되어 있다면 건너뛰어도 좋습니다.
```bash
sudo apt install default-jre
```
그리고 아래 주소에서 RT-Druid를 다운로드하고 압축을 해제합니다. 압축을 푸는 위치는 어디든지 상관없습니다.  
<http://www.erika-enterprise.com/index.php/download/erika-v3-download.html>

## RT-Druid 실행 및 프로젝트 생성

아래 명령어를 입력하여 RT-Druid를 실행합니다. `/<path>/<to>/<rt-druid>/<extraction>/`{: .filepath} 부분은 압축을 풀었던 경로에 맞게 지정해 주어야 합니다.
```bash
/<path>/<to>/<rt-druid>/<extraction>/eclipse/eclipse
```
예를 들어, `home` 디렉토리에 압축을 풀었다면 실행 명령어는 아래와 같습니다.
```bash
~/eclipse/eclipse
```

이렇게 workspace를 선택하는 창이 뜨면 성공입니다. 원하는 곳에 workspace를 생성하고 `Launch`를 눌러주세요.

![image1](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/1.png){: .w-75}
_Workspace 경로 지정 및 실행_

`Launch`를 눌러 실행하면 Welcome 창이 반겨주는데, 살포시 닫아주고
`File - New - RT-Druid v3 Oil and C/C++ Project`를 눌러 새 프로젝트를 만들어줍니다.

![image2](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/2.png)
_File → New → RT-Druid v3 Oil and C/C++ Project_

프로젝트 생성 창이 뜨면, 아래 그림과 같이 설정합니다.  
Project Name은 아무거나 해주시고, Toolchain을 `Cross GCC`로 설정합니다.
그 다음 `Next`를 눌러줍니다.

![image3](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/3.png){: .w-75}
_Empty Project → Cross GCC_

`Create a project using one of these templates`를 체크해 주시고, `x86-64`의 `Helloworld OSEK demo` 선택합니다. 그리고 `Finish`를 누르면 프로젝트가 생성됩니다.
![image4](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/4.png){: .w-75}
_Create a project using one of these templates 체크 → x86-64의 Helloworld OSEK demo 선택_

## 프로젝트 살펴보기
프로젝트 생성에 성공하였다면 이런 화면이 표시됩니다. 좌측의 Project Explorer를 보시면, 여러 파일들이 보입니다.  
conf.oil 파일은 Alarm, Task 등 OSEK/VDX 요소들이 정의된 파일입니다. oil은 OSEK Implementation Language의 약자입니다.  
main.c 파일은 conf.oil에 정의된 요소들의 실제 동작이 정의된 파일입니다. 

![image5](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/5.png)
_RT-Druid 메인 화면_

![image6](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/6.png){: .w-50}
_conf.oil 파일_

conf.oil 파일이 이런 식으로 Task의 스택 크기, 우선 순위, 사용할 Resource 등을 정의한다면,

![image7](/assets/img/2023-11-25-osek-vdx-1-installing-erika3/7.png){: .w-75}
_main.c 파일_

main.c 파일은 Task의 실제 동작 코드를 가지고 있습니다.

코드 내용을 바탕으로 이 프로젝트의 동작을 대충 짐작해 보면 다음과 같습니다.

1. **Task1과 2가 실행된다.**  
(SCHEDULE 속성이 FULL (=full preemptive) 이므로 이 Task들은 다른 Task 실행 중에도 실행될 수 있습니다.)

2. **Priority가 더 높은 Task2가 먼저 SharedUartResource를 점유한다. Task2는 콘솔창에 헬로월드와 counter 변수값을 출력하고 counter에 1을 더한다.**  
(일반적인 상식과는 조금 다르게, OSEK에서는 숫자가 클수록 우선순위가 높습니다. 우선순위가 1이라고 해서 1등인 것이 아니고 오히려 꼴등이라는 거죠.)

3. **Task2가 Resource 점유를 해제하면, Task1이 그 Resource를 점유하고 같은 작업을 실행한다.**

4. **Task1이 Resource 점유를 해제하면, Task2가 그 Resource를 점유하고 ............**

5. **무한 반복**

즉, 콘솔창에는
```
Hello world from Task2! (0)
Hello world from Task1! (1)
Hello world from Task2! (2)
Hello world from Task1! (3)
...
...
```
이 계속 반복되어 표시될 것이라고 예상해 볼 수 있습니다.

## 마무리
이번 글에서는 ERIKA3를 설치하고 프로젝트를 만드는 방법까지 다루어 보았습니다.  
[다음에는 프로젝트를 빌드하고, kvm을 이용하여 실제로 부팅을 시켜보도록 하겠습니다.](/posts/osek-vdx-2-build-create-image-run)