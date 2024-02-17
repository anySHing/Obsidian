> Continuous Integration / Continuous Delivery(CI/CD)

- 애플리케이션 개발 단계를 자동화하여 애플리케이션을 보다 짧은 주기로 고객에게 제공하는 방법
- 자주 빌드하고 자주 배포 -> 유저에게 빠르게 제품을 전달
	- 더 빠르게 버그가 수정되고 유저의 요구사항이 반영될 수 있는 시스템

## AWS CodeCommit

> "AWS CodeCommit은 프라이빗 Git 레포지토리를 호스팅하는 안전하고 확장성이 뛰어난 관리형 소스 제어 서비스입니다."

- AWS 버전의 Github
- IAM 크레덴셜을 통해 접근 가능(기존의 HTTP 프로토콜, SSH 역시 지원)
- AWS의 다양한 서비스와 연동
- 이벤트 버스를 통한 이벤트 기반 로직 처리 가능
- 가격이 싸다!
- CI/CD중 CI(Continuous Integration)를 담당
- 별도의 프로비전 불필요
- 간단하게 빌드 세팅이 된 서버 하나 빌려서 사용하는 개념
- 좀 느림

## AWS CodeDeploy

> "AWS CodeDeploy는 Amazon EC2, AWS Fargate, AWS Lambda 및 온프레미스 서버와 같은 다양한 컴퓨팅 서비스에 대한 소프트웨어 배포를 자동화하는 완전관리형 배포 서비스입니다."

- 빌드/테스트된 제품을 배포해주는 서비스
- 다양한 배포 모드(In-Place, Blue/Green 등) 지원
- 배포 실패 시 자동으로 롤백 지원
- 배포 상태를 모니터링 가능

## AWS CodePipeline

> "AWS CodePipeline은 빠르고 안정적인 애플리케이션 및 인프라 업데이트를 위해 릴리스 파이프라인을 자동화하는 데 도움이 되는 완전관리형 지속적 전달 서비스입니다."

- 전체 프로세스 오케스트레이션
- Serverless
- 이벤트 기반 운영 가능
- 다양한 AWS 서비스 연동 가능
- 다양한 외부 서비스 연동 가능(Jenkins 등)

## 아티팩트

- 파이프라인의 각 스테이지에서 다음 스테이지로 전달하기 위한 작업을 수행한 결과물
	- 빌드된 애플리케이션, 테스트 결과 내용, 작업 수행 내용 등

## BuildSpec.yml

- Codebuild에서 수행할 내용을 정의한 문서
	- yml 형식
- 정의 가능한 내용
	- 빌드 환경(예: node.js Python 등), 환경 변수
	- 스테이지 별 명령어
		- install, pre_build, build, post_build
	- 아티팩트 설정
		- path 및 구성 방법
	- 기타(cache, proxy 등)

## AppSpec.yml

- CodeDeploy에서 수행할 내용을 정의한 문서
	- yml 형식
- 정의 가능한 내용
	- 배포 방법
	- 각 스테이지 별 수행 내용 정의

## AWS CodeBuild

- 