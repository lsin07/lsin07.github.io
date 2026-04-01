---
title: WSL에 데스크톱 작업 환경 설치하기
description: WSL에 xfce 데스크톱 환경을 설치하고, rdp를 이용하여 연결해 봅시다.
date: 2026-04-01 22:00:00 +0900
categories: [Windows]
tags: [wsl, xfce, linux, windows]     # TAG names should always be lowercase
---

WSL을 처음 설치하면, 밋밋한 터미널 환경만이 제공됩니다. 여기에 데스크톱 환경을 설치해서, 진짜 리눅스를 설치해서 사용하는 기분을 내 봅시다.

> Windows 11 빌드 26200.8039, WSL 버전 2.6.3.0, WSL 배포판 ubuntu-24.04 기준입니다.
{: .prompt-info}

## xfce와 xrdp 환경 설치

먼저, 비교적 가볍게 쓸 수 있는 데스크톱 환경인 xfce와 이를 Windows에서 접속할 수 있게 해주는 RDP 서버인 xrdp를 설치합니다.

1. 우선 기존 패키지들부터 업그레이드합니다.
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2. xfce 데스크톱 환경을 설치합니다.
    ```bash
    sudo apt install xfce4 xfce4-goodies -y
    ```

3. Windows에서 RDP로 접속할 수 있도록 해주는 `xrdp`를 설치합니다.
    ```bash
    sudo apt install xrdp -y
    ```

## 환경 설정

1. `~/.xsession` 파일을 추가하여 RDP를 통한 접속 시 xfce 환경이 실행되도록 설정합니다.  
아래 명령으로 추가가 가능합니다.
    ```bash
    echo "xfce4-session" > ~/.xsession
    ```

2. `/etc/xrdp/startwm.sh` 파일을 수정합니다.
    ```bash
    # vim 말고 nano 등 다른 텍스트 에디터를 써도 좋습니다.
    # 단, sudo 권한으로 열어야 합니다.
    sudo vim /etc/xrdp/startwm.sh
    ```
    1. 파일을 열면, 맨 아래 두 줄이 아래와 같이 되어 있는데, 이 두 줄을 주석 처리해서 지웁니다.

        변경 전:

        ```bash
        test -x /etc/X11/Xsession && exec /etc/X11/Xsession
        exec /bin/sh /etc/X11/Xsession
        ```

        변경 후:

        ```bash
        # test -x /etc/X11/Xsession && exec /etc/X11/Xsession
        # exec /bin/sh /etc/X11/Xsession
        ```
    2. 파일 맨 아래에 아래 코드를 추가합니다.
        ```bash
        # xrdp와 충돌을 일으키는 기존 WSLg 환경 변수를 비활성화합니다.
        unset DBUS_SESSION_BUS_ADDRESS
        unset XDG_RUNTIME_DIR

        # xrdp 세션을 X11로 강제합니다.
        export XDG_SESSION_TYPE=x11

        # dbus-launch를 사용하여 세션 시작이 정확히 진행되도록 합니다.
        exec dbus-launch --exit-with-session startxfce4
        ```

설정이 완료되었으면, xrdp 서비스를 시작합니다.

```bash
sudo service xrdp start
```

## 동작 확인

터미널에 아래 명령을 입력하여 WSL 세션의 IP 주소를 확인합니다.

```bash
ip addr
```

`eth0`의 `inet` 주소가 Windows에서 접근할 수 있는 WSL 세션의 IP 주소입니다. 보통 `172.xx.xx.xx`로 시작합니다.

IP 주소를 확인하였다면, Windows의 원격 데스크톱 연결 앱을 실행하고, 주소 입력란에 해당 IP 주소를 입력합니다.

그러면 xrdp 로그인 창이 뜨고, 아이디와 비밀번호를 입력하는 창이 표시됩니다. 여기에는 당연히 Windows 계정이 아닌 WSL을 설치할 때 생성했던 WSL ubuntu 계정을 입력해야 합니다.

만약 정상적으로 실행되지 않는다면, Windows의 CMD 혹은 Powershell에서 아래 명령을 입력하여 WSL을 종료하였다가 다시 실행해 보세요.

```powershell
wsl --shutdown
```

## 주의사항

WSL은 메모리와 전력 소모를 줄이기 위해, 사용자가 직접 열어둔 콘솔(터미널) 창이 없으면 약 15초 후에 인스턴스를 종료해 버립니다. 그러면 RDP 연결도 자동으로 끊기기 때문에, 터미널 창을 하나 계속 띄워 놓아 WSL이 종료되지 않도록 해야 합니다.

## Conclusion

사실 WSL2는 WSLg라는 쓸만한 리눅스 GUI 앱 실행 환경을 가지고 있어서, 굳이 데스크톱 환경을 만들어서 거기서 GUI 앱을 실행할 필요가 없기에 딱히 실용성은 없는 작업입니다.

그래도 심심하다면 한 번쯤 해볼 만한 것 같네요.
