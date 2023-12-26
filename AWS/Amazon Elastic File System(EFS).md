> "Amazon EFS(Amazon Elastic File System)는 AWS 클라우드 서비스와 온프레미스 리소스에서 사용할 수 있는, 간단하고 확장 간응하며 탄력적인 완전 관리형 NFS 파일 시스템을 제공합니다. 이 제품은 애플리케이션을 중단하지 않고 온디맨드 방식으로 페타바이트 규모까지 확장하도록 구축되어, 파일을 추가하고 제거할 때 자동으로 확장하고 축소하며 확장 규모에 맞게 용량을 프로비저닝 및 관리할 필요가 없습니다."

> 개요

이전에 EBS에 대해서 알아볼 때 EBS는 일반적으로 하나의 EC2와만 연결되어 있다. (Multi-Attach는 예외로 한다)

근데 경우에 따라서는 여러 개의 EC2에 고르게 스토리지가 필요할 경우가 있다.

-> 세션을 같이 저장한다거나, 소스 코드를 같이 저장한다거나, 빅데이터 / 머신러닝을 위한 데이터를 분산처리하기 위해 공유된 스토리지를 가질 필요가 있음

이 때 EFS를 활용할 수 있다.

> 정의

- NFS 기반 공유 스토리지 서비스(NFSv4)
	- 따로 용량을 지정할 필요 없이 사용한 만큼 용량이 증가 <-> EBS는 미리 크기를 지정해줘야 함
	- 페타바이트 단위까지 확장 가능
	- 몇 천개의 동시 접속 유지 가능
	- 데이터는 여러 AZ에 나누어 분산 저장
	- 쓰기 후 읽기(Read After Write) 일관성
	- Private Service: AWS 외부에서 접속 불가능
		- AWS 외부에서 접속하기 위해서는 VPN 혹은 Direct Connect 등으로 별도로 VPC와 연결 필요
	- 각 가용 영역에 Mount Target을 두고 각각의 가용 영역에서 해당 Mount Target으로 접근
	- LINUX ONLY

![[스크린샷 2023-12-26 오전 6.59.02.png]]

> Amazon EFS 퍼포먼스 모드

- General Purpose: 가장 보편적인 모드. 거의 대부분의 경우 사용 권장
- Max IO: 매우 높은 IOPS가 필요한 경우
	- 빅데이터, 미디어 처리 등

> Amazon EFS Throughput 모드

- Bursting Throughput: 낮을 Throughput일 때 크레딧을 모아서 높은 Throughput일 때 사용
	- EC2 T 타입과 비슷한 개념
- Provisioned Throughput: 미리 지정한 만큼의 Throughput을 미리 확보해두고 사용

> Amazon EFS 스토리지 클래스

- EFS Standard: 3개 이상의 가용 영역에 보관
- EFS Standard-IA: 3개 이상의 가용 영역에 보관, 조금 저렴한 비용 대신 데이터를 가져올 때 비용 발생
- EFS One Zone: 하나의 가용 영역에 보관 -> 저장된 가용 영역의 상황에 영향을 받을 수 있음
- EFS One Zone-IA: 저장된 가용 영역의 상황에 영향을 받을 수 있음. 데이터를 가져올 때 비용 발생(가장 저렴)

> Amazon FSx

- FSx for Windows File Server
	- EFS의 윈도우즈 버전
	- SMB 프로토콜을 활용
	- Microsoft Active Directory와 통합 등의 관리 기능 사용 가능
	- LINUX, MacOS 등의 다른 OS에서도 활용 가능
- FSx for Lustre
	- 리눅스를 위한 고성능 병렬 스토리지 시스템
	- 주로 머신러닝, 빅데이터 등의 고성능 컴퓨팅에 사용
	- AWS 밖의 온프레미스에서 액세스 가능

> 실습

- EFS를 활용한 스토리지 공유 웹서버 만들기
	- Amazon EFS를 위한 보안 그룹 생성
	- Amazon EFS 생성
	- EC2 인스턴스 3개 프로비전
		- 유저 데이터
			- 생성한 EFS를 마운트
			- 웹 서버 생성 / 실행
	- 각 웹서버에서 스토리지를 공유하는 것을 확인
	- 리소스 정리
