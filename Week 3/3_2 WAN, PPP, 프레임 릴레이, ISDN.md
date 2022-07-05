# WAN, PPP, 프레임 릴레이, ISDN

## WAN, Wide Area Network

광역 통신망(WAN)은 드넓은 지리적 거리/ 장소를 넘나드는 통신 네트워크 또는 컴퓨터 네트워크이다. 광역 통신망은 종종 전용선과 함께 구성된다.

대표적인 예 ) 유선 전화기, 팩스, 모뎀 등

일반적으로 중간에 통신회사를 이용해서 광대역, 네트워킹 구축을 한다.

### 종류 (3가지 방식)

![Untitled](https://user-images.githubusercontent.com/84123877/177287027-99c351a3-b285-42e3-a1c1-79f889995462.png)

1. **Leased Line, 전용선 방식**
    - 통신 사업자(예, 전화국)에게 통신회선을 임대 받아 사용
    - 만약 서울 - 부산간 wan 구축을 원할 경우, 통신사업자가 설치해 놓은 회선 중 하나를 임대료를 내고 사용
    - 혼자만 쓰는 전용선이므로, 보안에 강하나 비용이 많이 든다.
    - ex_ HDLC, PPP, SLIP
    
2. **Ciruit Swiched, 회선 스위칭 방식**
    - 통신을 하는 순간에만 필요한 회선을 열어주고, 통신이 끝나면 회수하는 방식
    - ex) 전화, 모뎀, ISDN (통화 순간에만 회선을 사용, 종료 후 회선 반환)
    - PPP, SLIP, HDLC
    
3. **Packet Switched. 패킷 스위칭 방식**
    - 패킷 단위로 나뉘어서 통신회선을 타고 목적지까지 전달되는 방식 (즉 통신회선을 다른 사람과 나누어 씀)
    - 실제 자신이 가진 회선은 없지만, 목적지까지 회선을 가진 것처럼 데이터를 목적지까지 전달되도록 동작 = Virtual Cricuit의 개념
    - ex) 프레임 릴레이, ATM, X.25

---

## 참고, CPE ,Demarc, DTE, DCE

### CPE, Customer Premises Equipment

- WAN 쪽 서비스에서 통신사업자와 고객장비를 구분하기 위한 용어로,

고객이 가진 장비, 즉 고객 사이트에 있는 장비를 의미한다.

- ex) 라우터, DSU,CSU(모뎀)

### Demarc, Demarcation (경계)

- WAN에서 문제 발생시, 서비스 제공자(통신 사업자)와 고객 사이의 책임 경계를 구분하기 위해 만들어진 용어
- 고객 책임 : DSU/CSU 문제
- 통신사 책임 : 회선 문제
- 실무에서는 WAN 관련 문제들이 많이 발생하므로, 회선 관련 지식과 경험이 많아야 유리

### DTE, Data Terminal Equipment

- WAN으로 통신을 시작하기 위한 터미널 역할
- ex_ 라우터

### DCE, Data Communication Equipment / Data Circuit-terminationg Equipment

- 사용자와 네트워크로 구분 했을때, 네트워크측으로부터 연결 역할을 담당하는 연결 통로 제공
- ex_ DSU/CSU 장비

---

## HDLC 와 PPP

### HDLC

컴퓨터 데이터 통신에 적합한 전송 제어 방식이다.

라우터 시리얼(serial) 라인에 주로 사용하는 HDLC(High-Level Data Link Control)

(시스코의 라우터는 시스코만의 HDLC 방식을 사용하므로 표준 HDLC가 아니다.)

표준 HDLC는 지원하는 네트워크 프로토콜이 하나뿐이지만, 시스코 HDLC는 여러 개의 네트워크 프로토콜을 지원한다. 하지만 표준이 아니기 때문에 시스코 장비들끼리만 사용해야한다.

<aside>
💡 시스코 장비와 다른 장비를 연결 할 때는 PPP를 사용한다.

</aside>

시리얼 인터페이스는 Default로 encapsulation HDLC 로 되어 있다. 

하지만 모뎀 라인과 같은 Async 라인에서는 Enable 해줘야 한다.

```
(config) # 인터페이스 or 모뎀
(config-if) # encapsulation ?
**(config-if) # encapsulation hdlc**

show interface serial 0/0 -> 확인

----

Encapsulation HDLC

----
```

### PPP

PPP(Point-to-Point) 프로토콜은 WAN에서 가장 일반적이느 그리고 가장 괜찮은 인캡슐레이션 방식이다.

