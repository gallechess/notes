# Windows Service로 동작하는 JVM의 dump 생성

## 1. 개요
Windows OS에서 Java 프로그램을 Windows Service로 등록하여 운영하는 경우 작업 관리자에서는 java 프로세스 대신 이를 감싸고 있는 네이티브 프로세스만 보이게 된다. 이 경우 일반적인 방법으로는 JVM Thread/Heap dump를 생성할 수 없다. 여기서는 이러한 상황에서 JVM 모니터링 및 이슈 분석을 위해 Thread/Heap dump를 생성하는 방법에 대해 설명한다.

## 2. JVM Thread/Heap dump
Java 1.6 버전부터는 JVM의 Thread/Heap dump 생성을 위한 프로그램이 JDK에 내장되어 있다. jstack.exe와 jmap.exe이 그것이며, 아래와 같이 손쉽게 덤프를 생성할 수 있다.
* Thread dump 생성 : jstack.exe [Process ID] > [생성할 덤프 파일명]
* Heap dump 생성 : jmap.exe -dump:format=b,file=[생성할 덤프 파일명] [Process ID]

하지만 Java 프로그램이 Windows Service로 등록되어 동작하게 되면 java 프로세스 대신 이를 감싸는 네이티브 프로세스(WebLogic의 beasvcX64.exe, Tomcat의 tomcat.exe, Apache Commons Daemon의 prunsrv.exe 등)만 보이게 되어 jstack/jmap 프로그램을 사용해도 덤프가 생성되지 않는다. 이러한 경우에 jstack/jmap을 사용할 수 있는 방법이 있는데 바로 Sysinternals Suite의 PsExec 프로그램을 활용하는 방법이다.

## 3. Sysinternals Suite와 PsExec
Sysinternals Suite는 윈도우 OS 상에서 트러블슈팅이나 모니터링에 도움 되는 유틸리티들의 모음이다. 프로세스/네트워크/메모리/디스크 등을 모니터링 할 수 있는 여러가지 용도의 유틸리티들이 있으며 대표적으로 많이 쓰이는 유틸리티로는 Process Explorer가 있다. Sysinternals Suite에 포함된 유틸리티들은 특별한 설치가 필요없이 실행파일만 실행하여 구동이 가능하며, 32-bit/64-bit에 관계없이 사용 가능하다.
* Sysinternals Suite 홈페이지 : <https://technet.microsoft.com/ko-kr/sysinternals/bb842062>

PsExec는 Sysinternal Suite에 포함된 유틸리티 중 하나로서 리모트 시스템에 접근하여 특정 명령어를 실행하는 기능을 제공한다. 홈페이지에는 다음과 같이 소개되어 있다.
* PsExec is a light-weight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications, without having to manually install client software.
* PsExec 홈페이지 : <https://technet.microsoft.com/ko-kr/sysinternals/bb897553>

PsExec의 본래 목적은 리모트 시스템에 대해 명령어를 실행하는 것이지만 로컬에서 Windows Service로 동작하는 Java 프로그램에 대해 jstack/jmap을 통한 Thread/Heap dump 생성을 가능하게 해준다.

## 4. PsExec를 통한 Thread/Heap dump 생성
PsExec를 통해 JVM 덤프를 생성하는 것은 간단히 아래와 같이 할 수 있다.
* Thread dump 생성
> ```
> PsExec.exe -s [full-path jstack.exe] [Process ID] > [생성할 덤프 파일명]
> ```
> 예시) PsExec.exe -s "C:\Program Files\Java\jdk1.6.0_13\bin\jstack.exe" 2528 > C:\dump\thread.txt

* Heap dump 생성
> ```
> PsExec.exe -s [full-path jmap.exe] -dump:format=b,file=[생성할 덤프 파일명] [Process ID]
> ```
> 예시) PsExec.exe -s "C:\Program Files\Java\jdk1.6.0_13\bin\jmap.exe" -dump:format=b,file=C:\dump\heap.bin 2528

덤프 생성 시 간혹 '사용 가능한 저장소가 부족하여 이 명령을 처리할 수 없습니다'라는 오류 메시지가 표시되는 경우가 있는데, 이 때는 PsExec를 사용하지 않고 직접 jstack/jmap을 실행하면 덤프를 생성할 수 있다.
