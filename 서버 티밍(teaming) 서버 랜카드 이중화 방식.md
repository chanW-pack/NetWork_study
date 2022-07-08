# 서버 티밍(teaming) & 본딩 (bonding) - 서버 랜카드 이중화 방식

---

금일 server NIC 증설 작업이 있어, 작업 이후 본딩 및 티밍 작업이 있었는데 작업 경험을 살려 복습해보려 한다.

티밍 (Teaming) : Windows 서버 LAN카드 이중화 기술

본딩 (Bonding) : Linux 서버 LAN카드 이중화 기술

우선 Windows 환경에서 티밍에 대해 구성해보겠다.

## 서버 티밍 (teaming)

---

티밍은 네트워크 포트 2개를 한개처럼 묶어서 대역폭을 늘려주고 이중화로 안정성까지 더해주는 역할을 해주면서 Linux에서의 Bonding과 같은 역할을 한다.

쉽게 말하면 Windows 서버 랜카드를 이중화하는 기술로, 여러개의 서버 LAN 카드를 하나로 묶는 구성이다.

<aside>
💡 네트워크의 LACP 기술과 비슷하게 생각하면 된다.

</aside>

![Untitled](https://user-images.githubusercontent.com/84123877/177929514-45243234-e8c0-47d0-a126-95a396dd7484.png)

> 위 사진처럼 LAN 카드 2개를 장착 후 두 케이블을 연결한다.
> 

서버는 Active로 통신을 하다가, Active 케이블(혹은 LAN카드 1) 이 장애가 발생하면 Standby 쪽으로 통신을 변경하여 진행하게 된다.

한쪽에 문제가 생겨도 다른 한쪽으로 통신이 계속해서 이어지니 통신단절의 위험이 줄어든다.

이걸 Windows 서버에서 티밍(Teaming) LAN 이중화라고 부른다.

<aside>
💡 경우에 따라 서버 LAN 카드에서 Active-Active / Active-Standby 구성이 가능하다.
(본인도 액티브-액티브 구성으로 진행했다.)

</aside>

## 티밍 구성 방법

---

1. 서버관리자 → 로컬 서버 → NIC 팀 사용 안 함

![Untitled 1](https://user-images.githubusercontent.com/84123877/177929501-4eccdcd4-ff33-49d1-ba43-d200b578e2be.png)

1. 인터페이스에서 NIC 팀에 추가할 네트워크 어댑터를 선택하고 클릭 작업을 한 다음에 새 팀 추가를 선택한다.

![Untitled 2](https://user-images.githubusercontent.com/84123877/177929507-fb6b3941-d6bc-4a2c-997b-e9cd47542d78.png)

1. 대화 상자가 열리고 네트워크 어댑터와 팀 멤버가 표시된다. 팀 이름에 NIC 팀의 이름을 입력하고, 필요한 경우 추가 속성팀 구성 모드, 부하 분산 모드 및 대기 어댑터에 대한 값을 선택한다.

![Untitled 3](https://user-images.githubusercontent.com/84123877/177929509-01946c94-3675-4b90-986b-54bf7c79a529.png)

> 대부분의 경우 가장 성능이 뛰어난 로드 균형 모드는 동적이다.
> 

1. 확인을 선택하면 이더넷의 상태가 활성으로 변한다. 네트워크 속성에서 Team에서 아이피 주소 및 서브넷 마스크, 게이트웨이 등을 설정해 주면 된다.

![Untitled 4](https://user-images.githubusercontent.com/84123877/177929510-34c5a316-8189-4d93-8170-9d82ef32999d.png)

![Untitled 5](https://user-images.githubusercontent.com/84123877/177929513-1476dcd2-89d0-4568-9c35-1e5d75918964.png)

> 마지막으로 세팅이 완료되면 해당 사진처럼 2개의 네트워크가 teaming이라는 하나의 연결로 나타나게 된다.
> 

---