타사 장비와 함께 사용하러면 반드시 PPP를 사용해야 한다.

PPP는 강력한 보안과 여러가지 네트워크 계층 프로토콜을 한꺼번에 지원하는 장점을 가진 표준 프로토콜이다.

PPP안에는 NCP와 LCP가 있다.

- NCP, Network control protocol :
    - 모든 데이터 계층의 프로토콜을 지원한다. (IP, IPX, Appletalk 등의 멀티 프로토콜 지원 담당)
    - 서로 다른 Layer 3계층 프로토콜 유형을 캡슐화 하는 기능을 지원한다.
    
- LCP, Link Control protocol :
    - 데이터 링크 연결을 확립하고, 수립, 유지, 종료를 담당한다.
    - 링크에 루프가 발생하면 이를 탐지하고 인터페이스를 shutdown시켜 다른 정상적인 경로를 통해 패킷을 이동시킨다.
    - 오류가 발생하면 인터페이스를 shutdown시켜 더 나은 경로로 패킷을 이동시킨다.
    - 라우터와 라우터 간에 여러 개의 링크로 구성된 경우 트래픽을 로드 분산시킴. (로드 밸런싱)

이런 LCP의 인증방법에는 PAP와 CHAP가 있다.

### PPP의 세션 구축 단계

1. 데이터 링크 계층의 세션 구축 (2계층)
2. 보안 인증 단계 (옵션 단계) → 라우터끼리의 인증
3. 네트워크 계층 세션 구축 (3계층)

PPP는 1대1 로 접속하는 방식.

### PPP의 보안 인증 (PAP, CHAP)

