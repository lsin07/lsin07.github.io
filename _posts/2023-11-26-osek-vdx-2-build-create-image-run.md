---
title: OSEK/VDX 실습 (2) - 프로젝트 빌드, 이미지 제작, 실행
date: 2023-11-26 00 :00:00 +0900
categories: [Automotive SW, OSEK]
tags: [automotive, embedded, rtos, osek, erika3]     # TAG names should always be lowercase
---

> **OSEK/VDX 실습**
1. [ERIKA3 설치하기](/posts/osek-vdx-1-installing-erika3)
1. 프로젝트 빌드, 이미지 제작, 실행 📌

---

## 시작
이번에는 저번 시간에 만든 예시 프로젝트를 빌드 및 실행해 보겠습니다. 지금 저희에겐 실제 MCU 보드나 가상 환경이 준비되어 있지 않기 때문에, bare-metal x86-64 시스템용 이미지를 만들어볼 것입니다.

### Bare-metal?
Bare-metal이란, 원래 '어떤 소프트웨어도 담겨 있지 않은 하드웨어'를 뜻합니다. PC를 예로 들면, 이제 막 구입해서 아직 윈도우도 설치되지 않은 상태 정도로 보시면 되겠습니다. 우리는 이 Bare-metal에 운영체제 같은 소프트웨어를 설치하여 사용합니다. 즉, 이론상으로는, 아무것도 없는 깡통 x86-64 PC를 이번에 만들 이미지 파일을 사용하여 부팅시킬 수 있다는 의미입니다. 물론 이렇게 부팅된 PC는 주어진 작업 말고는 아무것도 하지 못하겠지만요.  
물론 실제로 이런 식으로 하는 것은 가능하지 않을 뿐더러, 가능하다고 하더라도 번거롭고, 위험하고, 불필요한 작업입니다. 우리에게는 KVM이 있으니까요.

### KVM?
**KVM** (Kernal-based Virtual Machine)은 커널 위에서 작동하는 Type 1 전가상화 하이퍼바이저입니다.  
Type 1은 이 하이퍼바이저가 OS와는 독립적으로 동작한다는 것을 의미합니다. (KVM의 경우 커널 위에서 실행되기 때문에 사실 완전히 독립적이지는 않습니다.) KVM 이외에도 Xen이나, Hyper-V 등의 가상화 기술이 있습니다.  
Type 1이 있으면 Type 2도 있겠죠. Type 2는 OS 위에서 실행되는 하이퍼바이저를 의미합니다. Virtualbox 등이 있습니다. Xen과 Virtualbox의 동작 방식의 차이를 잘 생각해 보면 Type 1과 Type 2 하이퍼바이저에 대해 쉽게 이해할 수 있을 겁니다.
KVM은 Kernal mode에서 동작하기 때문에, 유저가 직접 상호작용할 수 없습니다. 그래서, KVM은 QEMU라는 또 다른 에뮬레이터를 통해 User mode에 구현됩니다. KVM과 QEMU는 별개의 가상화 기술이지만 매우 밀접한 관계를 가지고 있어 KVM/QEMU로 합쳐서 부르기도 합니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/1.png)
_Yi Ren et al., 2016, 'Shared-Memory Optimizations for Inter-Virtual-Machine Communication'_

## 빌드[^fn1]

그러면 이제 저번 시간에 만들었던 프로젝트 빌드를 시작해 봅시다.

먼저, ERIKA3 x86-64 컴파일러가 필요합니다. 아래 링크에서 다운로드하고 압축을 풀어 주세요.  
<http://erika-enterprise.com/download/erika3_x86_64_xtools.tar.gz>

컴파일러 다운로드가 끝났으면, 프로젝트의 일부를 수정해야 합니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/2.png)

이미 다 작성된 코드이기 때문에 크게 바꿔야 될 부분은 없고, conf.oil 파일의 65번째 줄에 있는 PLATFORM을 JAILHOUSE에서 BARE로 바꾸어 줍니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/3.png)

그리고, 프로젝트명을 우클릭하고 'Properties'를 눌러 프로젝트 설정창을 엽니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/4.png)

설정창 왼쪽 메뉴에서 'Oil' - 'Generator properties'로 들어가셔서 Enable project specific settings를 체크하시고, 아래 Name에서 'X86_64' - 'GNU compiler prefix'를 찾아서 더블클릭합니다. 저는 이 값이 이미 설정되어 있지만, 처음 하신다면 아마 다른 항목들처럼 빈 칸으로 되어 있을 것입니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/5.png)

그리고, 값을 위 그림과 같이 설정합니다.
위 그림은 예시이며, 아까 받은 컴파일러 압축을 그냥 홈 폴더에 풀었을 경우의 경로입니다. 만약 다른 곳에 압축을 풀었다면, /x-tools/x86_64-unknown-elf/bin/x86_64-unknown-elf- 부분은 그대로 두고, ~ 부분만 다른 경로로 바꾸어 주시면 됩니다.

다 되셨다면, OK를 누르고, Apply and Close를 눌러 변경사항을 적용합니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/6.png)

이런 창이 뜰 텐데, Yes를 누르시면 됩니다. 소스가 변경되었으니 output 파일을 새로 만들겠다는 의미입니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/7.png)

이제 진짜 빌드할 시간입니다. 메뉴바의 'Project' - 'Build Project'를 눌러 빌드를 진행합니다.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/8.png)

이런 식으로 뜨면 성공입니다. 실패하셨다면, 컴파일러 경로는 잘못되지 않았는지, 혹시 오타를 내지는 않았는지 확인해 보세요.

