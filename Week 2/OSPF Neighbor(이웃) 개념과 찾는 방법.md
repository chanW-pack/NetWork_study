# OSPF Neighbor(이웃) 개념과 찾는 방법

## Neighbor 란?

**주위에 있는 OSPF 라우터들**을 의미하며, *Hello 패킷을 멀티캐스팅해서 이웃을 찾은 후, 라우터들은 자신의 DB에 이웃을 저장한다.

즉, 주위에 있는 OSPF 라우터를 찾아 자신의 DB에 저장하여 주위 OSPF 라우터와 이웃관계가 되고,

**이웃 관계가 되어야 통신이 가능하다.**

*hello 패킷 : 통상, 두 라우터 간에 인접관계를 설정하고 유지하는 기능을 수행 

(근처 노드 간의 이웃(Neighbor)관계의 성립 및 유지)

### neighbor를 찾는 과정

1. Down State
    - 다운되어 있던 라우터가 켜지는 상태
    
2. init State
    - 헬로 메세지를 멀티캐스팅한다.
    - 헬로를 받은 라우터들은 자신들의 이웃 목록에 해당 라우터 추가
    
    *  **멀티캐스트를 이용**하므로, 네트워크 내에서 **OSPF로 동작하는 라우터만** 메세지를 수신한다.
    

1. Two-way state
    - 헬로 메세지를 받은 라우터들이 **유니캐스트를 이용**해서, **자신의 정보 전달**
    

### Hello Packet 구성

- Router ID
- Hello/dead intervals (중요)
- Neighbors
- Area-ID (중요)
- DR IP address
- BDR IP address
- Authenitcation password (중요)

1. OSPF로 통신하는 라우터들간에 다음 항목의 값이 모두 같아야만 이웃이 맺어질 수 있다.
    - Hello/dead intervals
    - Area-ID
    - Authenication password
    - Stub area flag
    
2. hello 패킷은 10초에 한번씩 발생한다.

1. **라우터 ID (RID)는 라우터의 IP중 가장 높은 주소를 사용**한다. 인터페이스들이 죽었다 살아나는것을 반복한다면 RID가 자꾸 바뀔 가능성이 있다.

1. RID가 변경될 때마다 라우터 정보도 끊임없이 바뀌어야 하므로, 보통 *루프백(loopback)인터페이스 주소를 RID로 지정해서 사용한다.

*루프백 인터페이스 : [https://www.notion.so/OSPF-DR-BDR-ddbaabdf66c443d5b136ad4fb019d646](https://www.notion.so/OSPF-DR-BDR-ddbaabdf66c443d5b136ad4fb019d646)

## 주의 :

<aside>
⛔ 만약, 이미 RID가 있는 상태에서 loopback interface를 만들면 바로 RID가 적용되지 않고, 라우터를 재부팅 또는 OSPF 프로토콜을 내렸다가 다시 올려야한다.

</aside>