![Untitled 1](https://user-images.githubusercontent.com/84123877/177287016-ddc348fb-5b08-4139-bc14-43caf0d43df6.png)

PAP (PPP Authentication Protocol 또는 Password ... ) :

1. A → B로 hostname과 password 를 보낸다.
2. B는 저장되어 있는 값과 비교 후 접속 허가 또는 접속 불허

가장 일반적인 패스워드 확인법으로, 접속하고자 하는 라우터에 자신의 hostname, password를 전송하고, 이를 인증하는 방식이다.

단점으로 호스트 이름과 암호가 암호화되지 않고 (Clear Text, 평문 전송) 전송되므로, 

네트워크 통신 도중 해킹될 가능성이 존재한다.

<aside>
💡 2 Way Handshake 라고도 부른다. (접속요청, 접속 허가/불허 패킷으로 연결이 구성되므로..) 그리고 두 라우터에 비밀번호는 통일되어야 한다.

</aside>

### CHAP (Challenge Handshake Authentication Protocol)

![Untitled 2](https://user-images.githubusercontent.com/84123877/177287023-e7014229-8dea-4d7f-bb73-c6b75aae8116.png)

CHAP은 보안이 강화된 만큼 인증과정도 늘었다.

3 Way Handshake : 1. Challenge  2. Reponse  3. Accept/Reject

- Challenge : 다음에 답을 보낼때 이용해야할 코드값 전송
- Response : 이 challenge 값을 이용해 자신의 패스워드 암호화(Hashing)
- Accept/Reject : Hashing된 암호를 자신이 hashing 한 값과 비교해서 같으면 허용, 다르면 거부

password를 해싱해서 전송하므로, 중간에서 해킹할 수 없다.

(hashing : 복구화 불가)

---

## 프레임 릴레이 (Frame-Relay) 구성

프레임 릴레이란 : 하나의 물리적인 회선에 여러 논리적인 회선인 가상 회선을 만들어 마치 전용선처럼 취급하는 서비스이다.

**프레임 릴레이** (**Frame Relay**)는 LAN간 또는 광역통신망(WAN)내 단말 지점 간의 비용-효율적인 데이터의 전송을 위해 고안되었다. 고속 통신기술중의 하나로, 기존 X.25의 패킷전송기술을 고속 데이터 통신에 적합하도록 개선한 통신기술이다.

패킷통신의 효율성과 전용회선이 지닌 고속전송의 특성을 결합한 것으로, 광대역 종합정보통신망인 ATM의 전단계이다.

### 즉, 기존에 WAN에서 사용하던 통신 방식인 X.25를 개선한것으로,

에러 복구 기능 및 흐름 제어 등의 데이터 처리 과정을 생략함으로써 효울적인 데이터 전송 방법을 제공하는것.

X.25에 비해 빠르고 효과적이지만 에러 제어 기법은 거의 제공이 안된다고 보면 된다.

Ethernet에서는 IP와 MAC Address를 쓰는 반면, Frame-relay는 DLCI와 Next Hop IP 라는 것을 사용한다.

![Untitled 3](https://user-images.githubusercontent.com/84123877/177287025-86aaa212-ca13-4436-81bd-792a54970f5c.png)

<aside>
💡 인캡슐레이션 방식에는 Cisco 방식, IEFT 방식(타사 라우터와 혼용시 사용) 두 가지가 있다.

</aside>

### DLCL (Data Link Connection Identifier) : 프레임 릴레이 연결을 위한 주소

Frame-relay에서 2계층 주소, 0~1023까지 할당 가능 

(즉, 라우터와 프레임 스위치 사이의 논리적인 회선을 식별하는 번호이다.)

### LMI (Local Management Interface) :

프레임 릴레이 스위치와 라우터 사이에서 PCV의 상태 정보를 교화(알림)하기 위해 사용되는 표준 프로토콜이며, 매 10초마다 채널 상태 정보 갱신을 위해 네트워크에 폴링을 실시하고, 만약 응답이 없을 경우 장치 연결이 끊어진것으로 간주한다.

- 프레임 릴레이 구성 시 Encapsulation 타입과 LMI만 잡아주면 Inverse ARP 가 동작해서 자동으로 연결 가능하도록 해주지만 보통 [frame-relay interface-dlci] 또는 [frame-relay map] 명령어를 이용해서 수동으로 설정한다.

### inverse APR 보다 수동 설정이 권장되는 이유

- 맨 처음 프레임 릴레이를 구성하면 inverse ARP로 인해 잘 연결되지만, 다른 라우터를 프레임 릴레이 망에 붙이기 위해 frame-relay map을 사용해서 재부팅되면, frame-relay map 명령어를 사용했기에 inverse ARP 가 동작이 안되어 연결이 되지 않는다.
- 즉, frame-relay map 명령어와 inverse ARP 는 함께 동작하지 않으므로 반드시 둘 중 하나만 사용한다.

### 프레임 릴레이의 혼잡 제어, Congestion Control

1. **FECN (Forward Explicit Congestion Notification)**
    - 프레임릴레이 망에 의해 설정
    - 해당 프레임이 목적지까지의 경로에서 혼잡상황을 겪었음을 수신 DTE에게 알리기 위해 사용
    - **즉, 프레임 릴레이 네트워크에서 트래픽 혼잡 현상이 발생했을 경우 이 상황을 데이터를 수집하는 라우터에 알리는 통지이다.**

1. **BECN (Backwrod Explicit Congestion Notification)**
    - 프레임릴레이 망에 의해 혼잡경로를 경험한 프레임의 전달 역방향으로 전송되는 프레임에 설정
    - BECN 필드의 목적 : FECN 또는 BECN 표시를 통하여 흐름제어 능력을 가진 상위 프로토콜이 적절한 행동을 취할 수 있도록 하기 위함
    - **즉, 프레임 릴레이 네트워크에서 트래픽 혼잡 현상이 발생했을 경우 송신 라우터의 전송률을 조정하기 위한 통지이다.**
    
2. **DE (Discared Eligibility)**
    - 폐기할 프레임을 결정할 수 있도록 하는 기준을 제공
    - **즉, 프레임 릴레이에서 트래픽 혼잡 시 가장 우선적으로 버려지는 프레임에 설정된 비트이다.**

---

## ISDN (Integrated Services Digital Network,)

ISDN이란 종합 정보 통신망으로, 음성, 문자, 화상 등의 다양한 통신 서비스를 하나의 디지털 통신망을 근간으로 종합적으로제공할 수 있도록 통합한 것이다.

국내와 같이 DSL 기술이나 메트로 이더넷처럼 초고속 전송 기술이 잘 정착된 나라가 많이 않기 때문에 외국에서느 ISDN이 많이 사용되고 있다고 한다.

<글쓴이 왈 몇년 안에 사라질것으로 예상되는 기술이라고 한다... 알아두기만 하자>

회선 종류로는 BRI, PRI 이 있다.

### BRI, Basic Rate Interface

- 2B + 1D = (2 x 64 kbps _ 16 kbps) =
- 데이터 대역폭 (128) + 16 = 144 kbps

### PRI, Primary Rate Interface

- 종류 : E1 PRI, T1 PRI
- T1 PRI = 23B + 1D = 1,544 Mbps , 미국, 일본 등에서 사용
- E1 PRI = 30B + 1D = 2,048 Mbps, 유럽, 한국 에서 주로 사용

---
