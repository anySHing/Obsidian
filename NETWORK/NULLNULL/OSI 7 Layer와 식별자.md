> L1(Physical)은 다루지 않는다.
> L2(Data Link)는 Ethernet을 다룬다.
> L3(Network)는 Internet을 다룬다.
> L4(Transport)는 TCP/UDP를 다룬다.
> L5(Session)은 SSL(TLS)를 다룬다.
> L6(Presentation)는 -> Pass
> L7(Application)은 HTTP를 다룬다.

L1은 [[NIC]], L2는 [[Driver]] L3는 [[IP]], L4 [[TCP]], 나머지 L5, L6, L7(Application)은 어떠한 프로그램이 담당한다.

> 식별자

- 식별자란?
	- 무언가를 식별하는데에 근거가 되는 어떠한 정보

- L2에서의 식별자: Physical Address, 즉 MAC 주소
- L3에서의 식별자: IP(Internet Protocol) 주소 === [[호스트]]
- L7에서의 식별자: Port 번호

> 포트 번호는 여러 군데에서 쓰인다.
> L2: 인터페이스 식별자 (공유기 같은데 랜 케이블 들어가는 단자 == 인터페이스)
> L3, L4: 서비스 식별자
> L7: 프로세스 식별자