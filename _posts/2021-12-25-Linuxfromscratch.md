---
layout: post
title: "Linux from scratch"
subtitle: "Linux from scratch "
cover: /assets/images/posts/2021-12-25-Retrospective.png
date: "2021-12-25 10:11:00"
categories: posts
tags: ["Linux from scratch"]
---

[Linux from scratch](https://www.linuxfromscratch.org/lfs/)

소스를 컴파일하여 리눅스를 설치하는 것을 공유드리려 합니다.

글을 작성함에 있어서 약간의 허풍도 더하고 소설 혹은 에세이 같이 써 내려갈 것입니다. 업무에 필요한 보고서라면 그렇게 쓰면 안되지만, 수위를 조절해가면서 작성하면 부담 없을 것이라고 생각하고, 요즘 저의 링크드인에는 잘 쓰여진 보고서 같은 글들이 많기 때문에, '저 하나쯤...' 이란 생각으로 장난스럽게 작성하려 합니다. 다만, 사람마다 호불호가 있기 때문에 위의 링크를 통하여 정보를 얻으시면 공유 드린 글을 읽지 않으셔도 충분히 소스를 컴파일하여 리눅스를 설치하는 것을 배우실 수 있을 것입니다.

사실 저는 지금도 그냥 우분투 배포판을 이용하여 클릭 몇 번으로 리눅스를 설치하여 사용합니다. 지금은 "WINDOWS SUBSYSTEM LINUX (WSL)"을 이용하여 리눅스를 사용하고 있습니다. 요즘, EMBEDDED 하시는 분 말고 누가 소스로부터 리눅스를 설치할까요? 그냥 쉽게 가면 좋죠. : - )

다만, 이 정보를 통하여 리눅스를 다루는 것이 쉬워질 것이며, POSIX 표준에 대한 이해, 그리고 디렉토리 표준 구조 등을 알게 되실 것 입니다. 이런 것을 알게 되면, AIX, 솔라리스, FREEBSD 등 어떤 OS 가 되었든, 작은 러닝 커브를 넘는 것만으로 쉽게 여러 OS를 다루실 수 있을 것입니다. 저는 보통 POSIX 계열이란 말을 자주 사용하는데, 거기서 거기거든요. 믿거나 말거나

우리 개발자님들 자신만의 리눅스를 만드는 것에 호기심이 가득하지 않을까요? 그런 호기심이 가득 찬 개발자님들께 많은 도움이 되는 글이 되었으면 하네요.

### 1. [audience](https://www.linuxfromscratch.org/lfs/view/stable/prologue/audience.html)

One important reason for this project's existence is to help you learn how a Linux system works from the inside out. Building an LFS system helps demonstrate what makes Linux tick, and how things work together and depend on each other.

저작자는 이 프로젝트를 통하여 "Linux 시스템이 내부에서 외부로 작동하는 방식을 배우는 데 도움"을 가장 중요한 이유로 내 걸었습니다.

LFS allows you to create very compact Linux systems.

저는 "매우 컴팩트한 리눅스 시스템을 만들 수 있다."란 이 말이 제일 마음에 드네요.

Another advantage of a custom built Linux system is security.

사실, 이것도 잘만 다루면 보안에 대한 이점이 있는 것도 사실이지요.

In the end, education is by far the most powerful of reasons.

궁극적인 이유죠. 리눅스에 대한 교육이겠죠.

### 2. [architecture](https://www.linuxfromscratch.org/lfs/view/stable/prologue/architecture.html)

"AMD/Intel x86. x86_64 CPU"를 대상으로 설명하는 것입니다. 사실, ARM 등도 유사하긴 합니다. 어 신기하네요. 예전에는 ARM, Power-PC 용 문서도 있었는데, 지금은 보이지 않습니다. 믿거나 말거나 제가 처음 문서를 접했을 때는 버전 5 입니다. 지금은 버전 11 이구요. 기억이 가물가물하다는 것을 감안해주세요. : - )

| Architecture | Build Time    | Build Size |
| 32-bit       | 239.9 minutes | 3.6 GB     |
| 64-bit       | 233.2 minutes | 4.4 GB     |

