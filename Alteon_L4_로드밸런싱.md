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

### 추가 작성 예정 0824
