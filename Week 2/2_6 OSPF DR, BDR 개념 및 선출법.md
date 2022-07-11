# OSPF DR, BDR 개념 및 선출법

## OSPF의 반장과 부반장

- **DR (Designated Router) : 반장**
- **BDR (Backup Designated Router) : 부반장**
- OSPF 세그먼트에서는 각 라우터들이 OSPF에 참여하면 DR과 BDR에 자신의 Link State를 알린다.
- 이렇게 저들에게만 주는 것은 트래픽을 줄이고 Link State의 일치성을 제대로 관리하기 위함

- DR은 받은 정보를 다른 라우터들에게 제공하여 업데이트한다.
- BDR은 DR이 업무를 제대로 수행하는지 관찰하고 잘하지 못하면 일을 대신한다.
- DR과 BDR의 선출은 **라우터의 ID와 라우터의 Priority를 가지고 선출**한다.
- Priority의 디폴트 값은 1이다.
- 이쨋든 Priority의 값이 가장 큰 것이 DR이 되고, Priority 값이 가장 큰 것이 두 개라면 둘 중 라우터 ID가 높은 것이 DR이 된다.
- 새로운 라우터가 들어왔는데 Priority값이 가장 크더라도 **이미 설정되어 있으면 DR이 되지 못한다.**
- 전원을 새로 켜면 다시 Priority와 ID값으로 선출한다.
- **라우터가 DR와 BDR에 뽑히지 못하게 하러면 Priority값을 0으로 설정하면 된다.**

### DR, BDR 정리하자면,

- **DR** + Link state를 모두 관리하면서 링크의 상태를 항상 일치시키는 역할
- **BDR** + DR이 잘 동작하는지 감시하다가 다운되면 즉시 대신 동작 시작
- **Adjacency** : OSPF를 이용하는 모든 라우터의 DR, BDR, Link state를 sync 시키는 것
- **DR과 BDR 선출법** + OSPF로 동작하는 라우터들은 모두 priority를 갖는데, defualt = 1이다 + a) priority가 높은 순으로, DR과 BDR 선출 + b) priority가 같으면...?? RID(router ID)가 높은 순으로 선출 + c) DR, BDR 선출이 끝난후, priority가 더 높은 라우터가 참여하면..?? - 이미 DR, BDR 선출이 끝났으므로, 변화없다 - 라우터를 모두 껐다 다시켜서나, OSPF를 재동작으로 인해 재선출이 필요시

priority 순으로 다시 선출         

- DR이 다운되면..??

* BDR  -----> DR 이 됨

* 나머지 라우터중 우선순위가 높은것 ------> BDR로 선출됨

+ d) 특정 라우터를 영원히 DR/ BDR로 선출안되도록 하려면...??         - 그 라우터의 priority를 0으로 세팅

## 루프백(Loopback)인터페이스

루프백 인터페이스는 **시스코 라우터에 존재**하는 특별한 가상의 인터페이스다.

이 인터페이스를 생성하고 IP를 할당해서 이 주소에 ping을 할 수도 있고, 여러 목적(예: OSPF의 Router ID)를 위해 사용할 수 있다.

*** 루프백 생성 명령어**

```c
Router(config)# interface loopback (No.)
Router(config-if)# ip address 10.1.1.1 255.255.255.0
Router(config-if)# ^z

Router# show ip int
```

- No, : 생성하고자 하는 루프백 인터페이스의 번호
