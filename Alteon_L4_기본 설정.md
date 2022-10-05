# Alteon L4 기본 설정

---

### L4 기본 설정 순서

1. L4 IP 설정
2. 게이트웨이 설정
3. admpwd 설정 및 telnet 접속 활성화

---

### 1. interface 설정

가장 먼저 cfg/ip/interface 1 에서 외부에서 L4에 접근할 수 있도록 L4 IP를 설정해준다.

```bash
main$ cfg                                       [configuration 메뉴로 들어가는 명령어]
configration$ ip                            [ip관련 설정을 위한 메뉴로 들어가는 명령어]
ip$ if 1                                [interface 설정 - 256개의 interface 설정 가능]
IP interface$ addr 10.10.1.4                                 [스위치의 interface 주소]
IP interface$ mask 255.255.255.0                                    [주소에 대한 mask]
IP interface$ broad 10.10.1.255                     [주소에 대한 브로드캐스트 address]
IP interface$ en                        [ip와 관련된 설정 후에는 반드시 enable 해야함]
IP interface$ apply/save                            [apply로 설정 활성화(메모리 저장)]
                                          [save로 NVRam에 저장하여 리부팅 후에도 사용]
# 약식 구성방법
main $ cfg/ip/if 1/addr 192.168.1.2/mask 255.255.255.0/broad 192.168.1.255/en
```

### 2. gateway 설정

cfg/ip/gw 1 에서 게이트웨이를 설정해준다.

```bash
main$ cfg
configuration$ ip
ip$ gw 1                              [gateway 를 설정 - gateway는 4개까지 설정 가능]
Default gateway 1$ addr 10.10.1.1                                [gateway 주소 설정]
Default gateway 1$ en                                    
Default gateway 1$ apply/save

# 약식 구성방법
main $ cfg/ip/gw 1/addr 192.168.1.1/en/apply/save
```

### 3. 스위치 기능 설정

cfg/sys/user 에서 admpw를 설정해준다.

초기 L4 패스워드는 admin이다.

(패스워드를 잊어버렸을 경우 password에 `forgetMe!` 를 입력하면 패스워드 변경이 가능하다.)

```bash
# 패스워드 변경
main$ cfg
configuration$ sys                                             [system 메뉴로 들어감]
system$ user                                      [user 메뉴에서 원하는 사용자를 선택]
User AccessControl$ admpw                    [administrator의 password를 변경시 선택]
Current password 입력/new password 입력
User AccessControl$ apply/save

# 약식 구성방법
main $ cfg/sys/user/admpw
```

외부에서 접근이 가능하도록 telnet 접속을 활성화 시켜준다. (cfg/sys)

```bash
# telnet 기능 활성화
main$ cfg
configuration$ sys
system$ tnet en      [telnet 서비스는 os 9.0에서 default disable이므로 활성화 시켜줌]
system$ apply/save
```

초기 상태에서는 vlan 1에 모든 포트가 포함되어 있기 때문에 아래 명령어는 생략해도 된다.

포트를 vlan 2 또는 vlan 3으로 나누어 사용할 때 포트를 나누기 위한 명령어이다.

즉, 아래 명령어는 vlan 1에 포트 1,2 번을 할당하겠다는 명령어이다.

```bash
# vlan 설정
main$ cfg
configuration$ vlan 1                [default로 모든 포트는 valn 1에 할당되어 있다.]
vlan 1$ add 1                         [vlan 1에서 필요한 포트를 빼오는 형식]
vlan 1$ add 2

# interface를 vlan에 할당할때는 ip 설정하는 부분에서 해당하는 vlan을 지정해 주면 된다.
# ex. main $ cfg/ip/if 2/addr 192.168.1.1/vlan 2/en
# ip를 설정하고 뒤에 해당 vlan을 지정한다.
```

### apply, save 저장 관련 명령어
```bash
1. apply 전
diff          - 현재까지 입력된 설정 확인
revert        - 현재까지 입력된 설정 모두 취소

2. apply 후
diff flash    - apply 되었으나 save 되지 않은 설정을 확인
revert apply  - apply 된 설정을 모두 취소

3. save 후
되돌릴 수 없음. 다시 설정해야 한다.
```
---
