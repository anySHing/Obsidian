> "Elastic Load Balancing은 들어오는 애플리케이션 트래픽을 Amazon EC2 인스턴스, 컨테이너, IP 주소, Lambda 함수와 같은 여러 대상에 자동으로 분산시킵니다. Elastic Load Balancing은 단일 가용 영역 또는 여러 가용 영역에서 다양한 애플리케이션 부하를 처리할 수 있습니다. Elastic Load Balancing이 제공하는 세가지 로드 밸런서는 모두 애플리케이션의 내결함성에 필요한 고가용성, 자동 확장/축소, 강력한 보안을 갖추고 있습니다."

- 다수의 서비스에 트래픽을 분산 시켜주는 서비스
- Health Check: 직접 트래픽을 발생시켜 Instance가 살아있는지 체크
- Autoscaling과 연동 가능
- 여러 가용 영역에 분산 가능
- 지속적으로 IP 주소가 바뀌며 IP 고정 불가능: <span style="color: orange">항상 도메인 기반으로 사용</span>

> 종류

- [Application Load Balancer](#application-load-balancer)
- [Network Load Balancer](#network-load-balancer)
- [Classic Load Balancer](#classic-load-balancer)
- [Gateway Load Balancer](#gateway-load-balancer)
## Application Load Balancer

- 똑똑
- 트래픽을 모니터링하여 라우팅 가능
	- ex) image.sample.com -> 이미지 서버로, web.sample.com -> 웹 서버로 트래픽 분산

## Network Load Balancer

- 빠름
- TCP 기반 빠른 트래픽 분산
- Elastic IP 할당 가능

## Classic Load Balancer

- 옛날거
- 예전에 사용되던 타입으로 현재는 잘 사용하지 않음

## Gateway Load Balancer

- 먼저 트래픽 체크를 함
- 가상 어플라이언스 배포/확장 관리를 위한 서비스

![[스크린샷 2023-12-26 오전 2.29.32.png]]

> 대상 그룹(Target Group)

- ALB가 라우팅할 대상의 집합
- 구성
	- 3+1가지 종류
		- Instance
		- IP(Private)
		- Lambda
		- 다른 ALB
	- 프로토콜(HTTP, HTTPS, gRPC 등)
	- 기타 설정
		- 트래픽 분산 알고리즘, 고정 세션 등

![[스크린샷 2023-12-26 오전 2.32.23.png]]

> 아키텍쳐

![[스크린샷 2023-12-26 오전 2.33.26.png]]