대략의 빌드 타임입니다. 4시간 걸리죠. 쉽지 않아요. 리눅스를 사용하려 하시면 그냥 배포판을 사용하는 것이 좋습니다. 설치에서 사용까지 10분 미만이거든요. 다만, 특별한 OS를 구축해야 할 필요가 있는 업무나 혹은 개인적인 호기심을 가진 분들은 4시간 정도 투자하셔도 되지요. 아마도 긱(Geek)스러운 기질을 가지신 분들은 흐흐흐! 시도해 보실 것 같습니다.

### 3. [prerequisites](https://www.linuxfromscratch.org/lfs/view/stable/prologue/prerequisites.html)

아래와 같은 지식이 필요하며, 그렇지만, 문서를 읽고 직접 부딪쳐 가면서 배우실 수 있습니다. 어렵지 않아요. 믿거나 말거나

- Unix 시스템 관리에 대한 일정 수준의 기존 지식
- [소프트웨어 빌드 및 설치에 대한 지식](https://tldp.org/HOWTO/Software-Building-HOWTO.html)
- [소스로부터 설치하는 가이드](http://moi.vonos.net/linux/beginners-installing-from-source/)

PS. 표준 스펙에 대한 내용까지 적었는데, -388자 라고 글을 못 올리네요. 아쉽게도 내일 표준에 대한 이야기부터 공유 드리겠습니다. 링크도 공유드렸으니 미리 살펴 보셔도 좋지 않을까 합니다. 어렵지 않고 쉽고, 리눅스를 소스에서 설치하는 것보다 중요한 것은 표준 문서를 보고 이 작동 원리를 아는 것입니다. 허풍도 떨고 장난스럽게 쓰려 하지만, 놓치지 않으셨으면 합니다. 설치를 해보았다가 아니라 "이런 원리구나", "표준이 이러하구나"라는 것을 놓치지 마시길 부탁드립니다. 월요일 즐거운 한주를 시작하길 빌겠습니다.

### 4. [standards](https://www.linuxfromscratch.org/lfs/view/stable/prologue/standards.html)

여기의 링크는 느긋하게 한 번 읽어 보시길 제안 드립니다. 표준은 중요합니다. 표준에서 벗어나지 않습니다. 살짝 Windows 가 표준에서 벗어났지만, 이름이 알려진 OS 들은 이 표준을 거의 따르고 있답니다.

- POSIX.1-2008 / https://lnkd.in/gWCDug2x
- Filesystem Hierarchy Standard (FHS) Version 3.0 / https://lnkd.in/gYBkud3V
- Linux Standard Base (LSB) Version 5.0 (2015) / https://lnkd.in/gzTGZr5Q

"Many people do not agree with the requirements of the LSB." 이 문구가 눈에 띄네요. 사실 많은 사람들이 취향이 다양하기 때문에 굳이 따르지 않는다. 다만, 의무 사항 같은 SHOULD 의 느낌이네요.

그래픽 관련한 부분이 없기 때문에, 아래와 같이 CORE 부분에 대한 요구를 충족시킨다고 합니다.

LSB Core: Bash, Bc, Binutils, Coreutils, Diffutils, File, Findutils, Gawk, Grep, Gzip, M4, Man-DB, Ncurses, Procps, Psmisc, Sed, Shadow, Tar, Util-linux, Zlib


### 5. [package](https://www.linuxfromscratch.org/lfs/view/stable/prologue/package-choices.html)

아래의 패키지들이 여기서 사용되어지며 각 패키지들이 상호 연관되어 있기도 합니다. 고수분들은 몇몇 패키지들을 빼서 사용하실 수도 있다고 생각되는데, 제 생각에는 아래 패키지는 거의 MUST 라고 봅니다. : - )

- ACL (Access Control Lists)
- Attr
- Autoconf
- Automake
- Bash
- Bc
- Binutils
- Bison
- Bzip2
- Check
- Coreutils
- DejaGNU
- Diffutils
- E2fsprogs
- Eudev
- Expat
- Expect
- File
- Findutils
- Flex
- Gawk
- GCC
- GDBM
- Gettext
- Glibc
- GMP
- gperf
- Grep
- Groff
- GRUB
- Gzip
- Iana-etc
- Inetutils
- Intltool
- IProute2
- Kbd
- Kmod
- Less
- Libcap
- Libelf
- Libffi
- Libpipeline
- Libtool
- Linux Kernel
- M4
- Make
- Man-DB
- Man-pages
- Meson
- MPC
- MPFR
- Ninja
- Ncurses
- Openssl
- Patch
- Perl
- Pkg-config
- Procps-NG
- Psmisc
- Python 3
- Readline
- Sed
- Shadow
- Sysklogd
- Sysvinit
- Tar
- Tcl
- Texinfo
- Util-linux
- Vim
- XML::Parser
- XZ Utils
- Zlib
- Zstd

PS. 아쉽네요. 아! 패키지 옆에 패키지에 대한 설명도 달아 놓았는데, -1500 자라고 빨간색 경고가 뜨네요. 표준 문서는 꼭 읽어 보세요. 그리고 POSIX 문서가 많아 보이는데, 그래도 조금씩 조금씩 읽어 보시면 POSIX 계열의 OS 가 거기서 거기란 것을 알 수 있습니다. MAN PAGE 나 HELP 문서 정도 보면 다 다룰 수 있답니다. 믿거나 말거나 ; - )

