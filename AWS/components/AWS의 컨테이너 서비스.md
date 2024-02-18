- 데스크탑 컴퓨터
	- 외부 기기가 필요함
		- 본체
		- 모니터
		- 키보드
		- 마우스
		- 스피커
	- 휴대성/이동성(Portability)이 없음
- 랩탑 컴퓨터
	- All In One
	- 사용에 모든 필요한 기기가 내장
	- 휴대성/이동성(Portability)이 충분

> 컨테이너란?

- 랩탑처럼 All In One
- 소프트웨어 애플리케이션을 실행하기 위해 필요한 모든 것을 담은 패키지
	- 코드
	- 운영체제(OS)
	- 설정 파일
	- 의존 파일(실행에 필요한 라이브러리 등)
- 대표적인 컨테이너 관리 플랫폼: Docker, Kubernates(k8s)
- ![[스크린샷 2024-02-19 오전 2.56.54 1 1.png]]

> AWS의 컨테이너 서비스

1. Amazon ECS(Elastic Container Service)
	1. EC2 Mode
	2. AWS Fargate
		1. AWS App Runner
2. Amazon EKS(Elastic Kubernetes Service)

> Amazon ECS

- AWS의 컨테이너 오케스트레잉션 서비스
- 컨테이너를 실행하고 배포하고 관리
- 두 가지 모드:
	- EC2: EC2를 활용한 컨테이너 서비스
	- Fargate: Serverless 컨테이너 서비스
- 다른 AWS 서비스와 연동 가능
	- ECR(Elastic Container Registry): AWS의 Docker Hub
	- ALB, CloudWatch
- 사용 사례:
	- AWS 상에서 컨테이너 기반 앱을 호스팅하는 일반적인 방법
	- 대규모의 애플리케이션의 구축/배포/실행

> ECS EC2 vs Fargate

- EC2:
	- 직접 관리 필요(스케쥴링 등)
	- VPC 안에 생성
	- 주로 대규모의 애플리케이션에 사용
	- 비용이 많이 소모됨
- ECS Fargate:
	- Serverless
	- VPC 밖에 생성되어 VPC에서 접근
	- 대규모 애플리케이션과 단기적인 작업에 주로 사용
	- 비교적 비용 관리가 쉬움