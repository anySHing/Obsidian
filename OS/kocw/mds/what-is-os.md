# 운영체제란 무엇인가?

- 컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층
  - 좁은 의미의 운영체제: 커널
    - 운영체제의 핵심으로 메모리에 상주하는 부분
  - 넓은 의미의 운영체제: 커널 뿐 아니라 각종 주변 시스템 유틸리티를 포함하는 개념

## 운영체제의 목표

- 컴퓨터 시스템을 편리하게 사용할 수 있는 환경을 제공

  - 운영체제는 동시 사용자 / 프로그램들이 각각 독자적 컴퓨터에서 수행되는 것 같은 환상을 제공
  - **하드웨어를 직접 다루는 복잡한 부분을 운영체제가 대행**

- **컴퓨터 시스템의 자원을 효율적으로 관리**
  - 프로세서, 기억장치, 입출력 장치 등의 효율적 관리
    - 사용자 간 형평성 있는 자원 분배
    - 주어진 자원으로 최대한의 성능
  - 사용자 및 운영체제 자신의 보호
  - 프로세스, 파일, 메시지 등을 관리

## 운영체제의 분류

1. **동시 작업 가능 여부**

- 단일 작업(single tasking)

  - 한번에 하나의 작업만 처리
    ex) MS-DOS 프롬프트 상에서는 한 명령의 수행을 끝내기 전엔 다른 명령을 수행시킬 수 없음

- 다중 작업(multi tasking): 요즘은 그냥 이걸로 보면 됨
  - 동시에 두 개 이상의 작업을 처리
    ex) UNIX, MS Windows 등의 OS는 한 명령의 수행이 끝나기 전에 다른 명령이나 프로그램들을 수행할 수 있음

2. **사용자의 수**

- 단일 사용자(single user)
  ex) MS-DOS, MS Windows

- 다중 사용자(multi users)
  ex) UNIX, NT server

3. **처리 방식**

- 일괄 처리(batch processing)
  - 작업 요청의 일정량을 모아서 한꺼번에 처리
  - 작업이 완전 종료될 때 까지 기다려야 함
    ex) 초기 Punch Card 처리 시스템
    현대 OS에선 찾아보기 어렵다.
- 시분할(time sharing)
  - 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
  - 일괄 처리 시스템에 비해 짧은 응답 시간을 가진다.
    ex) UNIX
  - **interactive**한 방식
    현대 OS는 시분할 방식
- 실시간(Realtime OS)
  - 정해진 시간 안에 어떠한 일이 반드시 종료됨이 보장되어야 하는 실시간 시스템을 위한 OS
    ex) 원자로 / 공장 제어, 미사일 제어, 반도체 장비, 로봇 제어
- 실시간 시스템의 개념 확장
  - Hard realtime system(경성 실시간 시스템)
  - Soft realtime system(연성 실시간 시스템)