---
title: AUTOSAR CP (1) - AUTOSAR 개요
description: 차량용 표준 SW 개발 플랫폼인 AUTOSAR Classic Platform에 대해 알아봅시다.
date: 2024-11-28 19:15:21 +0900
categories: [Automotive SW, AUTOSAR CP]
tags: [automotive, embedded, autosar, autosar_cp]     # TAG names should always be lowercase
---
![logo](/assets/img/2024-11-26-AUTOSAR-overview/autosar-1200x900.png){: .w-75}

**Table of Contents**

1. [What is AUTOSAR?](#what-is-autosar)
    1. [AUTOSAR Platforms](#autosar-platforms)
2. [Why AUTOSAR?](#why-autosar)
    1. [E/E System Getting Complicated](#ee-system-getting-complicated)
    2. [HW Independency of SW](#hw-independency-of-sw)
    3. [Cost Efficiency](#cost-efficiency)

---

## What is AUTOSAR?

**AUTOSAR(AUTomotive Open System ARchitecture)** 는 BMW, BOSCH, Continental AG, Mercedes-Benz Group AG,
Siemens VDO, 그리고 Volkswagen 등 독일의 자동차 회사들을 중심으로 설계된, 자동차용 E/E(Electric/Electroinc)
아키텍처를 위한 개방된 표준 소프트웨어 개발 플랫폼을 의미합니다.

### AUTOSAR Platforms
AUTOSAR 표준은 Classic Platform과 Adaptive Platform으로 구분됩니다.

**AUTOSAR Classic Platform(AUTOSAR CP)**은 일반적인 실시간(Real-time) 임베디드 제어기 시스템을 위한 소프트웨어
플랫폼입니다. 예를 들면, ARM의 Cortex-M 시리즈 같은 마이크로컨트롤러들이 대상이 됩니다. AUTOSAR CP에서는
사용자가 정의한 코드와 소프트웨어 구조를 바탕으로 바이너리 파일을 빌드하고 하드웨어에 다운로드하여 실행합니다.
AUTOSAR CP의 동작은 일반적으로 기존에 사용하던 차량용 제어기 소프트웨어의 역할을 대신하는 범위 내에서 이루어지게
됩니다.

원래 AUTOSAR 표준이라고 하면 AUTOSAR CP를 일컫는 말이었지만, 다양한 인포테인먼트 및 주행 보조 시스템이 발전하면서
더 높은 연산 성능 및 통신 성능을 요구하게 됨에 따라, 고성능 제어기를 위한 일반적인 POSIX 환경에서 동작이 가능한
차량용 미들웨어 플랫폼인 **AUTOSAR Adaptive Platform(AUTOSAR AP)**이 2017년부터 AUTOSAR 표준에 포함되었습니다.

AUTOSAR Adaptive Platform은 ARM의 Cortex-A 시리즈, 혹은 x86과 같은 아키텍처를 가진 고성능 프로세서를 위한
소프트웨어 플랫폼입니다. AUTOSAR CP와 다르게 일반적인 리눅스와 같은 POSIX 운영체제를 기반으로 동작하여,
기반 운영체제의 애플리케이션과 같은 형태로 동작합니다.
동작의 범위 역시 전통적인 자동차의 기능이 아닌 인포테인먼트나 자율주행 등 새로운 Use-case에 대응합니다.

|     | Classic Platform | Adaptive Platform |
| --- | :---------------: | :----------------: |
| 대상 하드웨어 | 실시간 시스템 | 고성능 프로세서 |
| 기반 OS | 없음 (Bare-metal) | POSIX 기반 OS |
| 통신 방식 | Signal-based | Service-oriented |
| 업데이트 방식 | HW에 수동으로 Deploy | OTA 업데이트 등 사용 가능 |
| 적용 범위 | 전통적인 차량 전장 제어 | 인포테인먼트 및 자율주행 등 새로운 Use-case |

## Why AUTOSAR?
### E/E System Getting Complicated
![ecu](/assets/img/2024-11-26-AUTOSAR-overview/electronic-control-unit.png){: .w-50}

자동차용 전장 시스템은 날이 갈수록 복잡해지고 있습니다. 새로운 자동차가 공개될 때마다 사용자의 편의와 안전을
위한 기능들이 추가되고 있으며, 현대의 자동차는 그 기능의 90% 이상이 ECU(Electronic Control Unit, 전자제어유닛)를
통해 직접적, 혹은 간접적으로 제어됩니다. 기계적인 장치들로 이러한 복잡한 기능을 구현하는 시대는 한참 전에 끝났습니다.
AUTOSAR 플랫폼의 목적은 이처럼 복잡해지는 차량용 전장 소프트웨어 시스템을 **효율적**이고 **유연**하게,
그리고 **확장 가능성**을 염두에 두고 관리할 수 있도록 하는 것입니다.

### HW Independency of SW
마이크로컨트롤러 상에서 동작하는 애플리케이션 소프트웨어를 작성하려면, 마이크로컨트롤러가 제공하는 각종 기능에
접근하기 위해서 사용하려는 모델의 하드웨어 매뉴얼을
확인하여, 어떤 메모리 영역에 접근하여 어떤 값을 설정해야 특정 모듈이 활성화되는지에 대한 정보를 찾아서 수동으로
설정해 주어야 합니다. 또는 마이크로컨트롤러 제조사가 제공하는 HAL(Hardware Abstraction Layer) 코드를 사용하는
방법도 있겠지만, 어떤 방법을 사용하든 간에 완성된 애플리케이션 소프트웨어는 하드웨어에 강하게 종속되어 있는
형태일 것입니다. 즉, 특정 ECU에 맞추어 소프트웨어를 개발하면 그 코드는 그 ECU 상에서만 동작시킬 수 있게 되는 것입니다.
만약 ECU 하드웨어의 성능 개선, 혹은 ECU 제조사와의 계약 변경 등 다양한 이유로 차량에 탑재될 ECU를 다른 모델로
변경해야 한다면, 이전까지 공들여 만들어 놓은 소프트웨어를 갈아엎고 처음부터 다시 제작해야 합니다. 이에 많은 비용과
시간이 소모될 것임은 당연하고, 사용자의 안전에도 영향을 끼칠 수 있습니다. 예를 들어, 정확히 필요한 때에만 폭발하도록
심혈을 기울여 설계한 에어백 제어 소프트웨어를 처음부터 다시 만들어야 한다면 이전보다 안정성이 낮아지고, 예상치 못한
동작을 할 가능성이 더 높아질 것입니다.

![asw](/assets/img/2024-11-26-AUTOSAR-overview/asw.png){: .w-75}
_Seperation Between ASW and BSW_

이 문제를 해결하기 위해, AUTOSAR는 애플리케이션 소프트웨어와 하드웨어 관련 소프트웨어를 철저히 분리합니다.
하드웨어에 종속적인 소프트웨어들을 따로 모아 놓고, 마찬가지로 하드웨어와는 관련 없는 애플리케이션의 동작에
관련된 소프트웨어를 따로 모은 다음, 이 두 소프트웨어 그룹이 상호작용할 수 있도록 인터페이스를 제공하는 것이
AUTOSAR의 역할입니다. 하드웨어와 관련 없는 애플리케이션 소프트웨어, 즉 위 그림에서 위쪽 부분의 소프트웨어에
해당하는 부분을 AUTOSAR에서는 **ASW(Application SoftWare)**, 반대로 아래쪽 하드웨어와의 연결을 만들어 주는
소프트웨어 부분을 **BSW(Base SoftWare)**라고 합니다.

하드웨어에 맞추어 개발된 BSW가 존재한다면, ASW는 이제 하드웨어에 상관없이 어디서든 동작이 가능해집니다. 예를 들어,
어떤 자동차 회사가 기존에는 ECU 제조사 A의 A1234라는 ECU를 사용하였는데, 이제부터는 B 제조사의 B5678이라는 ECU를
사용하려 한다고 합시다. 그렇다면, 기존에는 A1234 ECU에서 사용하였던 코드를 버리고 B5678 ECU에 맞추어 처음부터
새로운 소프트웨어를 작성해야 했다면, AUTOSAR 도입 이후에는 A1234와 B5678 ECU에 맞는 BSW만 설계해 놓고
ASW는 이전에 사용하던 것을 그대로 가져와 사용하면 되는 것입니다.

### Cost Efficiency

기업은 결국 금전적인 이익을 추구하는 집단이기 때문에, 항상 개발 비용에 대해서 생각할 수밖에 없습니다.
위에서 설명하였던 소프트웨어의 하드웨어 독립성과 소프트웨어 재사용성이 결국 자동차 기업들의 비용 절감으로
이어지게 됩니다.

일반적으로 자동차 산업은 OEM(Original Equipment Manufacturer)과 그 협력업체들의 연계로 구성됩니다.
OEM은 여러분들이 일반적으로 '자동차 회사'라고 하면 떠올리는 현대자동차, 메르세데스-벤츠, 포드 등의 회사들이고,
협력업체들은 이 OEM들에게 부품이나 소프트웨어를 공급하는 회사들이라고 생각하면 되겠습니다.

AUTOSAR를 통한 소프트웨어 플랫폼 표준화를 이루어냄으로써, OEM 측면에서는 SW 재사용을 통해 새로운 모델의
차량을 개발하는 기간을 단축하여 개발 비용 절감 효과를 얻을 수 있습니다. 또한, 다양한 협력업체에서 소프트웨어를
제공받아 쉽게 통합할 수 있으며, 소프트웨어의 세부 요구사항을 표준화하여 협력업체에 명확하게 전달할 수 있습니다.

마찬가지로, 협력업체 측면에서는, 기존에는 여러 OEM들이 사용하는 하드웨어의 사양에 따라 소프트웨어 개발을 모두
개별적으로 진행해야 했다면, AUTOSAR 플랫폼을 도입함으로써 한 번 개발한 소프트웨어를 별도의 복잡한 수정 없이
여러 OEM에 동시에 공급할 수 있다는 장점이 있습니다.

## References
- AUTOSAR official website (<https://www.autosar.org/>)
- 차세대 ECU를 위한 AUTOSAR Adaptive Platform  
(<https://www.autoelectronics.co.kr/article/articleView.asp?idx=2593>)
- Why AUTOSAR ?— A simple guide  
(<https://medium.com/@sjindhirapooja/why-autosar-a-simple-guide-part1-5064ec3def60/>)
