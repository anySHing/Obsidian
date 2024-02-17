## NAT Gateway

> 개요

- "NAT 게이트웨이는 Amazon Virtual Private Cloud(Amazon VPC)의 프라이빗 서브넷에 있는 인스턴스에서 인터넷에 쉽게 연결할 수 있도록 지원하는 가용성이 높은 AWS 관리형 서비스입니다."

> Nat Gateway/NAT Instance

- Private 인스턴스가 외부의 인터넷과 통신하기 위한 통로
- NAT Instance는 단일 EC2 인스턴스 / NAT Gateway는 AWS에서 제공하는 서비스
- NAT Gateway/Instance는 모두 서브넷 단위
	- Public Subnet에 있어야 함
	- NAT Gateway는 서비스(관리 필요 없음)
	- NAT Instance는 EC2 인스턴스이기 때문에 관리 필요

## Bastion Host

> 개요

- "외부에서 사설 네트워크에 접속할 수 있도록 경로를 확보해주는 서버"
- Private 인스턴스에 접근하기 위한 EC2 인스턴스
- Public 서브넷에 위치해야 함