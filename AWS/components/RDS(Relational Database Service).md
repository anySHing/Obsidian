> "Amazon RDS(Relational Database Service)를 사용하면 클라우드에서 관계형 데이터베이스를 간편하게 설정, 운영 및 확장할 수 있습니다. 하드웨어 프로비저닝, 데이터베이스 설정, 패치 및 백업과 같은 시간 소모적인 관리 작업을 자동화하면서 비용 효율적이고 크기 좆어 가능한 용량을 제공합니다. 사용자가 애플리케이션에 집중하여 애플리케이션에 필요한 빠른 성능, 고가용성, 보안 및 호환성을 제공할 수 있도록 지원합니다."

> RDS의 아키텍쳐

![[스크린샷 2024-02-21 오후 10.09.27.png]]

> RDS의 특징

- 관계형 데이터베이스를 제공하는 서비스
	- Relation Database Service: 관계형 데이터베이스
	- <-> NoSQL(DynamoDB, DocumentDB, ElastiCache)
- 가상 머신 위에서 동작
	- 단 시스템에 직접 로그인 불가능 -> OS 패치, 관리 등은 AWS의 역할
- Serverless 서비스가 아님
	- 단 Aurora Serverless는 말 그대로 Serverless 서비스
- CloudWatch와 연동
	- DB 인스턴스의 모니터링(EC2와 동일)
	- DB에서 발생하는 여러 로그(Error Log, General Log 등)를 CloudWatch와 연동하여 확인 가능
- 내부에서는 EC2를 활용
	- VPC 안에서 동작
		- 기본적으로 public IP를 부여받지 않아 외부에서 접근 불가능
		- 설정에 따라 Public으로 오픈 가능(DNS로 접근)
	- 서브넷과 보안 그룹 지정 필요
	- EC2 타입의 지정 필요
	- 스토리지는 EBS를 활용
		- EBS 타입의 선택 필요
		- 생성 시 EBS의 용량을 지정해서 생성
- Parameter Group: Root 유저만 설정 가능한 DB의 설정 값들을 모아 그룹화한 개념
	- DB 클러스터에 따라 파라미터 그룹을 적용시켜 설정 값을 적용
- 업데이트
	- 마이너 버전 엔진 업데이트는 자동으로 업데이트 설정 가능
	- 기터 업데이트의 경우 점검 시간(Maintenance Window)을 설정하여 특정 시간에 업데이트가 이루어질 수 있도록 설정 가능

> RDS의 인증 방법

- 전통적인 유저/패스워드 방식
	- AWS Secret Manager와 연동하여 자동 로테이션 가능
- IAM DB 인증
	- 데이터베이스를 IAM 유저 크레덴셜/Role을 통해 관리 가능
- Kerberos 인증

> RDS의 가격 모델

- 컴퓨팅 파워 + 스토리지 용량 + 백업 용량 + 네트워크 비용
- Reserved Instance 구매가능
	- EC2와 마찬가지로 일정 기간을 계약하여 저렴한 가격에 서비스를 비용

> RDS에서 제공하는 DB 엔진

- MS SQL Server: 오픈 소스가 아니기 때문에 라이센스 비용 추가(자신의 라이센스 사용 가능)
- Oracle: 오픈 소스가 아니기 때문에 라이센스 비용 추가(자신의 라이센스 사용 가능)
	- Oracle OLAP
- MySQL Server
- PostgreSQL
- Maria DB
- Amazon AUrora

> RDS의 암호화

- 암호화 지원
	- SQL 서버 혹은 Oracle에서는 TDE(Transparent Data Encryption) 지원
- 모든 엔진에서 EBS 볼륨 암호화 지원
	- Default Master Key 혹은 생성한 Master Key 선택 가능
- 자동 백업, 스냅샷, Read Replica 등에 적용됨

> RDS의 백업

- 자동 백업
	- 매일마다 스냅샷을 만들고 트랜잭션 로그를 저장
	- 데이터는 S3에 저장되며 데이터베이스의 크기 만큼의 공간 점유
	- 저장된 데이터를 바탕으로 일정 기간 내의 특정 시간으로 롤백 가능
		- 다른 DB 클러스터를 새로 생성
	- 1~35일 까지 보관 지원
	- Backup을 시행할 때는 약간의 딜레이 발생 가능성
	- 기본적으로 사용 상태로 설정되어 있음
- 수동 백업(DB 스냅샷)
	- 유저, 혹은 다른 프로세스로부터 요청에 따라 만들어지는 스냅샷
	- 데이터베이스가 삭제된 이후에도 계속 보관
	- 스냅샷의 복구는 항상 새로운 DB Instance를 생성하여 수행

> RDS Multi AZ

- 두 개 이상의 AZ에 걸쳐 데이터베이스를 구축하고 원본과 다른 DB(StandBy)를 자동으로 동기화(Sync)
	- SQL Server, Oracle, MySQL, PostgresSQL, MariaDB에서 지원
	- Aurora의 경우 다중 AZ를 설계 단계에서 지원
- 원본 DB의 장애 발생 시 자동으로 다른 DB가 원본으로 승격됨(DNS가 StandBy DB로)
- StandBy DB는 접근 불가능
- 퍼포먼스의 상승 효과가 아닌 안정성을 위한 서비스
- ![[스크린샷 2024-02-21 오후 10.49.15.png]]
	- RDS Primary가 사용 불가능할 경우 자동으로 RDS StandBy로 DNS가 옮겨짐
- 비용이 두배

> 읽기 전용 복제본(Read Replica)

- 원래 데이터베이스의 읽기 전용 복제본을 생성(Async)
	- 쓰기는 원본 데이터베이스에, 읽기는 본제본에서 처리하여 워크로드 분산
	- MySQL, PostgresSQL, MAriaDB, Oracle, Aurora에서 지원
- 안전성이 아닌 퍼포먼스를 위한 서비스
- 총 5개 까지 생성 가능
- 각각의 복제본은 고유 DNS가 할당됨 -> 접근 가능
	- 원본 DB의 장애 발생 시 수동으로 DNS 변경이 필요함
	- 복제본 자체에 Multi-AZ 설정 가능(MySQL, Maria DB, PostgreSQL, and Oracle)
	- Multi-AZ DB에 Read Replica 설정 가능
	- 자동 백업이 활성화 되어 있어야 읽기 전용 복제본 생성 가능
	- 각 DB의 엔진 버전이 다를 수 있음
- ![[스크린샷 2024-02-21 오후 10.52.35.png]]

> RDS Multi Region

- 다른 리전에 지속적으로 동기화시키는 DB 클러스터를 생성
	- Async 복제
- 주로 로컬 퍼포먼스 혹은 DR 시나리오로 활용
- 각 리전 별로 자동 백업 가능
- 리전 별로 Multi-AZ 가능


|  | Multi-AZ | Multi-Region | Read Replica |
| :--: | :--: | :--: | :--: |
| 목적 | 고가용성<br> | DR / 로컬 퍼포먼스 | 확장성 / 성능 |
| 복제 방식 | Sync | Async | Async |
| 액티브 | Primary DB만 Read/Write 가능 | Read만 가능 | Read만 가능 |
| 백업 | 자동 백업(StandBy 기준) | 자동 백업 가능 | 기본적으로 백업 X |
| 엔진 업데이트 | Primary만 업데이트 | 각 리전 별로 다른 업데이트 | DB별로 다른 업데이트 |
| Fail Over | 자동으로 StandBy로 Failover | 수동으로 FailOver | 수동으로 Failover |
