> 


> NAT(Network Address Translation)

- 사설 IP가 공용 IP로 통신할 수 있도록 주소를 변환해주는 방법
- 3가지 종류
	- [Dynamic NAT](##dynamic-nat): 1개의 사설 IP를 가용한 공인 IP로 연결
		- 공인 IP 그룹(NAT Pool)에서 현재 사용 가능한 IP를 가져와서 연결
	- [Static NAT](#static-nat): 하나의 사설 IP를 고정된 하나의 공인 IP로 연결
		- AWS Internet Gateway가 사용하는 방식
	- [PAT(Port Address Translation)](#pat(port-address-translation)): 많은 사설 IP를 하나의 공인 IP로 연결
		- 포트를 기반으로 사설망의 어떤 기기가 통신을 받아야하는지 결정한다
		- NAT Gateway / NAT Instance가 사용하는 방식

## Dynamic NAT

![[스크린샷 2023-12-26 오전 7.51.57.png]]
## Static NAT

![[스크린샷 2023-12-26 오전 7.54.14.png]]

## PAT(Port Address Translation)

![[스크린샷 2023-12-26 오전 7.55.34.png]]

> 사설 IPs

|     이름     | Class | IP address range | IP 개수 | 서브넷 마스크 |
|:------------:|:-----:|:----------------:|:-------:|:-------------:|
| 24-bit block | A     |         10.0.0.0 ~ 10.255.255.255         |     16,777,216    |   (255.0.0.0)            |
| 20-bit block | B     |       172.16.0.0 ~ 172.31.255.255           |  104,857       |    (255.240.0.0)           |
| 16-bit block | C     |   192.168.0.0 ~ 192.168.255.255               |  65,536       | (255.255.0.0)              |

> Classless Inter Doman Routing(CIDR)

- IP는 주소의 영역을 여러 네트워크 영역으로 나누기 위해 IP를 묶는 방식
- 여러 개의 사설망을 구축하기 위해 망을 나누는 방법

> CIDR Block/CIDR Notation

- CIDR Block: IP 주소의 집합
	- 호스트 주소 비트만큼 IP 주소를 보유 가능
- CIDR Notation: CIDR Block을 표시하는 방법
	- 네트워크 주소와 호스트 주소로 구성
	- 각 호스트 주소 숫자만큼의 IP를 가진 네트워크 망 형성 가능
- A.B.C.D/E 형식
	- ex) 10.0.1.0/24, 172.16.0.0/12
	- A.B.C.D: 네트워크 주소 + 호스트 주소 표시 E: 0~32: 네트워크 주소가 몇 bit인지 표시
		- ex) 192.168.2.0/24
			- 네트워크 비트: 24
			- 호스트 주소: 32 - 24 = 8
			- 즉 2^8 = 256개의 IP 주소 보유
			- 192.168.2.0 ~ 192.168.2.255 까지 총 256개 주소를 의미

> 서브넷

- 네트워크 안의 네트워크
- 큰 네트워크를 잘게 쪼갠 단위
- 일정 IP 주소의 범위를 보유
	- 큰 네트워크에 부여된 IP 범위를 조금씩 잘라 작은 단위로 나눈 후 각 서브넷에 할당

![[스크린샷 2023-12-26 오전 8.11.00.png]]