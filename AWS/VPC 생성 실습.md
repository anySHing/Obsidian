> VPC 관련 몇가지 알아둘 점

- 커스텀 VPC 생성 시 만들어지는 리소스: 라우팅 테이블, 기본 NACL, 기본 보안그룹
	- 서브넷 생성 시 모두 기본 라우팅 테이블로 자동 연동
- 서브넷 생성 시 AWS는 총 5개의 IP를 미리 점유
- VPC에는 단 하나의 Internet Gateway만 생성 가능
	- Internet Gateway 생성 후 직접 VPC에 연동 필요
	- Internet Gateway는 자체적으로 고가용성/장애 내구성을 확보
- 보안 그룹은 VPC 단위
- 서브넷은 가용 영역 단위(1서브넷 = 1가용영역)
- 가장 작은 서브넷 단위는 /28(11개, 16-5)

> VPC 생성 실습

- 직접 커스텀 VPC 생성(10.0.0.0/16)
- 서브넷 3개 생성: Public, Private, DB
- 각 서브넷에 각각 Routing Table 연동
- Public 서브넷에 인터넷 경로 구성: Internet Gateway
- Public Subnet에 EC2 프로비전
- Private SUbnet에 EC2 프로비전
	- Bastion Host
- DB Subnet에 DB 생성
	- NAST Gateway Setup
	- Private Subnet/Public Subnet에서 DB 접속 테스트