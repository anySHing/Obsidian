> "AWS (IAM)를 사용하면 AWS 서비스와 리소스에 대한 액세스를 안전하게 관리할 수 있습니다. 또한, AWS 사용자 및 그룹을 만들고 관리하며 AWS 리소스에 대한 액세스를 허용 및 거부할 수 있습니다."

- AWS Account 관리 및 리소스 / 사용자 / 서비스의 권한 제어
	- 서비스 사용을 위한 인증 정보 부여
- 사용자의 생성 및 관리 및 계정의 보안
	- 사용자의 패스워드 정책 관리(일정 시간마다 패스워드 변경 등)
- 다른 계정과의 리소스 공유
	- Identity Federation (Facebook 로그인, 구글 로그인 등)
- 계정에 별명 부여 가능 -> 로그인 주소 생성 가능
- IAM은 글로벌 서비스 (Region 별 서비스가 아님)

> IAM 구성

![[스크린샷 2023-12-25 오후 4.43.11.png]]

![[스크린샷 2023-12-25 오후 4.45.21.png]]

> IAM의 권한 검증

![[스크린샷 2023-12-25 오후 4.45.49.png]]

![[스크린샷 2023-12-25 오후 4.46.18.png]]

> 사용자의 종류

- 루트 사용자: 결제 관리를 포함한 계정의 모든 권한을 가지고 있음
	- 관리 목적 이외에 다른 용도로 사용하지 않는 것을 권장
	- 탈취 되었을 때 복구가 매우 어려움 -> MFA 설정을 권장
	- MFA(Multi-factor authentication): 일회용 패스워드를 생성하여 로그인
- IAM 사용자: IAM을 통해 생성해서 사용하는 사용자
	- 한 사람 또는 하나의 애플리케이션을 의미
	- 설정 시 콘솔 로그인 권한 부여 가능
	- 설정 시 AWS 서비스를 이용할 수 있음
		- Access Key
		- Secret Access Key
	- AdminAccess를 부여하더라도 루트 사용자로 별도의 설정을 하지 않으면 Billing 기능을 사용할 수 없음

> IAM 자격 증명 보고서

- 계정의 모든 사용자와 암호, 액세스 키, MFA 장치 등의 증명 상태를 나열하는 보고서를 생성하고 다운로드 가능
- 4시간에 한번씩 생성 가능
- AWS 콘솔, CLI, API에서 생성 요청 및 다운로드 가능
- 포함되는 정보
	- 암호
		- 암호의 활성화 여부
		- 마지막으로 사용된 시간
		- 마지막으로 변경된 시간
		- 언제 변경되어야 하는지
	- 액세스 키
		- 액세스 키 활성화 여부
		- 마지막으로 사용된 시간
		- 마지막으로 변경된 시간
		- 어떤 서비스에 마지막으로 사용되었는지
	- 기타
		- MFA 사용 여부
		- 사용자 생성 시간

> IAM 모범 사용 사례

- 루트 사용자는 사용하지 않음
- 불필요한 사용자는 만들지 않음
- 가능하면 그룹과 정책을 사용
- 최소한의 권한만을 허용하는 습관을 들이기(Principle of least privilege)
- MFA를 활성화 하기
- AccessKey 대신 역할을 활용하기
- IAM 자격 증명 보고서 활용하기