![](/assets/img/2023-11-26-osek-vdx-2-build-create-image-run/9.png)

빌드 결과물은 out 폴더에 저장됩니다. 이 중 저희에게 필요한 파일은 이 elf 파일이니 기억해 둡시다.

## 시스템 이미지 만들기
실행 파일을 만들었으니, 이 파일을 바탕으로 부팅 가능한 이미지 파일을 만들어 봅시다.
이 과정은 grub을 필요로 합니다. 먼저 필요한 grub이 설치가 되어 있는지 확인합니다.
```bash
dpkg --get-selections | grep grub-pc
```
무언가가 뜬다면 문제가 없는 것이고, 아무것도 뜨지 않는다면 아래 코드를 입력하여 grub을 설치해주세요.
```bash
sudo apt install grub-pc
```

grub을 설치하였거나 이미 설치되어 있다면, 이미지 파일을 만들기 위해 필요한 패키지들을 설치해줍니다.
```bash
sudo apt install xorriso mtools
```

그 다음, iso 파일 디렉토리를 만들어줍니다.
```bash
mkdir -p iso/boot/grub
```
현재 디렉토리에 grub 폴더가 든 boot 폴더가 든 iso 폴더가 생성됩니다.

그리고, grub 폴더 안에 grub.cfg 파일을 생성합니다.
내용은 아래와 같습니다.
```
 set timeout=0
 set default=0
 menuentry "erika_os" {
   multiboot /boot/erika.elf
   boot
 }
```
그런 다음 방금 진행하였던 프로젝트 빌드 결과물인 erika.elf 파일을 iso/boot 폴더에 복사합니다.

완성된 폴더 구조는 아래와 같아야 합니다.
```
iso
└── boot
    ├── erika.elf
    └── grub
        └── grub.cfg

2 directories, 2 files
```

iso 폴더가 있는 디렉토리로 나온 다음, 아래 명령어를 입력합니다.
```
grub-mkrescue -o erika3.iso iso
```
이런 식으로 iso 파일이 생성되면 성공입니다.
```
xorriso 1.5.4 : RockRidge filesystem manipulator, libburnia project.

Drive current: -outdev 'stdio:erika3.iso'
Media current: stdio file, overwriteable
Media status : is blank
Media summary: 0 sessions, 0 data blocks, 0 data, 85.7g free
Added to ISO image: directory '/'='/tmp/grub.jUUo0E'
xorriso : UPDATE :     587 files added in 1 seconds
Added to ISO image: directory '/'='/home/user/src/erika/iso'
xorriso : UPDATE :     591 files added in 1 seconds
xorriso : NOTE : Copying to System Area: 512 bytes from file '/usr/lib/grub/i386-pc/boot_hybrid.img'
ISO image produced: 6023 sectors
Written to medium : 6023 sectors at LBA 0
Writing to 'stdio:erika3.iso' completed successfully.
```

## KVM에서 이미지 파일 부팅[^fn2]


먼저, 사용 중인 CPU가 가상화를 지원하는지 확인합니다.
```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

출력 값이 0이라면, CPU가 가상화를 지원하지 않거나, 펌웨어 설정에서 가상화 기술 사용 설정이 꺼져 있는 상태입니다. 요즘 CPU는 대부분 가상화를 지원하니, 컴퓨터 펌웨어 설정으로 들어가서 가상화를 켜주세요.
출력 값이 0이 아닌 수라면, CPU가 KVM 실행이 가능한 상태입니다.

이제 KVM을 설치해 줍니다.
```bash
sudo apt install qemu-kvm
```

설치가 완료되었다면, 아래 명령어를 입력해 KVM이 실행 준비 상태인지 확인합니다.
```bash
kvm-ok
```

결과가 아래와 같다면 준비가 완료된 것입니다. 만약 kvm을 사용할 수 없다고 뜬다면, 컴퓨터를 재부팅해 보세요.
```
INFO: /dev/kvm exists
KVM acceleration can be used
```

아래 명령어를 입력해, erika3.iso 이미지로 kvm을 부트시킵니다.
```bash
sudo kvm erika3.iso -cpu host -smp 1 -m 1G -no-reboot -no-shutdown -nographic
```

아래와 같이 동작하면 성공입니다! 저번 시간에 예상했던 것과 동일하게 작동하는 것을 확인할 수 있습니다.
```
SeaBIOS (version 1.15.0-1)


iPXE (https://ipxe.org) 00:03.0 CA00 PCI2.10 PnP PMM+3FF8B3A0+3FECB3A0 CA00
                                                                               


Booting from Hard Disk...
GRUB Calibrated TSC Timer frequency: 2803482.700 kHz (0xa719c04c)
Calibrated X2APIC Timer frequency: 2803482.700 kHz (0xa719c04c)
Starting OS...
Starting communication over UART
Hello world from Task2! (0)
Hello world from Task1! (1)
Hello world from Task2! (2)
Hello world from Task1! (3)
Hello world from Task2! (4)
Hello world from Task1! (5)
Hello world from Task2! (6)
Hello world from Task1! (7)
Hello world from Task2! (8)
Hello world from Task1! (9)
Hello world from Task2! (10)
```

QEMU의 동작을 멈추게 하고 싶다면 Ctrl+A를 누른 다음 X를 눌러주세요.

## References

[^fn1]: <https://www.erika-enterprise.com/wiki/index.php/Bare-metal_x86-64_image>
[^fn2]: <https://www.erika-enterprise.com/wiki/index.php/ERIKA3_on_the_KVM_hypervisor>