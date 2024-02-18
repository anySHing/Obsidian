> "AWS(Amazon Web Services)는 전 세계적으로 분포한 데이터 센터에서 200개가 넘는 완벽한 기능의 서비스를 제공하는, 세계적으로 가장 포괄적이며, 널리 채택되고 있는 클라우드 플랫폼입니다."

> 들어가는 개념

- [[리전(Region)|리전]]
- [[가용 영역(Availability Zone)|가용 영역]]
- [[엣지 로케이션(Edge Location)|엣지 로케이션]]
- [[ARN(Amazon Resource Name)|ARN]]
# 글로벌 서비스와 리전 서비스

AWS에는 서비스가 제공되는 지역의 기반에 따라 글로벌 서비스와 리전 서비스로 분류

> 글로벌 서비스: 데이터 및 서비스를 전 세계의 모든 인프라가 공유

- [[CloudFront]]
- [[IAM(Identity and Access Management)|IAM]]
- [[Route53]]
- [[WAF]]

> 지역 서비스: 특정 리전을 기반으로 데이터 및 서비스를 제공

- 대부분의 서비스
- [[S3(Simple Storage Service)|S3]]
	- *S3의 경우 전 세계에서 동일하게 사용할 수 있으나 데이터 자체는 리전에 종속: 리전 서비스*

> 정리

- AWS는 여러 리전과 리전 안의 2개 이상의 가용영역, 엣지 로케이션으로 구성
- [[리전(Region)|리전]]: AWS가 제공되는 서버의 물리적 위치
- [[가용 영역(Availability Zone)|가용 영역]]: 하나의 리전 안에 두 개 이상의 가용 영역이 있으며 하나 이상의 데이터 센터로 구성
- [[엣지 로케이션(Edge Location)|엣지 로케이션]]: AWS의 여러 서비스를 빠르게 제공하기 위한 거점(캐싱)
- AWS의 서비스는 글로벌 서비스와 리전 서비스로 구분
- AWS의 모든 리소스는 [[ARN(Amazon Resource Name)|ARN]]이 부여됨