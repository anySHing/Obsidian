## 보안그룹(Security Group)

> 개요

> "보안 그룹은 인스턴스에 대한 인바운드 및 아웃바운드 트래픽을 제어하는 가상 방화벽 역할을 합니다."

- Network Access Control List(NACL)와 함께 방화벽의 역할을 하는 서비스
- Port 허용
	- 기본적으로 모든 포트는 비활성화
	- 선택적으로 트래픽이 지나갈 수 있는 Port와 Source를 설정 가능
	- **Deny는 불가능** -> NACL로 가능함
- 인스턴스 단위(정확히는 ENI 단위)
	- 하나의 인스턴스에 하나 이상의 SG 설정 가능
	- NACL의 경우 서브넷 단위
	- 설정된 인스턴스는 설정한 모든 SG의 룰을 적용받음
		- 기본 5개, 최대 16개

> Stateful

- 보안그룹의 Stateful
	- 보안그룹은 Stateful
	- Inbound로 들어온 트래픽이 별 다른 Outbound 설정 없이 나갈 수 있음
	- NACL은 Stateless