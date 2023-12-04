> Physical 계층은 다루지 않는다.
> Data Link 계층은 Ethernet을 다룬다.
> Network 계층은 Internet을 다룬다.
> Transport 계층은 TCP/UDP를 다룬다.
> Session SSL(TLS)를 다룬다.
> Presentation 계층 -> Pass
> Application은 HTTP를 다룬다.

Physical 계층은 [[NIC]], Data Link 계층은 [[Driver]] Network 계층은 [[IP]], Transport 계층은 [[TCP]], 나머지 Session, Presentation, Application 계층은 어떠한 프로그램이 담당한다.

> 식별자

- 식별자란?
	- 무언가를 식별하는데에 근거가 되는 어떠한 정보

- Physical, Data Link 계층에서의 식별자: Physical Address, 즉 MAC 주소
- Network 계층에서의 식별자: IP(Internet Protocol) 주소 === [[호스트]]
- Transport 계층에서의 식별자: Port 번호