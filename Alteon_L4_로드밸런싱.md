### SLB 설정 순서

1. VLAN 생성과 Real IP 설정
2. Real server group 설정 후, real server ip 추가
3. 가상 IP 주소(VIP, 실제 외부에 알려지는 IP)아 서비스 타입 지정
4. 서버와 클라이언트 포트 Enable
5. SLB 활성화
6. 적용/저장

## SLB 설정

---

### Real Server 구성

```bash
# Real Server 구성
main $ cfg
configuration $ slb                                             [slb 메뉴로 들어감]
layer4 $ real 1                                              [real server 1번 지정]
real server 1$ rip 192.168.1.3      [real server의 ip를 설정할때는 rip address이다]
real server 1$ en
real server 1$ ..                                 [마침표 두번으로 상위 메뉴로 이동]

# Real server 약식 구성방법
main $ c/sl/re 1/rip 192.168.1.3/en/../re 2/rip 192.168.1.4/en 방법으로 계속
```

### Group 지정

```bash
# Group 지정
Layer 4$ group 1
Real server group 1$ add 1/add 2/add 3/add 4            [Real server 틀을 추가한다.]
Real server group 1$ health                    [서버와의 health check 하는 기준 선택]
icmp|tcp|http|dns|pop3|smtp|nntp|ftp|imap|radius|sslh  [.. 중 하나를 선택할 수 있다.]

Real server group 1$ metric                                    [로드밸런싱 기법 선택]
leastconns|roundrobin|minmisses|hash|response|bandwidth           [.. 중 하나를 선택]

# Group 지정 약식 구성방법
main $ c/sl/gr 1/add 1/add 2/add 3/add 4/met round/heal http
```

### Virtual Server 구성

```bash
# virtual server 구성
Layer 4$ virt 1             [virtual IP 설정 메뉴. VIP는 256개 까지 사용할 수 있다.]
Virtual Server 1$ vip 192.168.1.10/en            [virtual ip를 설정하고 enable 진행]
Virtual Server 1$ service 80                                      [서비스 포트 지정]
Virtual Server 1 http Service $ group 1                  [서비스를 적용할 그룹 지정]
Virtual Server 1 http Serivce $ .. / ..                           [상위 메뉴로 이동]
Layer 4 $ on                                                     [SLB 전체를 활성화]
Virtual Server 1 http Service $ apply / save                              [저장하기] 
```

### port 구성, 지정

```bash
# Service port 구성하기
main $ cfg
Configuration $ slb
Layer 4 $ port 1
SLB port 1 $ client en       [client traffic 이 들어오는 포트에 client enable를 해준다]
SLB port 1 $ ..
Layer 4 $ port 2
SLB port 2 $ server en                [server가 물려있는 포트에 server enable를 해준다]
SLB port 2 $ apply/save

# User define port 지정하기(ex, 8080, 8001, 12000.....)
user define port를 알테온에서 설정하는 방법은 3가지가 있다.

1. 사용자의 destination port 가 80 이고 실제 service port가 다를경우(ex.8001)

Main $ cfg/slb/vi 1
Virtual Server 1$ se 80                                   [ 사용자의 트래픽은 80이다]
Virtual Server 1 http Service $ rport 8001           [real port 가 8001임을 지정한다]
Current real port:     80               
New pending real port: 8001                           [서비스 포트가 변경됨을 알려줌]
Virtual Server 1 http Service$ gr 1                 [서비스 하게될 리얼서버그룹 지정]
Current real server group:     1
New pending real server group: 1
Virtual Server 1 http Service$ apply              [apply해서 서비스를 활성화시켜준다]

2. 사용자의 destination port 가 실제 service port와 동일한경우
예를 들어 사용자가 http://www.cng.co.kr:8001 이라고 브라우저에 직접 치는 경우

Main $ cfg/slb/vi 1
Virtual Server 1$ se 8001                               [ 사용자의 트래픽은 8001이다]
Virtual Server 1 8001 Service $ gr 1                [서비스 하게될 리얼서버그룹 지정]
Current real server group:     1
New pending real server group: 1
 Virtual Server 1 http Service$ apply             [apply해서 서비스를 활성화시켜준다]
  
3. 사용자의 destination port 가 80 이고 실제 service port가 다른 여러개인 경우
예를 들어 사용자는 실제 서비스 포트를 전혀 모르고 80으로만 쿼리를 보내고
실제 서버의 서비스는 8080, 8001, 12000을 사용한다고할경우, 
알테온은 사용자의 쿼리가 어떤 서비스를 요구하는지 전혀 알수 없다.

따라서 이런 경우 각각의 서비스에 대한 각각의 vip를 설정해야한다.
설정사항은 1번과 같다.
```

### Filter 설정

필터는 cisco router에서 나오는 access-list와 동일한 것으로 보면 된다.
단지 alteon에서는 allow, deny 외에 redirection 이라는 필터링 기법을 지원한다.

redirection은 특정한 트래픽을 강제로 지정한 방향으로 가도록 만들어주는 필터이다.
주로 cache redirection에 이용되며, WCR(web cache redirection), APR(applicationRedirection) 에 쓰는 것이 보통이다.

filter는 1 부터 224 까지 쓸수 있으며 1이 가장 우선순위가 높다.

```bash
# Filter 설정하기
main # cfg
configuration # slb
layer 4 # fi 100                         [filter 번호 지정, 100번부터 지정하는게 보통]

filter 100 # sip 192.168.1.1/smask 255.255.255.255/dip 10.10.10.1/dmask

255.255.255.255/pro tcp/dpo 80/act deny/en

layer 4 # port 1/add 100/fi en     [slb port에 filter를 추가하고 filter enable를 해줌]
layer 4 # apply/save                                              [apply/save는 필수]

source ip가 192.168.1.1인 host가 destination ip 10.10.10.1 
destination 80port 로 올경우 deny 시켜라는 filter.
filter 는 소스 트래픽이 들어오는 포트에 지정.
```

---