### 6. [구성](https://www.linuxfromscratch.org/lfs/view/stable/prologue/organization.html)

목차는 중요합니다. 언제나 목차의 시작은 "Introduction" 부터지요. 어김없네요.

- 1. 소개: 설치를 진행하는 방법에 대한 몇 가지 중요한 참고 사항을 설명합니다. 사실 소개만 읽어도 다 읽은 거죠. 시작이 반이잖아요.
- 2. 빌드를 위한 준비: 파티션 만들기, 패키지 다운로드 및 임시 도구 컴파일과 같은 구축 프로세스를 준비하는 방법에 대한 설명입니다. 소스를 컴파일해야지요. 그러면 뭐 이런 저런 준비 작업이 있지 않겠습니까? 그런거죠. 여행가방 싸듯이 비행기를 타면 티켓도 준비하고, 혹은 바베큐를 먹을거면, 여러가지 준비해야 하잖아요.
- 3. Cross 툴체인과 임시 도구의 구축: 시스템을 구성하는 데 필요한 툴체인과 임시 툴을 설치하는 것에 대한 설명입니다. 이쪽은 여행지에 도착해서 이제 텐트도 치고, 바베큐 준비도 하는, 즉 여행 가방을 풀고 본격적으로 놀기 시작하기 위해서 여행지에 세팅을 하는 것이라 생각하시면 좋을 것 같습니다.
- 4. 시스템 구축: 시스템 구축에 필요한 패키지를 하나씩 컴파일하고 설치하며, 부트 스크립트를 설치하고 커널을 설치하는 과정을 설명합니다.
- 5. 부록: 뭐 설명이 따로 필요 있나요? 어김없이 당연한 섹션입니다. 항상 소개부터 시작해서 부록으로 끝나는거죠.

### 7. [정오표 및 보안 권고](https://www.linuxfromscratch.org/lfs/view/stable/prologue/errata.html)

문제로 인해서 특정 기능이 제대로 동작하지 않을 경우가 있을지도 모르는데, 그 때 아래 링크가 도움이 되실 것입니다.

https://lnkd.in/ep_u_GZH

또한, 보안적인 문제 및 권고 사항을 확인하시려면 아래의 링크에서 확인하시면 됩니다.

https://lnkd.in/eRPEvy8Y

PS. 다음부터 "파트1"로 넘어가려 합니다. 여행 준비 하는 거죠. "파트1"의 내용 중에 가장 중요한 부분이 "How to Build an LFS System" 입니다. "그래서 어떻게 해야하는데?"에 대한 질문에 답인 거죠. 나머지는 디테일한 부분일 것입니다. 오늘은 "-100자" 이런 문구가 뜨지 않네요. 하하하! 양 조절 잘한 듯 보입니다.

