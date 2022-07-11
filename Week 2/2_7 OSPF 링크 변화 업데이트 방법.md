# OSPF 링크 변화 업데이트 방법

처음 라우터가 켜지거나, 새로 **OSPF 라우팅**으로 구성되면 

그 라우터는 멀티캐스트 주소(224.0.0.5)를 이용해 헬로 패킷을 전송한다.

- 헬로 패킷을 이용해 DR, BDR 주소 파악
- 새 라우터는 자신이 가진 링크 정보를 LSA(Link-State Advertisement)에 담아

모든 DR, BDR에 멀티캐스팅(**224.0.0.5 사용**),  *LSU(Linkstate update)라 부른다.

*LSU 

- 멀티캐스팅을 이용해서 모든 DR, BDR에 전송
- DR에 LSA가 도착하면 BDR은 타이머를 세팅하고ㅡ DR이 새 LSA를 다른 OSPF 라우터들에게 재전송하는지 기다림
- DR은 이 새로받은 LSA 정보를 멀티 캐스팅(224.0.0.5)을 이용해서

모든 OSPF 라우터들에게 전송하고, ack를 받아서, 정보가 잘 도착했는지 확인

- **DR이 BDR의 타이머가 끝날 때까지 LSA 정보를 다른 OSPF 라우터에게 전송하지 않으면**

**BDR은 DR이 되고, 헬로 패킷을 이용해 BDR 재선출**

- 만약 링크가 끊어지면, 해당 라우터는 그 정보를 즉시 DR에 알리고 DR은

그 LSA 정보를 가까운 네트워크로 flooding해서 네트워크에 대한 정보 업데이트.

## 명령어

**명령어**

**OSPF enable**

Router(config)# **router ospf (process-ID)*** process-ID : OSPF를 여러개 돌릴 때 그 프로세스를 구별하기 위한 ID로 사용

- 참고: 한개의 라우터에서 ospf의 process-ID를 여러개 운용하는 것은 바람직하지 않음.

왜냐하면, process-ID당 하나의 DB를 운용하므로 성능에 문제

**OSPF로 운영할 네트워크를 정의**

Router(config-router)# **network address (wildcard-mask) area (area-id)**

- wildcard-mask : 서브넷 마스크 중 1 -> 0, 0 -> 1로 바꾸어서 정의.예) subnetmask : 255.255.0.0 -> wildcard-mask : 0.0.255.255
- areaOSPF에서 보다 확장성 있는 라우팅 업데이트를 위해서 사용하는 개념,전체 OSPF 영역을 보다 작은 area 단위로 나눠서 그 영역 안의 OSPF 라우터들끼리만

우선 링크 정보를 업데이트- 다른 area와의 통신은 ABR(area border router, area 사이에 있는 라우터들)이 담당- 백본 area : area 0- area를 구성할 때는 백본 area를 중심으로 다른 area를 구성해 주는 것이 일반적

**OSPF의 토폴로지 유형 확인**

Router# **show ip ospf interface**

- 결과화면의 세번째 줄에 Network Type으로 표시됨
- cost : OSPF의 링크 속도에 따라 부여하는 값 100,000,000 / 대역폭(bps) = 100,000,000 / 1,544,000 = 64Dead : hello 패킷에 응답이 없을때 라우터가 죽었다고 생각하는 주기

**OSPF로 연결된 neighbor 확인**

Router# **show ip ospf neighbor**

- 이웃 라우터가 표시State가 FULL - 이웃을 서로 인식했고, 서로간에 라우팅 테이블의 교환이

성공적으로 이루어져 현재 통신을 제대로 수행하고 있다는 의미

**OSPF 구성 연습**

**라우터 A ]    fa 0/0 : 172.16.10.1/24 ,       s0 : 192.168.12.1/28**

**라우터 B ]        s0 : 192.168.23.2/28 ,    s1 : 192.168.12.2/28**

**라우터 C ]       fa 0 : 172.16.30.1/24 ,       s0 : 192.168.23.3/28**

**(Condition)**

- **Router A ***

interface FastEthernet 0/0  ip address 172.16.10.1  255.255.255.0!interface Serial 1/0  ip address 192.168.12.1  255.255.255.240!router ospf  100network 172.16.10.0  0.0.0.255  area  0network 192.168.12.0  0.0.0.15  area  0

- **Router B ***

interface Serial 0  ip address 192.168.23.2  255.255.255.240!interface Serial 1  ip address 192.168.12.2  255.255.255.240!router ospf  100network 192.168.12.0  0.0.0.15  area  0network 192.168.23.0  0.0.0.15  area  0

- **Router C ***

interface FastEthernet 0  ip address 172.16.30.1  255.255.255.0!interface Serial 0  ip address 192.168.23.3  255.255.255.240!router ospf  100network 172.16.30.0  0.0.0.255  area  0network 192.168.23.0  0.0.0.15  area  0

**[Answer]**

- 라우터 A, B, C 공통
- 각 라우터의 인터페이스에 주어진 IP 할당OSPF enable 후, 해당 네트워크 할당
- 라우터 B
- DCE 장비이므로 해당 인터페이스에서 추가적으로 clock rate 설정 필요(난 56000으로 설정해서 테스트 함)
- 확인
- ping test : A의 이더넷 -> C 의 이더넷show ip routeshow ip ospf interfaceshow ip ospf neighbor
