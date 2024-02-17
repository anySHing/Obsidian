![[스크린샷 2023-12-25 오후 9.14.14.png]]

> 중지

- 중지 중에는 인스턴스 요금 미청구
- 단 EBS 요금, 다른 구성요소(Elastic IP 등)는 청구
- 중지 후 재시작 시 퍼블릭 IP 변경
- EBS를 사용하는 인스턴스만 중지 가능: 인스턴스 저장 인스턴스는 중지 불가

> 재부팅

- 재부팅시에는 퍼블릭 IP 변동 없음

> 최대 절전모드

- 메모리 내용을 보존해서 재시작 시 중단 지점에서 시작할 수 있는 정지모드

| 인스턴스 상태 | 설명  | 인스턴스 사용 요금 |
       |: -------------: |: --------------------------------- :|: ------------------ :|
| <span style="color:orange">pending</span> | 인스턴스가 running 상태로 될 준비 | <span style="color: green">미청구</span> |
| <span style="color:green">running</span> | 인스턴스 사용 중 | <span style="color: red">청구</span> |
| <span style="color:orange">stopping</span> | 인스턴스가 중지 또는 최대 절전 모드로 전환 중 |<span style="color: green">미청구</span><br><span style="color: orange">최대절전 시 청구</span> |
| <span style="color:orange">stopped</span> | 인스턴스가 중지 상태: 재시작 가능 | <span style="color: green">미청구</span> |
| <span style="color:orange">shutting-down</span> |  인스턴스 종료 중 | <span style="color: green">미청구</span> |
| <span style="color:orange">terminated</span> | 인스턴스가 영구적으로 삭제됨 | <span style="color: green">미청구</span> |