### 8. [How to Build an LFS System](https://www.linuxfromscratch.org/lfs/view/stable/chapter01/how.html)

이미 설치되어 있는 리눅스를 사용하여 LFS 시스템을 구축합니다. 저는 아마도 UBUNTU 설치 USB를 가지고 시작할 것 입니다. 기존의 리눅스 시스템을 시작점으로 컴파일러, 링커, 쉘 등 새 시스템을 구축하는 데 필요한 프로그램을 추가적으로 설치할 것입니다.

2장은 설치하려는 리눅스의 기본 파티션과 파일 시스템을 만드는 방법을 설명합니다. 이 파티션에 패키지나 커널을 컴파일하여 설치하는 것이죠.

3장은 LFS 시스템을 구축하기 위해 다운로드해야 하는 패키지와 패치 그리고 새 파일 시스템에 그것을 저장하는 방법을 설명합니다.

4장은 작업을 위해서 환경을 설정해 주는 것들을 설명합니다. 주의 깊게 읽으라고 하네요.

5장은 호스트 시스템에서 새로운 툴을 분리하기 위해 Cross 컴파일로 초기 툴체인(binutils, gcc, glibc)를 설치하는 방법을 설명합니다.

6장에서는 구축한 크로스 툴체인을 사용하여 기본 유틸리티를 컴파일하는 방법을 설명합니다.

7장에서는 "chroot" 환경으로 들어가 이전에 빌드된 툴을 사용하여 최종 시스템을 빌드하고 테스트하는 데 필요한 추가적인 툴들들 빌드합니다.

5장을 시작하기 전에 "Toolchain Technical Notes" 에서 호스트와 새 시스템을 분리하는 기술적인 이유에 대해서 알게 될 것입니다. 느긋하세요.

8장에서는 이제 LFS 시스템을 구축합니다.

9장과 10장에서는 커널과 부트로더를 설정하는 것을 설명합니다.

11장에서는 "The End" 그렇죠. 그 다음에 나오는 것은 "Appendix" 라는 것이죠.

- 1. 기존 리눅스 상에서 시작하여
- 2. 설치를 위한 파일시스템을 만들고
- 3. 시스템 구축에 필요한 패키지와 패치를 다운로드하고
- 4. 작업을 위해 환경을 설정하고 (주의 깊게 읽을 것)
- 5. 크로스 컴파일 및 툴체인 설치 (gcc, binutils, glibc)
- 6. 크로스 툴체인을 이용하여 기본 유틸리티를 컴파일
- 7. "chroot" 를 이용하여 최종 시스템을 빌드하고 테스트하고 추가적인 툴들을 설치하고
- 8. 실제 시스템을 구축하고
- 9. 커널과 부트로더를 설정하는 것

그리하여 대망의 자신만의 컴팩트한 리눅스 시스템을 설치하는 것이 막을 내리겠죠. The End. 어렵지 않아요.

아래의 링크는 문제가 생기면 혹시나 저한테 묻지 마시고, 아래에 FAQ, MAILING LISTS, IRC 등을 이용하시는 것이 더 좋죠. 그런거죠. : - )

FAQ / https://lnkd.in/eiCUwkdP
Mailing Lists / https://lnkd.in/eZ3UFMr7 / https://lnkd.in/eMZc99dN
IRC / irc.libera.chat (support channel name : # LFS-support)
Mirror Sites / https://lnkd.in/eWrUbWjy

PS. 오늘도 양조절 잘했습니다. 흐흐흐! 다음에는 빌드를 위한 준비 작업을 설명할 것 입니다. 저는 우분투 설치 CD 를 이용하여 우분투를 설치하는 것이 아니라 저만의 리눅스를 설치하려 합니다. 아마도, 여기 있는 버전을 그대로 따르지는 않을 것 같습니다. 최신 버전을 사용해서 설치 쉘 스크립트를 만들려 해보고 있지요. 실패할 것 같은데 뭐 해보죠. 늘 그런거죠. 시도! 그리고 실패! 그것을 극복! 킄! 시간만 더 걸릴 것 같네요. : - (
    