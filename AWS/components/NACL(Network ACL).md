- 보안 그룹처럼 방화벽 역할을 담당
- 서브넷 단위
- 포트 및 IP를 직접 Deny 가능
	- 외부 공격을 받는 상황 등 특정 IP를 Block하고 싶을 때 사용
- 순서대로 규칙 평가(낮은 숫자부터)

> NACL 규칙

- 규칙 번호: 규칙에 부여되는 고유 숫자이며 규칙이 평가되는 순서. (낮은 번호부터)
	- AWS 추천은 100단위 증가
- 유형: 미리 지정된 프로토콜. 선택 시 AWS에서 잘 알려진 포트(Well Known Port)가 규칙에 지정됨
- 프로토콜: 통신 프로토콜
	- ex) TCP, UDP, SMP, ...
- 포트 범위: 허용 혹은 거부할 포트 범위
- 소스: IP 주소의 CIDR 블록
- 허용/거부: 허용 혹은 거부 여부

> AWS NACL 규칙 순서


112.12.35.4 주소로부터 DDOS 공격을 받고있는다고 가정

![[스크린샷 2023-12-26 오후 4.27.34.png]]

- 규칙 번호 100: TCP 프로토콜의 80번 포트로 들어오는 112.12.35.4/32 (완전히 구체적인 지정)주소의 요청을 거부
- 규칙 번호 200: TCP 프로토콜의 80번 포트로 들어오는 모든 요청을 허용
- 규칙 번호 \*: 규칙 번호 100에 허용되지 않는 모든 요청을 거부

규칙 번호는 낮은 숫자부터 차례대로 적용되기 때문에 100번에 TCP 프로토콜 80번 포트 0.0.0.0/0 을 적용하게 되면 DDOS 공격을 막지 못하고 그냥 모든 요청을 받아버리게 된다.