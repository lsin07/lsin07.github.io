---
title: AUTOSAR CP (2) - AUTOSAR 계층 구조
description: AUTOSAR Classic Platform의 Layered Software Architecture에 대해 알아봅시다.
date: 2025-02-13 13:29:01 +0900
categories: [Automotive SW, AUTOSAR CP]
tags: [automotive, embedded, autosar, autosar_cp]     # TAG names should always be lowercase
---
![logo](/assets/img/2024-11-26-AUTOSAR-overview/autosar-1200x900.png){: .w-75}

> **AUTOSAR CP**
1. [AUTOSAR CP (1) - AUTOSAR 개요](/posts/AUTOSAR-overview)
1. AUTOSAR CP (2) - AUTOSAR 계층 구조 📌

---

**Table of Contents**

1. [Overview](#overview)
1. [Basic Software (BSW)](#basic-software-bsw)
    1. [Microcontroller Abstraction Layer](#microcontroller-abstraction-layer)
    1. [ECU Abstraction Layer](#ecu-abstraction-layer)
        1. [I/O Hardware Abstarciton](#io-hardware-abstraction)
    1. [Service Layer](#service-layer)
    1. [Complex Drivers Layer](#complex-drivers-layer)
1. [Application Layer](#application-layer)
1. [Runtime Environments (RTE)](#runtime-environment-rte)

---

## Overview

AUTOSAR 플랫폼의 핵심이자 존재 의의는 애플리케이션 소프트웨어의 재사용성과 이식성을 높여
더 유연하고 확장성 있는 소프트웨어를 설계하고 개발 비용을 절감하는 것에 있습니다.
AUTOSAR는 몇 개의 독립적인 소프트웨어 계층 구조를 제공하여 이를 실현합니다.

![arch_topview](/assets/img/2025-02-05-AUTOSAR-layered-architecture/topview.png)

전체적으로 보았을 때, AUTOSAR가 제공하는 소프트웨어 아키텍처 계층은 3가지로 나누어집니다.  
하드웨어와 가까운 아래쪽부터 살펴보면,

[Basic Software (BSW)](#basic-software-bsw)
: 마이크로컨트롤러 (μC) 혹은 ECU 하드웨어 종속적인 영역으로, 하드웨어를 제어하고,
이를 위한 인터페이스를 상위 레이어에 제공합니다.

[Runtime Environment (RTE)](#runtime-environment-rte)
: 애플리케이션 소프트웨어가 다른 소프트웨어 구성 요소와 상호작용할 수 있도록 하는
통신 서비스를 제공합니다.

[Application Layer](#application-layer)
: 개별적인 μC나 ECU와는 완전히 독립적인 애플리케이션 소프트웨어 영역입니다.

위와 같이 크게 3개의 계층으로 구성되어 있습니다. 각 계층에 대해 자세히 살펴보도록 하겠습니다.

## Basic Software (BSW)

**Basic Software (BSW)**는 μC와 직접 통신하고 하드웨어를 직접 제어하는 영역입니다.
BSW는 추상화 레벨에 따라 4개의 추가적인 레이어로 구분됩니다.
![bsw_structure](/assets/img/2025-02-05-AUTOSAR-layered-architecture/bsw_structure.png)
_BSW Layers_

각각의 BSW 계층은 아래 그림과 같이 그 기능에 따라 여러 개의 그룹으로 세분화됩니다.
![bsw-fg](/assets/img/2025-02-05-AUTOSAR-layered-architecture/bsw-fg.png)
_BSW Functional Groups_

### Microcontroller Abstraction Layer

![mcal](/assets/img/2025-02-05-AUTOSAR-layered-architecture/mcal.png){: .w-75}
_Microcontroller Abstraction Layer (MCAL)_

Microcontroller Abstraction Layer (MCAL)은 위 그림에서 빨간색으로 표시된 부분으로,
BSW의 가장 하위 계층에 위치한 소프트웨어 레이어입니다.
MCAL 영역에서는 μC 및 peripheral에 직접 접근할 수 있는 디바이스 드라이버들을 제공하는 가장 기본적인 소프트웨어
추상화가 제공됩니다.

MCAL은 상위 소프트웨어 레이어에 개별 **μC에 대한 독립성**을 제공해야 합니다. 예를 들어, 마이크로컨트롤러의 GPIO를 통해
Digital HIGH를 출력하여 LED를 켜고자 할 때, MCAL에서는 μC의 GPIO 기능을 활성화하고, 특정 PORT 핀에 대한 GPIO 출력을
켜기 위해 메모리의 해당 주소에 특정 값을 기록하는 등의 일련의 과정을 "GPIO Port E의 4번 핀 HIGH 출력" 과 같이
추상화하여 상위 레이어에 제공합니다.

MCAL은 하드웨어에 직접 접근하므로 그 구현은 개별 μC에 종속적입니다. 하지만, MCAL은 μC와 독립적이면서 표준화된
인터페이스를 상위 계층에 제공해야 합니다.

### ECU Abstraction Layer

![ecual](/assets/img/2025-02-05-AUTOSAR-layered-architecture/ecual.png){: .w-75}
_ECU Abstraction Layer (ECUAL)_

ECU Abstraction Layer (ECUAL) 는 위 그림에서 초록색으로 표시된 부분으로,
각 기능이 μC의 일부인지 또는 주변 컴포넌트에 의해 실행되는지에 관계없이
통신, 메모리, 또는 IO와 같은 ECU의 기능에 대해 접근할 수 있는 API를 제공합니다.

ECUAL은 [MCAL](#microcontroller-abstraction-layer)에서 제공하는 인터페이스를 바탕으로
상위 레이어에 **ECU 하드웨어 구성에 대한 독립성**을 제공합니다. 예를 들어,
MCAL에서 구현된 "GPIO Port E의 4번 핀 HIGH 출력"을
"LED 1번 HIGH 출력"으로 한번 더 추상화하는 것이라고 이해하면 되겠습니다.

위와 같은 ECU 추상화 작업을 통해, ECUAL은 ECU의 하드웨어 구성과는 독립적인 인터페이스를
상위 레이어에 제공합니다. 따라서, 상위 레이어에서는 1번 LED가 어느 GPIO의 몇 번째 핀에
연결되어 있는지에 대해서 알지 못해도 1번 LED에 접근할 수 있게 됩니다.

#### I/O Hardware Abstraction

![iohwab](/assets/img/2025-02-05-AUTOSAR-layered-architecture/iohwab.png){: .w-75}
_I/O Hardware Abstraction (IoHwAb)_

I/O Hardware Abstraction (IoHwAb) 은 [ECUAL](#ecu-abstraction-layer)의 일부분으로,
[MCAL](#microcontroller-abstraction-layer)에 접근하기 위한
IoHwAb 포트와 같은 **AUTOSAR 인터페이스**를 애플리케이션 소프트웨어에 제공합니다.

> AUTOSAR 인터페이스에 대한 자세한 내용은 [Appication Layer](#application-layer) 부분을 참고하세요.
{: .prompt-info}

IoHwAb는 하드웨어 특정적인 값이나 MCAL API 함수들을 완전히 추상화하여, 애플리케이션 소프트웨어 단계예서는
모든 상호작용이 AUTOSAR 인터페이스를 통해 수행될 수 있도록 그 환경을 마련해 주어야 합니다.

위에서 설명하였던 LED 점등 예시는 디지털 출력을 사용하기 때문에 사실 IoHwAb에서 담당하여야 하는
부분입니다. 해당 예시의 경우, IoHwAb는 1번 LED 점등을 위한 AUTOSAR 인터페이스를 제공하여야 합니다.
여러 가지 방법이 있겠지만, 애플리케이션 소프트웨어에서 클라이언트 포트를 통해 접근할 수 있도록
**서버 포트**를 제공하는 방법이 제일 이상적일 것 같네요.

### Service Layer

![service-layer](/assets/img/2025-02-05-AUTOSAR-layered-architecture/service-layer.png){: .w-75}
_Service Layer_

Service Layer는 BSW의 최상위 레이어로, 아래와 같은 기능들을 애플리케이션 소프트웨어에 서비스의 형태로 제공합니다.

- 운영체제 (AUTOSAR OS)
- 차량용 네트워크 통신 및 관리
- 비휘발성 메모리 관리
- 진단 서비스
- ECU 상태 및 모드 관리
- 논리적/시간적 플로우 모니터링 (Watchdog)

> I/O에 대한 부분은 [IoHwAb](#io-hardware-abstraction)에서 관리되고 있기 때문에 제외됩니다.
{: .prompt-tip }

### Complex Drivers Layer

![cmplx-drvs](/assets/img/2025-02-05-AUTOSAR-layered-architecture/cmplx-drvs.png){: .w-75}
_Complex Drivers Layer_

Complex Drivers Layer는 여러 가지 이유로 AUTOSAR 표준에 포함될 수 없는 특수 요소들에, AUTOSAR 플랫폼 상
사용 가능성을 제공하기 위한 일종의 예비 공간입니다.

특정 소프트웨어 혹은 하드웨어 모듈이 AUTOSAR에 정의되어 있지 않거나, 매우 엄격한 시간적 조건을 만족해야
하는 등 다양한 이유로 일부 요소들을 AUTOSAR 표준에 포함할 수 없는 경우가 있습니다. 이러한 요소들을 사용할 수
있도록 AUTOSAR 아키텍처 내에 포함하기 위한 계층이 바로 Complex Drivers Layer입니다.

AUTOSAR 표준에 포함되지 않는 특수 케이스들을 위한 영역이기 때문에 의존성 조건도 다른 영역과는 달리 특별히
정해져 있지 않습니다. 공식 문서에는 'might be application, μC and ECU hardware dependent'라고 두루뭉실하게
표현되어 있습니다.

## Application Layer

![app-layer](/assets/img/2025-02-05-AUTOSAR-layered-architecture/app-layer.png){: .w-75}
_Application Layer_

Application Layer는 μC나 ECU로부터 완전히 추상화되어, 애플리케이션 소프트웨어의 동작만을 표현하는 영역입니다.
Application Layer에 존재하는 소프트웨어는 기존의 구성 방식과는 다르게 **Sofware Component (SWC)**를 중심으로,
**AUTOSAR Interface**를 통해 각 SWC끼리의 통신을 표현하는 방식으로 구현되어 있습니다.

![asw-example](/assets/img/2025-02-05-AUTOSAR-layered-architecture/asw-examples.png)
_Application Software 구성 예시_

위 그림은 AUTOSAR 애플리케이션 소프트웨어 구성의 예시입니다. 큰 회색 사각형이 SWC이고, 실선으로 서로 연결된
작은 사각형이 **포트**(Port) 입니다. 위 그림과 같이, AUTOSAR 애플리케이션 소프트웨어는 SWC와, SWC를 서로
연결해 주는 포트들로 구성되어 있습니다.

포트는 AUTOSAR 인터페이스를 바탕으로 특정 기능 수행이나 서비스 제공이 가능하도록 구현한 것입니다.
**AUTOSAR 인터페이스**는, 소프트웨어 컴포넌트끼리의 통신을 위해 AUTOSAR에서 제공하는 인터페이스를 말합니다.
실생활에서의 예를 들어 본다면, USB라는 '인터페이스'를 이용하여 컴퓨터는 마우스, 키보드, 프린터, 저장 장치 등
다양한 장치와 통신이 가능합니다. 이러한 개별 기능의 구현이 '포트'에 해당합니다.

Application Layer 시점에서, 소프트웨어 컴포넌트끼리의 통신은 실체가 없는 가상의 개념입니다. 포트와 인터페이스를
통해서 SWC 간의 통신을 정의하지만, 실제 ECU 레벨에서 어떤 방식으로 통신을 진행할지에 대해서는 아무것도
정해지지 않은 상태입니다. 이러한 SWC 간의 통신을 표현하는 일종의 가상의 버스를 **Virtual Functional Bus (VFB)**
라고 합니다. SWC들이 어떤 ECU에 매핑되냐에 따라 통신의 실제 구현은 달라질 수 있습니다. 만약
하나의 ECU 내의 SWC들 간의 통신이라면 공유 메모리 영역을 이용한 통신이 이루어질 것이고, 통신 대상
SWC가 서로 다른 ECU에 매핑되어 있다면 CAN, LIN, FlexRay, SOME/IP 등 다양한 차량용 네트워크 프로토콜을
사용할 수 있습니다. 이러한 세부 구현은 아래에서 설명할 Runtime Environment에 의해 이루어집니다.

## Runtime Environment (RTE)

![rte](/assets/img/2025-02-05-AUTOSAR-layered-architecture/rte.png){: .w-75}
_Runtime Environment (RTE)_

위에서 설명하였듯이 [Application Layer](#application-layer)의 소프트웨어 구조는 실체가 없는 추상적인 개념입니다.
따라서, Application Layer는 이러한 가상의 통신 구조를 구현하여 동작이 가능한 실체로써 구체화해 주는
과정이 필요합니다. Runtime Environment (RTE)는 Application Layer에서 정의한 VFB가 실제로 동작 가능하도록
관련 서비스를 제공하여 Application Layer에서 제시한 컴포넌트 기반 소프트웨어 아키텍처를 실현하는 역할을 합니다.

![rte-structure](/assets/img/2025-02-05-AUTOSAR-layered-architecture/rte-structure.png)

RTE는 소프트웨어 개발 과정에서 RTE Generator를 이용하여 각 ECU당 하나씩, 개별 ECU의 BSW에 맞게 생성됩니다. 
Application Layer에서는 특정 SWC가 어떤 ECU에 배정될지 (이 과정을 ECU Mapping이라고 합니다.) 에 대한 아무런
고려 없이 전체적인 소프트웨어를 설계합니다. 위의 그림처럼, ASW 개발 시에는 ECU 매핑이나 통신 방법에 대한
고려 없이 하나의 전체적인 시스템을 컴포넌트 단위로 설계하고, RTE 생성 및 ECU 매핑 과정에서 통신이 하나의 ECU 내에서
공유 메모리를 이용한 소프트웨어적인 방법으로 RTE 내부에서서 이루어질지(그림의 `HeatingDial`과
`SeatHeatingControl`의 통신), 또는 BSW에서 정의된 실제 통신 서비스를 이용하여 CAN과 같은 통신 규약으로
다른 ECU와 통신할지(그림의 `SeatHeatingControl`과 `PowerManagement`의 통신)가 결정됩니다.

## References

- AUTOSAR Main Requirements  
(<https://www.autosar.org/fileadmin/standards/R22-11/FO/AUTOSAR_RS_Main.pdf>)
- AUTOSAR Layerd Software Architecture  
(<https://www.autosar.org/fileadmin/standards/R18-10_R4.4.0_R1.5.0/CP/AUTOSAR_EXP_LayeredSoftwareArchitecture.pdf>)
- Specification of I/O Hardware Abstraction  
(<https://www.autosar.org/fileadmin/standards/R18-10_R4.4.0_R1.5.0/CP/AUTOSAR_SWS_IOHardwareAbstraction.pdf>)
- Virtual Functional Bus  
(<https://www.autosar.org/fileadmin/standards/R18-10_R4.4.0_R1.5.0/CP/AUTOSAR_EXP_VFB.pdf>)
- Vector E-Learning -- AUTOSAR의 레이어 모델  
(<https://elearning.vector.com/mod/page/view.php?id=1931>)