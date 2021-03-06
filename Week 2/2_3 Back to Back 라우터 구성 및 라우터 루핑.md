# Back to Back 라우터 구성 및 라우터 루핑

## Back to Back 라우터 구성

back to back 구성은 라우터와 라우터를 새로 직접 연결하면서 마치 전용선 구간에서 연결한 것처럼 만드는 기술이다.

원래 전용선을 연결하기 위해서는 라우터 뒤에 모뎀 장비인 DSU/CSU 가 붙고 그 뒤에 전용선을 연결한다.

하지만, Back to Back 구성에서는 라우터 대 라우터를 V.35 케이블 만을 가지고 서로 연결한다. 

**즉, DSU/CSU 를 생략하는것이다.**

### 실제 구성

![Untitled](https://user-images.githubusercontent.com/84123877/177039003-b3d0272b-a6d1-4ff2-a159-f2cb075111f0.png)

> Back to Back 구성시 라우터 둘 중 하나는 DCE로 동작을 해야한다.
> 

DTE : 사용자 - 네트워크 인터페이스의 사용자측에서 데이터 발신 장치나 수신 장치, 또는 두 가지 겸용으로 사용되는 장치

DTE에는 컴퓨터, 멀티플렉서, 라우터 등이 포함된다.

DCE : 사용자 - 네트워크 인터페이스의 네트워크 측으로 구성되는 통신 네트워크 장비의 연결 수단

DCE에는 모뎀과 인터페이스 카드가 포함된다.

<aside>
💡 **중요한 것은 한 쪽의 케이블은 DTE 다른 한 쪽은 DCE로 동작해야 한다.**

</aside>

---

## 라우터 루핑

디스턴스 벡터 알고리즘의 문제점 중 하나로, 

디스턴스 벡터 알고리즘은 일정시간마다 업데이트(RIP경우 30초)를 한다.

- A, B, C 3개의 라우터가 있을 때 A의 왼쪽에 있는 네트워크가 다운되었다. 그럼 A는 자신의 테이블에 다운되었다고 저장하게 된다.
- 하지만 아직 업데이트 시간이 되지 않아 전달하지 않고 기다린다.
- 그런데 B의 업데이트가 시작된다.
- B는 아직 A의 왼편이 다운되었는지 모르므로 A의 왼편 정보를 A에 전달한다. (자신에 있어서는 홉 카운트 1로)
- A는 메세지를 받고 B를 통해 자신의 왼쪽 네트워크에 갈 수 있는줄 알고 저장한다 (홉 카운트 2로)
- 그렇게 되면 A의 업데이트 시간이 되어 B에 그 정보를 전달하고 B 홉카운트는 3으로 또 다시 C는 4로 바꾼다. 이렇게 루핑이 발생한다.

### 라우터 루핑 해결방법

1. **Maximum Hop Count**
    - **최대 홉 카운트롤 15로 정하고** 15를 넘어가는 라우팅 경로에 대해 unreachable로 설정
    - *flush time 이 지나면 테이블에서 아예 삭제해 버린다.
    - **네트워크의 규모가 커질 경우에는 치명적인 약점**을 드러낸다.
    
2. **Hold down Timer**
    - 어떤 경로가 죽었다고 판단하면 이 경로에 대한 상태에 대해서 업데이트가 들어와도 바꾸지 않는다.
    - 일정 시간이 지난 다음에 바꿀 수 있다.
    - **즉 한 쪽이 죽었는데 다른 쪽에서 자신이 가지고 있던 죽은 쪽 홉카운트보다 큰 값이 들어오면 무시한다.**
    
3. **Split Horizon(스플릿 호라이즌)**
    - 라우팅 정보를 수신 시 자신보다 *메트릭이 작은 네트워크의 정보를 보내준 라우터에는 정보 송신시에 해당 네트워크에  대한 정보를 보내지 않는다는 것.
    - 그 네트워크에 대한 정보는 자신에게 보내 준 그 라우터가 더 인접하다고 인식하기 때문에 자신의 데이터를 송신하지는 않는다. (해당 네트워크에 대한 정보만)
    - **Split Horizon은 두 라우터 간의 루핑 방지 기술로, 전체 라우터의 루핑을 막는 것은 어렵다.**
    
4. **Route Poisoning(라우트 포이즈닝)**
    - 네트워크가 다운되면 라우터가 네트워크에 대한 값을 16 즉, **사용할 수 없는 값으로 만든다.**
    - 다른 라우터에서 정보가 들어와도 무시한 채 네트워크의 값을 16으로 해 다시 보낸다.
    - 다른 라우터들도 그 네트워크에 대한 값을 16으로 알게 된다.
    - **완전히 지워버리는것이 아니라 잘못된 라우팅 정보를 받는 일을 예방 할 수 있다.**
    
    (매트릭 값을 말함. 홉 카운트)
    
5. **Poison Reverse(포이즌 리버스)**
    - 포이즌 리버스 업데이트를 사용한 스플릿 호라이즌이라고 불린다.
    - 스플릿 호라이즌처럼 라우팅 정보를 보내온 쪽으로 알려주지 않는 것이 아니라 라우팅 정보를 되돌려 보내기는 하되 이 값을 무한대 값으로 쓰는 방식
    - 즉, 무한대로 쓸 수 없는 값이므로 라우팅 업데이트가 들어와도 무시한다.

- ***Flush Timer (240초)**
    
    RIP상에서 사용되는 timer를 이해하는데 혼동을 주는 timer이다. 위에서 언급한 다른 3개의 timer들은 순차적으로 발생되지만, 이 flush timer는 독립적으로 운영된다. 한마디로, flush timer는 routing update를 받지 못하면, 바로 counter되며, routing update를 다시 받게 되면, reset되는 timer이다. 총 240초 이며, 이 기간 내에 routing update가 발생하지 않으면, routing은 폐기된다. 
    

- *metric(메트릭)
    
    라우터가 목적지에 이르는 여러 경로(루트) 중 최적의 경로를 결정하는데 서열 기준을 매길 수 있게끔 숫자로 환산하는 변수. RIP에서는 지나가는 라우터의 개수 =홉 카운트
 
 
 **라우팅 메트릭의 자세한 개념**
# 라우팅 메트릭

```
1.라우팅 메트릭 (Metric)

  ㅇ라우터가 목적지에 이르는 여러 경로(루트) 중 최적의 경로를 결정하는데,
     - 서열 기준을 매길 수 있게끔 숫자로 환산하는변수 (최적 경로 선택 기준 값)

2.라우팅 메트릭 값 의미

  ㅇ 특정 경로 상의 총 메트릭
     - 경로 상의네트워크 또는링크들을 모두 거쳐 지나가는데 할당되는비용
        . 그 경로에 속하는 각네트워크 또는링크들의 메트릭의 합

  ㅇ 결국,라우터는 가장 적은 메트릭 값을 갖는 경로를 찾게됨

3. 일반적으로 사용되는 메트릭변수들

  ㅇ Path Length  :  경로 거리 (Hop Count 등)
  ㅇ Reliability  :신뢰성 유지 (링크고장의 수,전송에러의 수 등)
  ㅇDelay        :  걸리는시간 (msec 등)
  ㅇBandwidth    :  사용가능한 용량 (전송선로 용량으로써의 MHz 등)
  ㅇLoad         :  가용자원에 대한부하 (트래픽 등)
  ㅇ Cost         :링크 특성 또는 관리자의정책적인 고려 등에 의해 값을 매김

4.라우팅 프로토콜 별로 각기 고유한 메트릭 값을 갖음

  ㅇRIP         :Hop Count (홉 카운트)

  ㅇOSPF        :Link Cost (링크 비용)
     -링크대역폭,전송속도에 기초하여 계산되고, 16비트 Metric 값(1~65,535)으로 표현

  ㅇIGRP,EIGRP :  여러가지를 조합시킴
     -Bandwidth,Delay,Load, Reliability,MTU 등 여럿을 조합하여 계산한 Cost가 가장 낮은 경로

  ㅇBGP         :속성(Attribute)이라고 하는네트워크 도달성(Reachability)정보
     -BGP Path Attribute : Next_Hop,Weight, Local Preference, AS_path, Origin type,
MED 등

5. 메트릭 값에 따른라우터 행동 양식

  ㅇ 두 경로가 같은 메트릭 값이면 =>로드밸런싱

  ㅇ 두 경로가 다른 메트릭 값이면 => 최선 경로(메트릭 값이 가장 작은 값)로라우팅하다가,
고장나면 차선 경로로 대체
```

---

# 관련 명령어들

[관련 명령어들](https://www.notion.so/699ace7b1fe642e7b66e77652b89d013)
