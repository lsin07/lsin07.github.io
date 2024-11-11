---
title: VSCode를 이용한 LaTeX 문서 작성
date: 2023-11-15 00 :00:00 +0900
categories: [LaTeX]
tags: [latex, vscode]     # TAG names should always be lowercase
---

![Title Image](/assets/img/2023-11-15-writing-latex-on-vscode/title.png)
Visual Studio Code에 LaTeX 문서를 편집하기 위한 환경을 구축해 봅시다.

아래와 같은 단계로 진행됩니다.
1. TexLive 설치
2. Visual Studio Code 설치
3. VSCode의 LaTeX Workshop Extension 설치

## TeXLive 설치
TeX 문서를 빌드하기 위해서는 TeXLive라는 프로그램이 필요합니다.
> TeXLive 설치에는 긴 시간이 소요됩니다. (Windows: 2~3시간, Linux: 10~20분)  
> 시간적 여유가 있을 때 진행해 주세요.
{: .prompt-warning}
> TeXLive 전체 패키지는 약 8GB 정도의 저장공간을 사용합니다.
{: .prompt-warning}

자세한 내용은 KTUG의 TeX 설치 문서(<http://wiki.ktug.org/wiki/wiki.php/%EC%84%A4%EC%B9%98>)를 참조하세요.

아래 링크를 눌러 각 OS별 설치 방법을 확인하세요.
- [**Windows**](#windows)
- [**WSL (Windows Subsystem for Linux)**](#windows-wsl)
- [**Linux**](#linux)

### Windows
TeXLive Windows Installer를 이용해 설치하는 방법입니다.

- 먼저, TeXLive 설치 파일(<https://mirror.ctan.org/systems/texlive/tlnet/install-tl-windows.exe>)을 다운로드합니다.  
**무조건, 꼭, <span style="color: red">관리자 권한</span>으로 실행하세요! 그냥 실행하면 중간에 설치가 멈추고 처음부터 다시 진행해야 합니다.**

아래 그림처럼 `single-user installation` 모드로 실행되면 ***안*** 됩니다.

![image1](/assets/img/2023-11-15-writing-latex-on-vscode/1.png){: .w-75}
_**잘못된** 화면입니다. 설치 창을 닫고 관리자 권한으로 다시 실행하세요._

이렇게 아무런 메시지도 표시되지 않아야 정상입니다.

![image2](/assets/img/2023-11-15-writing-latex-on-vscode/2.png){: .w-75}

- 설치는 두 단계로 진행됩니다. 먼저 설치 파일의 압축을 풀고, 그 다음에 설치기가 실행됩니다. `Install`을 선택하고, `Next`를 눌러 줍니다. 잠시 기다리면 설치 파일 압축이 풀리면서 TeXLive 인스톨러가 실행됩니다.

![image3](/assets/img/2023-11-15-writing-latex-on-vscode/3.png){: .w-50}

위 그림과 같이 설치기가 실행됩니다.

`Advanced`를 누르면 다양한 설치 옵션을 선택 가능합니다. 필요한 모든 패키지를 설치하는 `scheme full` 설치는 시간과 저장공간을 많이 요구하지만, 부분 설치를 했다가는 나중에 필요한 패키지가 없어 빌드가 안 되는 불상사가 일어날 수도 있기 때문에 되도록이면 모든 패키지를 설치하는 `scheme full` 설치를 권장합니다.

- `Install`을 눌러 설치를 진행합니다. 설치에는 약 2~3시간의 긴 시간이 소요됩니다. 소요 시간은 네트워크 환경이나 컴퓨터 성능에 따라 달라질 수 있으며, 공식 문서에 의하면 졸업 논문 시즌 등에는 다운로드가 폭주하여 더 많은 시간이 소요될 수 있다고 합니다...

- 설치가 끝나면 시스템을 재부팅합니다. 그리고 명령 프롬프트 혹은 Powershell 등을 관리자 권한으로 열고 아래 명령어를 한 번 실행할 것을 권장합니다. 이 명령어는 현재 설치된 모든 LaTeX 패키지를 업데이트합니다.
```powershell
tlmgr update --all --self
```

### Windows (WSL)
WSL(Windows Subsystem for Linux)에서도 TeXLive를 설치할 수 있습니다. 설치 과정이 Windows Installer를 사용하는 것보다 빠르고 간편하니 WSL을 사용할 줄 안다면 WSL에 설치하는 것도 좋은 방법입니다. WSL에서의 설치 방법은 아래에서 설명할 Linux에서의 설치 방법과 같습니다.

### Linux
리눅스 환경에서는 패키지 관리자를 이용해 쉽게 설치가 가능합니다.

Ubuntu, Linux Mint 등 `apt`를 활용하는 Debian 계열 리눅스에서는 아래와 같이 입력합니다.
```bash
sudo apt install texlive-full
```

Red Hat 계열 리눅스에서는 아래와 같이 입력합니다.
```bash
sudo yum install texlive-*
```

설치 시간은 10-20분 정도로 Windows에서의 설치보다는 훨씬 적은 시간이 소요됩니다.

## Visual Studio Code 설치
Visual Studio Code (VSCode)는 다양한 확장 프로그램을 설치하여 IDE(통합 개발 환경)처럼 사용할 수 있는 확장성이 높은 텍스트 편집기입니다. 여기서는 LaTeX Workshop 확장 프로그램을 설치하여 LaTeX 편집기로 사용할 것입니다.

아래 링크를 눌러 각 OS별 설치 방법을 확인하세요.
- [**Windows**](#windows-1)
- [**WSL (Windows Subsystem for Linux)**](#windows-wsl-1)
- [**Linux**](#linux-1)

### Windows
- [Visual Studio 공식 홈페이지](https://code.visualstudio.com/)에서 `Download for Windows` 버튼을 눌러 Windows용 설치 파일을 다운로드합니다.

- `동의`를 선택하고 `다음`을 눌러 계속 진행합니다.

![vs1](/assets/img/2023-11-15-writing-latex-on-vscode/vs1.png){: .w-75}

- 설치 경로를 선택하고 `다음`을 눌러 계속 진행합니다.

![vs2](/assets/img/2023-11-15-writing-latex-on-vscode/vs2.png){: .w-75}

- 시작 메뉴 폴더를 선택하고 `다음`을 눌러 계속 진행합니다.

![vs3](/assets/img/2023-11-15-writing-latex-on-vscode/vs3.png){: .w-75}

- 바탕 화면 아이콘 및 기타 옵션을 선택합니다. 아래는 차례대로 4개의 기타 옵션에 대한 설명입니다.
    - Windows 탐색기에서 파일을 우클릭 했을 때 'Code로 열기' 옵션을 추가합니다.
    - Windows 탐색기에서 폴더를 우클릭 했을 때 'Code로 열기' 옵션을 추가합니다.
    - 지원되는 파일 형식에 대해 VSCode를 기본 편집기로 등록합니다.
    - VSCode 실행 프로그램을 환경 변수에 추가하여, 터미널에서 열 수 있도록 합니다. 이 옵션을 체크하면, 명령 프롬프트 혹은 Powershell에서 `code`를 입력하는 것으로 VSCode를 실행할 수 있습니다.

![vs4](/assets/img/2023-11-15-writing-latex-on-vscode/vs4.png){: .w-75}

- 설치를 진행합니다.

![vs5](/assets/img/2023-11-15-writing-latex-on-vscode/vs5.png){: .w-75}

### Windows (WSL)

WSL에서는 Windows에 설치된 VSCode에 WSL Extension을 설치하는 것으로 VSCode를 사용할 수 있습니다.  
굳이 WSL 환경 내부에 Linux용 VSCode를 설치하실 필요는 없습니다.

- 먼저 위에서 설명한 방법을 따라 Windows에 VSCode를 설치합니다.
- 왼쪽 바의 Extension 버튼을 클릭하고, `WSL`을 검색합니다.
- `Install`을 눌러 확장 프로그램을 설치합니다.

![image6](/assets/img/2023-11-15-writing-latex-on-vscode/6.png)

### Linux
> Snap Store에서 VSCode를 설치할 수도 있지만, 한글 입력 오류 등 몇 가지 문제가 있어 수동 설치를 권장합니다.
{: .prompt-tip}

- [VSCode 공식 홈페이지](https://code.visualstudio.com/download)에서 설치 파일을 다운로드합니다.  
Ubuntu 등 Debian 계열 리눅스에서는 `.deb` 파일을, Red Hat 계열 리눅스에서는 `.rpm` 파일을 다운로드합니다.

- 터미널에 아래 명령어를 입력합니다. Debian 계열 리눅스의 경우,
```bash
# <file> 에는 아까 다운로드받은 파일의 이름을 입력합니다.
sudo apt install ./<file>.deb
```
Red Hat 계열 리눅스의 경우,
```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/vscode.repo > /dev/null
dnf check-update
sudo dnf install code # or code-insiders
```

## LaTeX Workshop Extension 설치

VSCode용 LaTeX Workshop 확장 프로그램은 문법 자동 완성, 자동 빌드, 미리보기까지 지원하는 강력한 LaTeX 개발 도구입니다.  
단순한 텍스트 편집기인 VSCode에 LaTex Workshop 확장 프로그램을 설치하여 LaTeX용 개발 환경으로 만들어 주도록 하겠습니다.

- 왼쪽 바의 Extension 버튼을 클릭하고, `latex workshop`을 검색합니다.

![image4](/assets/img/2023-11-15-writing-latex-on-vscode/4.png){: .w-75}

- 맨 위에 보이는 `LaTeX Workshop`을 클릭하고, `Install`을 눌러 설치합니다.

## 테스트

설치가 정상적으로 진행되었는지 확인해 봅시다.

- `File` - `Open Folder` (또는 단축키 `Ctrl+K, O`)를 눌러 작업 공간으로 쓸 폴더를 열어 주세요. 그 다음 Explorer 탭을 선택하고 빈 공간을 우클릭한 후 `New File`을 눌러 TeX 파일을 만들어 주세요. TeX 파일의 확장자는 `.tex` 입니다.

![image5](/assets/img/2023-11-15-writing-latex-on-vscode/5.png){: .w-75}

- 예시 문서를 작성해 보겠습니다. 아래 TeX 코드를 복사-붙여넣기 합니다.
```tex
\documentclass{article}
\usepackage{lipsum}
\title{\textbf{The First  \LaTeX  Article}}
\author{\textbf{Author Name} \\ Department Name, University Name}
\begin{document}
\maketitle
\begin{abstract}
\lipsum[1]
\end{abstract}
\section{Hello, World!}
\lipsum[2-4]
\end{document}
```

- 그림에 표시되어 있는, 화면 우측 상단의 초록색 `Build` 버튼을 눌러주세요. (또는 단축키 `Ctrl+Alt+B`) 첫 번째 빌드가 성공하면 그 다음부터는 문서를 저장할 때마다 자동으로 빌드가 이루어집니다.

![image7](/assets/img/2023-11-15-writing-latex-on-vscode/7.png){: .w-75}

- 빌드가 성공하면, TeX 파일을 제외하고는 아무것도 없었던 폴더에 여러 파일들이 생성됩니다. 이 중 `.pdf` 파일을 열어 보면, 빌드 결과물을 확인할 수 있습니다.  
LaTeX Workshop 확장 프로그램에 PDF 뷰어가 포함되어 있기 때문에, 아래와 같이 VSCode에서 바로바로 결과물을 확인할 수 있습니다.

![image8](/assets/img/2023-11-15-writing-latex-on-vscode/8.png){: .w-75}