+++
author = "defact0"
title = "nsis 스크립트 사용하기"
date = "2020-02-13"
description = "How to use nsis"
tags = [
    "script",
]
categories = [
    "dev",
]
+++

**NSIS**(`Nullsoft Scriptable Install System`, 널소프트 스크립터블 인스톨 시스템)는 스크립트 기반으로 동작하는 `윈도용 설치 시스템`으로,  
 윈앰프를 만든 것으로 알려져 있는 널소프트가 지원하는 가벼운 설치 시스템이다. NSIS는 인스톨실드와 같은 상용 사유 제품들을 널리 대체하면서 인기를 끌고 있다.

<!--more-->


# 1.	NSIS 설치 및 환경구성

## 1.1	프로그램 다운로드

- NSIS (Nullsoft Scriptable Install System) is a professional open source system 
to create Windows installers.  
<http://nsis.sourceforge.net/Download>

- HM NIS Edit is the best Editor/IDE for Nullsoft Scriptable Install System (NSIS).  
<https://nisedit.software.informer.com/>

## 1.2	스크립트 검색하기

- 스크립트 작성 및 편집은 HM NIS Edit와 Notepad++프로그램을 번갈아 사용한다.
- <b>[Ctrl]+[F]</b>키를 누르면 아래 그림과 같은 화면이 나오고 [파일에서 찾기] 메뉴를 클릭하면,
화면이 변경되는데 <b>필터, 디렉토리</b>에 검색할 조건을 지정하고 <b>찾을 내용</b>에 검색어를 입력한 뒤
<b>[모두찾기]</b>버튼을 누르면 쉽게 원하는 스크립트를 검색할 수 있다.

![1](/img/2020/02/13/1.png)
 
## 1.3	NSIS 스크립트 컴파일 방법
- NM NIS Edit을 사용한 스크립트 컴파일

![2](/img/2020/02/13/2.png)

- notepad++로 편집 후 스크립트 컴파일

![3](/img/2020/02/13/3.png)


# 2.	기본 예제

## 2.1	[MessageBox] 메시지 출력

- 스크립트 내용

```bash
# 실행파일 이름 정하기
OutFile "Hello.exe"

# 설치창 비활성
SilentInstall silent

# 메인 로직
Section "MainSection" SEC01
    MessageBox MB_OK "Hello, World!"
SectionEnd
```

## 2.2	[Delete/Rename/File] 파일교체

- 스크립트 내용

```bash
# 실행파일 이름 정하기
  OutFile "Files.exe" 

# 설치창 비활성
SilentInstall silent

# 메인 로직
Section "MainSection" SEC01

    SetOutPath "$WINDIR\..\" ; $WINDIR : The Windows directory    #설치 경로 설정 
    SetOverwrite on # 설치하려는 파일이 대상 디렉터리에 있을 경우 덮어 쓸 것인지에 대한 옵션
    Delete /REBOOTOK "$WINDIR\..\test.txt_old"             # 기존 백업파일 삭제
    Rename "$WINDIR\..\test.txt" "$WINDIR\..\test.txt_old"  # 백업
    File "02__Files_package\test.txt"                         # 지정한 폴더에 파일 1개만 복사
    ;File "02__Files_package/*.*"                             # 지정한 폴더에 모든 파일 복사 (옵션)
    ;Delete /REBOOTOK "$WINDIR\..\test.txt_old"            # 백업파일 삭제 (동작 확인을 위해 주석처리함)
    MessageBox MB_OK "END" 

SectionEnd
```

- 실행

![5](/img/2020/02/13/5.png)

 
## 2.3	[macro] 매크로 사용

![6](/img/2020/02/13/6.png)


 
## 2.4	[include/macro] 매크로 분리하기
- 스크립트를 작성하다 보면 자주 쓰는 매크로는 따로 include로 불러서 사용하도록 작성하는 것이 중복되는 스크립트 작성을 피할 수 있는 방법이다.

- 스크립트 내용 (rename.nsh)

```bash
# 파일 교체 매크로
!macro MA_Com_Rename DLLName
    Delete /REBOOTOK "${DLLName}_old"    # 기존 백업파일 삭제
    Rename "${DLLName}" "${DLLName}_old" # 백업
    Delete /REBOOTOK "${DLLName}_old"    # 백업파일 삭제
!macroend
```

- 스크립트 내용 (rename_main.nsi)

```bash
# MA_Com_Rename 매크로를 쓰기 위해 추가로드
!include rename.nsh

# 실행파일 이름 정하기
  OutFile "Macro.exe" 
# 설치창 비활성
  SilentInstall silent

# 메인 로직
Section "MainSection" SEC01

    SetOutPath "$WINDIR\..\" ; $WINDIR : The Windows directory
    SetOverwrite on

    !insertmacro MA_Com_Rename "$WINDIR\..\test.txt" 
    File "02__Files_package\test.txt"

    MessageBox MB_OK "END" 

SectionEnd
```

- 스크립트 설명
rename_main.nsi 스크립트 파일에서 파일 삭제 처리를 하는 매크로를 불러들이기 위해
!include rename.nsh 을 먼저 선언하였다.

![7](/img/2020/02/13/7.png)

이렇게 자주 쓰는 매크로를 따로 파일로 만들어 필요할 때 include하여 활용 가능하다.

 
## 2.5	[include] OS x86 x64판별하기

![8](/img/2020/02/13/8.png)
![9](/img/2020/02/13/9.png)