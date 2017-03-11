---
title: linux 정리
date: 2017-02-24 17:56:32
tags: linux
categories: linux
thumbnail: /images/centos_logo.jpg
---
# Linux System Administration

## 실습 환경
- CentOS 5.4
- kernel 2.6.18
- vmware player
- \*.100 : FTP, DNS
- \*.200 : Web

## VMWare deault Network
- host PC : 192.168.xxx.1
- Router : 192.168.xxx.2
- DNS : 192.168.xxx.2
- DHCP : 192.168.xxx.254

## Remote Login
- server : Telnet#23, SSH#22
- Client : Secure-CRT, Tera-Term, Netterm, Putty....
- client 설정 : /etc/ssh/ssh_config
- server 설정 : /etc/ssh/sshd_config
- ssh / sshd

## SSH의 보안강화 자동로그인
- Server  

```
# ll .ssh/
-rw-r--r-- 1 root root 397  2월 23 17:15 known_hosts
```

- Client

```
# ssh-keygen  -t dsa   <-- key pair 생성
# ll .ssh
  -rw-------. 1 root root 736 2017-02-23 17:12 id_dsa       <-- private key
  -rw-r--r--. 1 root root 616 2017-02-23 17:12 id_dsa.pub   <-- public key
# scp  ~/.ssh/id_dsa.pub  {server_ip}:~/.ssh/authorized_keys
# eval $(ssh-agent)
# ssh-add

# vi .bash_profile
  eval $(ssh-agent)
  ssh-add
# vi .bash_logout
  kill $SSH_AGENT_PID
```

## kernel 확인
```
# ls /boot
vmlinuz-2.6.18-164.el5
# uname -r
2.6.18-164.el5xen
```

## Shell 확인
```
# ps
  PID TTY          TIME CMD
 3953 pts/2    00:00:00 bash

# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

## Shell variable
- 지역변수 : name=KIM
- 환경변수 : export name[=KIM]   : 자식 프로세스에게 복사되어 전달 됨
- variable substitution : $   
  `ex) echo $PATH`

## command substitution
- $()
- day=\`date +%d\`  <--- bourne sh
- month=$(date +%m) <--- ksh

## 연산 substitution
- (( 연산 ))
```
# x=100
# y=200
# (( sum=x+y ))
# echo $sum
300
```

## Shell의 File Generation 특수 문자
- \* : zero를 포함한 more character
- ? : any single character
- [...] : [...]에 나열된 single character

## PWD
- cd -     <-- 이전 디렉토리로 이동
- set | grep PWD
```
OLDPWD=/root
PWD=/etc/sysconfig/network-scripts
```

## Prompt  
```
# set | grep ^PS
PS1='[\u@\h \W]\$ '
PS2='> '
PS4='+ '
```

## Shell 이 로그인시 실행하는 일련의 스크립트
```
/etc/profile
~/.bash_profile : User specific environment and startup programs
~/.bashrc       : User specific aliases and functions
/etc/bashrc
```

## Shell의 숨은 파일
- /etc/skel 에서 계성 생성시 복사 됨 (bash_history 는 로그아웃 될때 생성)
- .bash_history : exit 할 때 메모리에 가지고 있는 history 정보 저장
- .bash_profile
- .bashrc
- .bash_logout : logout 시 실행하는 스크립트

## Shell script 구동 원리
- 확장자는 의미 없음 : myjob.sh
- $? : 이전명령 성공 여부
- 자식 shell 이 구동되며 실행  

```
# bash myjob.sh
bash --> bash --> 명령들..
```

- 실행 권한을 허용하려면  

```
# mysum.sh
  -bash: mysum.sh: command not found
# ./mysum.sh
  -bash: ./mysum.sh: 허가 거부됨
# chmod 755 myjob.sh
```

- 명령으로 실행 할때 어떤 쉘인지 알려주려면   

```
# vi myjob.sh
#! /bin/bash <-- 제일 앞에 쉘 정보 알려줌
```

- 현재 쉘에서 실행하려면  

```
# . .bashrc
# source .bashrc
```

## 리눅스의 종료와 재시작
1) 종료
- halt
- sutdown -h +10[0,now,hh:mm]
- init 0

2) 재시작
- reboot
- sutdown -r +10[0,now,hh:mm]
- init 6

## Run Level
```
# /etc/inittab
   0 - halt (Do NOT set initdefault to this)
   1 - Single user mode  <-- network 안됨
   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
   3 - Full multiuser mode
   4 - unused
   5 - X11 <-- GUI
```
```
- 런레벨별 실행, 중지되는 서비스 확인 K / S
# ls /etc/rc?.d
```
- POST : power on self test
- MBR(부트로더) GRUB
- Kernel load
- init process

## GRUB
- /etc/grub.conf --> /boot/grub/grub.conf
- hiddenmenu
- timeout=5
- title
- splashimage=(hd0,0)/grub/splash.xpm.gz
- (hd0,0) : 첫번째 하드디스크의 첫번째 파티션 == /boot
- grub passwd 설정  
  ```
  # grub
  grub> md5crypt
  Password: ***
  Encrypted: $1$fOVnB/$Ke.EE9GeW/TuEKQeXYq1V.

  # vi /etc/grub.conf
  password --md5 $1$fOVnB/$Ke.EE9GeW/TuEKQeXYq1V.
  ```

## 리눅스 시간
- 시스템 시간 : data
- H/W 시간 (CMOS 시간) : clock
- 두시간 동기화 :  clock -s(시스템시간 바꾸기) / -w(하드웨어시간 바꾸기)
- 시스템 시간 맞추기
```
# rdate -s time.bora.net  (-p print,  -s setting)
# date mmddHHMM[yy]
```

## VI editor
- 비정상 종료시 복구방법

```
vi -r myfile
rm .myfile.swp
```

- VI 환경 설정
  - .exrc(Unix 호환)  / .vimrc
- Tips
  - 입력모드 : i, r, a, o
  - 단어변경 : cw
  - set number / set nonumber
  - set showmode   # 리눅스는 기본, -- INSERT --
  - set showmatch  # 괄호 매치
  - set ts=4

## MAN page 분류번호 : ex) LS(1)
- man 5 passwd
- man -a passwd
- Section 1 : 일반사용자 명령 ( /bin, /usr/bin )
- Section 8 : 관리자 명령 ( /sbin, /usr/sbin )
- Section 2 : System Call (Kernel 함수)
- Section 3 : Library Call ( /lib, /usr/lib )
- Section 5 : File Format ( /etc )

## Linux Directory
- Tree 모양의 계층적 구조
- / : 최상위 디렉토리
- /boot : 커널 관련
- /dev : 장치파일
- /bin : 명령어
- /sbin : 관리 명령어
- /lib : Library
- /usr : 추가적인..
- /root : root 계정 홈
- /home : user들의 홈
- /etc : 시스템 설정 파일
- /mnt : 새로 마운트 할 곳
- /media : 이동식 저장 장치를 시스템에서 auto mount
- /proc : process 관련된 정보 memory fs,   ls /proc/$$
- /sys : 하드웨어관련된 정보 memory fs
- /misc: NFS 관련
- /net : NFS 관련
- ...

## Mount
- scsi : /dev/sda[a, b, c, d]
- IDE : /dev/hda[a, b, c, d]
- partition  

```
[DISK]
-----
|sda1  <-- FS : mounted  /boot
-----
|sda2  <-- SWAP
-----
|sda3  <-- FS : mounted  /
-----
# df
Filesystem    1K-blocks     Used  Available  Use%  Mounted on
/dev/sda3     11165404   4003200    6585876   38%  /
/dev/sda1       101086     18507      77360   20%  /boot
/dev/hdc       3754570   3754570          0  100%  /media/CentOS_6.8_Final

# swapon -s
Filename      Type        Size      Used    Priority
/dev/sda2     partition   4096564    964      -1
```

- \# umount /boot or [/dev/sda1]
- \# mount /dev/sda1 /boot
- /etc/fstab
- /mnt : 새로 마운트 할 곳
- fdisk -> mkfs -> mount
- 리눅스에 따라서 CD/DVD 장치 명이 다를 수 있음 : ls /dev/cdrom

## 사용자와 그룹
- useradd  -u(ID) -g(group) -d(home) -s(shell) : /etc/default/useradd
- usermod
- userdel
- groupadd
- groupmod
- groupdel
- Primary Group : -g ,로그인 했을 때 기본 속한 그룹 /  Secondary Group : -G
- newgrp (group) : 그룹 변경    <-> exit
- passwd -l username : lock
- passwd -u username : unlock
- passwd -d username : delete password
- pwconv / pwunconv : shadow file 생성 삭제
- /etc/passwd : 계정 정보, Primary Group 정보

```
계정:패스워드:UID:GID:GECOS:디렉토리:쉘
user:x:500:500::/home/user:/bin/bash
```

- /etc/shadow : 비밀번호 정보

```
계정:암호화된패스워드:암호변경일:암호사용최소일자:암호사용최대일자:
user:$1$R0qq4D8J$HM2Eq9PQwP.d/kL2ZhRRz/:17213:0:99999:7:::
```

- /etc/group : 그룹 정보, 유저의 Secondary Group 정보

```
user:x:500:
```

## 사용자별 공간 할당 - quota
- 파일시스템마다 사용자나 그룹이 생성할 수 있는 파일의 용량과 개수를 제한하는 것   

```
1) # rpm -qa|grep quota
2) # mkdir /userHome
3) # vi /etc/fstab
     /dev/sdb1    /userHome    ... defaults,usrquota ...
   # mount  /userHome
   혹은
   # mount -o usrquota /dev/sdb1 /userHome
   # mount
      /dev/sdb1 on /userHome type ext3 (rw,usrquota)
4) 테스트 user 생성
   # useradd  -d  /userHome/john   john
   # useradd  -d  /userHome/bann   bann
   # passwd john
   # passwd bann

5) # cd /userHome
   # quotacheck  -aum   (all, user, mount)  <-- quota check 를 위한 db 생성

6) # edquota -u john   <--- Block 크기 or Inode(파일) 개수 제한
     Disk quotas for user john (uid 507):
     Filesystem   blocks   soft   hard   inodes   soft   hard
     /dev/sdb1      40      0      0       10      0      0

7) # quotaon  /userHome   <=>   # quotaoff  /userHome

8) # edquota -p  john  bann
   # repquota /userHome
   # quota  bann
```

## File 유형
- \- : file
- d : directory
- b: block device
- c : character device
- l : link
- s : socket
- p : pipe

#### Socket
- 양방향
- 인터넷 소켓 : 네트워크 통신용 ==> netstat 명령으로 확인
- 유닉스 소켓 : IPC용

#### Pipe
- 단방향
- Anonymous pipe : ps -e | grep init
- Named Pipe : mkfifo 명령으로 생성

## File Permission
- user  group  other
-  rwx    rwx    rwx
- chmod 777 file
- chmod 7777 file
- chown username[.groupname] file
- chgrp groupname file
- directory : rwx : ls / 파일 생성,삭제 / cd
- umask : 0022 ->  000 010 010  허용하고 싶지 않은 비트 설정  (rw-r--r--)  

```
type        u    g    o
[0000][000][000][000][000]
       set user id bit  : rws : owner uid 권한으로 실행 가능
       set group id bit : rws : owner gid 권한으로 실행 가능
       sticky bit       : rwt : swap 공간에 남아 있음. root 만 설정 가능  

$ ll /usr/bin/chsh
-rws--x--x 1 root root 19096  9월  4  2009 /usr/bin/chsh
$ chsh  -> /etc/passwd 수정해야 됨
[ps table]
* set user id bit == 1
real user id : 일반 유저           : uid
effective user id : 권한 체크 유저  : owner uid
* set group id bit == 1
real group id : 일반 그룹           : uid
effective group id : 권한 체크 그룹 : owner gid

$ ll -d /tmp
drwxrwxrwt 12 root root 4096  2월 21 15:38 /tmp
```

## Switch User
- su : root는 생략되어 실행
- su username : 계정만 바꿈, bash 나 PATH 등은 유지
- su \- username : username 으로 로그인 한 것과 동일
- su -l username : 위와 동일
- sudo : root의 제한된 명령을 일반 user에게 허용하기 위해.. (/etc/sudoers)

## 링크
- ls -i : inode 번호 까지
- ls -l : inode 에 있는 정보까지 같이 보여 줌

```
[Filesystem]
| SB |   INode Block    |     Data Block    |
                               |-> 1000 | fileA
```

- Hard Link : ln

```
$ vi fileA
$ ln fileA fileA.ln
$ ls -il
229923 -rw-r----- 2 root root 12  2월 21 17:05 fileA
229923 -rw-r----- 2 root root 12  2월 21 17:05 fileA.ln

==> inode#, file type, link count, file size 가 동일 함
==> INode block을 공유
==> data를 변경하더라도 둘이 동일. Data block은 동일하게 사용하기 때문에
==> inode, data block 둘다 동일
```

- Symbolic Link : ln -s

```
# ln -s fileA fileA.sym
# ll -i
 229923 -rw-r----- 2 root root   14  2월 21 17:08 fileA
 229561 lrwxrwxrwx 1 root root    5  2월 22 09:46 fileA.sym -> fileA

 ==> INode block이 다름
 ==> Data block에 링크하고 있는 파일의 패스 정보가 들어있음
 ==> inode, data block 둘다 다름
 ==> 링크파일을 삭제하고 다시 만들어도 링크관계가 유지됨
```

- Hard link 제한 점
  - inode 번호를 참조 하기 때문에 다른 파일시스템에 있는 파일은 링크 불가

```
# ln /boot/grub/grub.conf mygrub.conf
ln: creating hard link 'mygrub.conf' to '/boot/grub/grub.conf': 부적절한 장치간 연결
```

  - 다렉토리는 링크 불가

```
# ln mydir testdir
ln: 'mydir': 디렉토리는 하드링크할 수 없습니다
```

## 프로그램 설치 명령
- RPM (Redhat Package Manager) : 의존성 문제 해결 X  

```
설치 : # rpm -Uvh package_name.rpm
삭제 : # rpm -e package_name
조회 : # rpm -qa | grep telnet     (-qf  -ql  -qi)
```

- YUM (Yellowdog Update Modified) : 의존성 문제 해결 O  

```
# yum install package_name
# yum localinstall package_name.rpm
# yum check-update
# yum update package_name
# yum remove package_name
# yum info package_name
```

## Telnet
- Telent 요청시 프로세스 생성 과정 (pstree 로 확인 가능)  
  init -> xinetd (/etc/xinetd.d) -> in.telnetd -> login -> bash
- telnet-server 설치 방법   

```        
1) # rpm -qa|grep telnet-server  
2) # yum [-y] install telnet-server  
3) # vi /etc/xinetd.d/telnet  
        disable=no  
   혹은  
   # chkconfig telnet on  
4) # service xinetd restart[start, stop, status]  
5) Listen Socket 확인  
   # netstat -a|grep telnet  
6) 연결시도후 실패할 경우 방화벽 확인  
   # system-config-securitylevel  
7) root로 로그인할려면  
	 # mv /etc/securetty /etc/securetty.bak  
```

## 파일 묶기 : tar
**절대경로 사용시 주의! 경로의 맨앞에 / 가 없음.**
- 생성 : `# tar -cvf 파일명 대상목록`  
- 목록 : `# tar -tvf 파일명`  
- 풀기 : `# tar -xvf 파일명`  
- 압축 해제 및 tar 풀기  

```
# tar -xzvf 파일명  
J : tar + xz   
z : tar + gzip   
j : tar + bzip2   
```

## 파일 압축
- zip unzip  : 원본 파일 놔두고 압축  

```
# zip etc.tar.zip etc.tar
# unzip etc.tar.zip
```

- gzip gunzip : 하드링크 관계, 원본 파일 삭제 후 압축  

```
# gzip etc.tar
# gzip -d etc.tar.gz   # gunzip etc.tar.gz
```

- bzip2 bunzip2 : Symbolic link, 원본 파일 삭제 후 압축  

```
# bzip2 etc.tar
# bzip2 -d etc.tar.bz2
```

- xz : 원본 파일 삭제 후 압축  

```
# xz etc.tar
# xz -d etc.tar.xz
```

- 압축률  

```
# ll etc*
-rw-r--r-- 1 root root 13763954  2월 22 13:23 etc.tar.zip
-rw-r--r-- 1 root root 13763834  2월 22 12:01 etc.tar.gz
-rw-r--r-- 1 root root  9214644  2월 22 12:01 etc.tar.bz2
-rw-r--r-- 1 root root  8282932  2월 22 12:01 etc.tar.xz
```

## dump & restore
- dump  

```
* Full Backup
# dump -0uf /tmp/boot.dump /boot # /dev/sda1  

* Incremental Backup
# dump -2uf /tmp/boot.dump /boot # /dev/sda1

# cat /etc/dumpdates
/dev/sda1 0 Wed Feb 22 13:40:22 2017 +0900
/dev/sda1 2 Wed Feb 22 13:43:42 2017 +0900
```

- restore  

```
# restore -rf /tmp/boot.dump
# restore -rf /tmp/boot.dump2
```

## 파일 위치 검색
- find  

```
  find  찾는경로  찾는조건          동작
# find /home  -name Filename    [-print]
                -size +10k        -exec  리눅스명령  {} \;
                -user KIM
                -type f[d]
                -inum 1000
                -perm 777
                -newer
# find . -name fileA
# find /bin -inum 1015859
# find . -name fileA -exec ls -l {} \;
# find . -name fileA -exec rm {} \;
```

- which : $PATH 에 설정된 디렉터리만 검색  

```
# which ifconfig
```

- whereis : 실행 파일 및 소스, man 페이지 검색  

```
# whereis ifconfig
```

## Time-Schedule 작업
- 일회성 작업 : at  

```
# at now + 3minutes
at> date > /root/at.out
at> cal >> /root/at.out
at> <EOT>
job 2 at 2017-02-22 14:35

# at -l   <==> atq
1       2017-02-22 15:29 a root
# at -d 1 [작업번호]  <==> atrm 1
```
```
/etc/at.deny  : 사용 제한
/etc/at.allow : 사용 허용
```

- 반복적인 작업 : crontab  

```
crond 실행되면서 /etc/crontab 참조
분 시 일 월 요일 사용자 실행명령
01  *  * * *    root   run-parts /etc/cron.hourly
```
```
* /var/spool/cron/user_name 으로 크론테이블 생성됨 but root만 접근 가능
$ ll /usr/bin/crontab  <-- set user id bit
-rwsr-sr-x 1 root root 315416  2월 27  2009 /usr/bin/crontab

# crontab -l  : 리스트
# crontab -e  : 크론 테이블 수정
  45,46,47 14 * * 3 date >> /root/cron.out
  45-47 14 * * 3 date >> /root/cron.out
# crontab -r  : 삭제
```

## 파이프
- ls | more
- 2개의 프로그램을 연결해주는 연결 통로

## 필터
- stdin 으로 받아 stdout으로 내보냄
- cat
- wc -[lwc] filename
- grep (global regx print) : grep ^root /etc/group
- awk
- sed

## 리다이렉션
- 입출력 방향 변경  
- stdin 변경   

```
$ 명령 < 파일
```

- stdout 변경  

```
$ 명령 > 파일     overwrite  
$ 명령 >> 파일    append                
```

- stderr 변경  

```
$ 명령 2>파일  
$ 명령 2> /dev/null
```

## 프로세스
- 실행중인 프로그램
- $$ : 내 PID
- $? : 마지막 실행된 자식 프로세스의 종료 상태
- 포그라운드 프로세스 : $ sleep 100
- 백그라운드 프로세스 : $ sleep 100 &
- 좀비 프로세스 : $ ps -e | grep defunct
- 고아 프로세스 : 백그라운드 프로세스 종료전에 부모 프로세스가 종료된 경우  

```
# ps -ef | grep sleep
root      7926     1  0 16:12 ?        00:00:00 sleep 1000
root      7948     1  0 16:17 ?        00:00:00 sleep 2000
PPID : 1,  TTY : ? 로 변경 됨
```

- 작업 : 쉘에서 실행시킨 프로세스  

```
# sleep 1000 &
[1] 8018
# jobs
[1]-  Running                 sleep 1000 &
[2]+  Running                 sleep 1000 &
# fg
sleep 1000
[2]+  Stopped                 sleep 1000
# jobs
[1]-  Running                 sleep 1000 &
[2]+  Stopped                 sleep 1000
# bg 2
[2]+ sleep 1000 &
# jobs
[1]-  Running                 sleep 1000 &
[2]+  Running                 sleep 1000 &
```

- 종료  

```
# kill 8018  <- pid
# kill %2    <- job number
# kill -l
# kill $$    <- default -15. bash 가 무시함
9) SIGKILL  19)SIGSTOP  무시할 수 없음. 커널이 무조건 전달 함
```

## Service
- service network restart # status, start, stop
- 데몬, 서버프로세스
- 시스템과 독자적으로 구동되어 제공되는 프로세스

## 하드디스크 추가하기
- fdisk : 파티션 나누기   

```
# fdisk  /dev/sdb
      n -> p -> 1 -> enter -> enter -> p -> w
# fdisk -l /dev/sdb
  Disk /dev/sdb: 1073 MB, 1073741824 bytes
  255 heads, 63 sectors/track, 130 cylinders
  Units = cylinders of 16065 * 512 = 8225280 bytes

     Device Boot      Start         End      Blocks   Id  System
  /dev/sdb1               1         130     1044193+  83  Linux
```

- mkfs : 파일시스템 생성

```
# mkfs -t ext3 /dev/sdb1
  mke2fs 1.39 (29-May-2006)
  Filesystem label=
  OS type: Linux
  Block size=4096 (log=2)
  Fragment size=4096 (log=2)
  130560 inodes, 261048 blocks
  13052 blocks (5.00%) reserved for the super user
  First data block=0
  Maximum filesystem blocks=268435456
  8 block groups
  32768 blocks per group, 32768 fragments per group
  16320 inodes per group
  Superblock backups stored on blocks:
          32768, 98304, 163840, 229376

  Writing inode tables: done
  Creating journal (4096 blocks): done
  Writing superblocks and filesystem accounting information: done

  This filesystem will be automatically checked every 24 mounts or
  180 days, whichever comes first.  Use tune2fs -c or -i to override
```

- mount  

```
# mkdir  /mydata
# mount  /dev/sdb1  /mydata
# ls /mydata
  lost+found
# mount
  /dev/sdb1 on /mydata type ext3 (rw)
```

- /etc/fstab : 시스템 시작시 자동으로 마운트 되도록 설정  

```
# blkid /dev/sda3
  /dev/sda3: LABEL="/" UUID="22bf335b-fc20-4c8b-8694-760499058928" TYPE="ext3" SEC_TYPE="ext2"
# e2label /dev/sdb1 /mydata
# vi /etc/fstab
  장치명[LABEL, UUID]  마운트될디렉토리    FS타입   속성       덤프사용여부    파일시스템체크여부
  /dev/sdb1           /mydata            ext3     defaults        1             1
```

## RAID
```
* 실습 환경
scsi |------[sda]--[sdb]--[sdc]--[sdd]--[sde]--[sdf]--[sdg]
boot        |--raid 0--|  |--raid 1--|<--복구
```

- Linear RAID : 1번 하드디스크가 모두 채워진 뒤 2번째 하드디스크를 사용
- RAID 0(Stripping) : 여러 하드디스크에 동시에 저장. 저장성능 효츌적. 데이터 안전성 보장 못함

```
1) # fdisk  /dev/sdc
	   n ->  p  ->  1  -> enter  -> enter  -> t  -> L -> fd  -> p  -> w
2) # fdisk  /dev/sdd
 위와 동일
3) # ll /dev/md*
            장치명     타입    메이져  마이너
   # mknod  /dev/md0   b      9       0
   - 매이져: /proc/devices 확인 가능
   - 마이너: 하드웨어의 위치정보 ll /dev/sd*
4) # mdadm  --create   /dev/md0    \
            --level=0   \
            --raid-devices=2  /dev/sdc1  /dev/sdd1
 # mdadm --detail --scan
  혹은
 # mdadm -D -s
5) # mkfs.ext3  /dev/md0
6) # mkdir  /raid0
   # mount  /dev/md0  /raid0
7) # df => 용량확인
   # cp /bin/c*   /raid0
8) # vi /etc/fstab
   /dev/md0   /raid0   ext3   defaults  1  1
 # umount /raid0
 # mount /raid0
9) # halt
============>  disk 제거후 재시작  
1) # vi /etc/fstab 에서 해당정보 제거  <-- 복구모드에서는 readonly mode 로 마운트 됨
2) # mount -o remount /              <-- rw 모드로 다시 마운트 되도록 설정
 # vi /etc/fstab
3) # reboot
```

- RAID 1(미러링) : 여러 하드디스크에 중복 저장, 데이터 안전성 보장됨  

```
1) # fdisk  /dev/sdd
   n ->  p  ->  1  -> enter  -> enter  -> t  -> L -> fd  -> p  -> w
2) # fdisk  /dev/sde
   위와 동일
3) # mdadm  --create   /dev/md1  \
            --level=1    \
            --raid-devices=2  /dev/sdd1  /dev/sde1
   # mdadm -D /dev/md1
4) # mkfs.ext3  /dev/md1
5) # mkdir  /raid1
   # mount  /dev/md1  /raid1
6) # df => 용량이 1/2
   # cp /bin/d*   /raid1
7) # vi /etc/fstab
     /dev/md1   /raid1   ext3   defaults  1  1
   # umount /raid1
   # mount /raid1
8) # halt
============> disk 제거후 재시작
1) 확인
   # ll /dev/sd*
   # df
   # ll /raid1
   # mdadm -D  /dev/md1
   Number   Major   Minor   RaidDevice State
     0       8       49        0      active sync   /dev/sdd1
     1       0        0        1      removed
2) # fdisk  /dev/sde
    n ->  p  ->  1  ->  enter  ->  enter  -> t  -> fd  ->  p  -> w
3) # mdadm  /dev/md1  --add  /dev/sde1 ; mdadm -D /dev/md1
   Number   Major   Minor   RaidDevice State
     0       8       49        0      active sync   /dev/sdd1
     2       8       65        1      spare rebuilding   /dev/sde1
   # mdadm -D /dev/md1
  Number   Major   Minor   RaidDevice State
     0       8       49        0      active sync   /dev/sdd1
     1       8       65        1      active sync   /dev/sde1
   # fdisk  -l
```

- RAID 5 : 하드디스크 최소 3개 이상 필요. 패리트비트 사용. (0+0+0+pairity= 짝수). 공간효율 결함 허용. N-1 만큼 공간 사용  

## LVM (Logical Volume Manager)
- 여러개의 하드디스크를 합쳐서 한개의 파티션으로 구성 후 필요에 따라 여러 파티션으로 구성 가능  
- PV: Physical Volume   
- VG: Volume Group   
- LV: Logical Volume  
- PE: Physical Extent  
- LE: Logical Extent

```
  PV            VG           LV
[ 20G ]                    [ 30G ]
          =>  [ 40G ]  =>
[ 20G ]                    [ 10G ]
```

- pvcreate PV
- vgcreate [-s size] VGName PVPath
- lvcreate [-L size] [-n lvname] VGName
- pvdisplay / vgdisplay / lvdisplay  -m
- lvextend  <--> lvreduce
- vgextend  <--> vgreduce  
- 실습  
  ```
  1) # fdisk /dev/sdc
     # fdisk  /dev/sdd
      n ->  p  ->  1  ->  enter  ->  enter  ->  t  -> L  -> 8e -> p -> w

  2) # pvcreate [-f] /dev/sdc1  /dev/sdd1
     # pvdisplay

  3) # vgcreate myVG  /dev/sdc1  /dev/sdd1
     # vgdisplay  -v  myVG

  4) # lvcreate --size 500m  --name myLG1  myVG
     # lvcreate --size 800m  --name myLG2  myVG

  5) # mkfs -t ext3  /dev/myVG/myLG1
     # mkfs -t ext3  /dev/myVG/myLG2

  6) # mkdir /lvm1
     # mkdir /lvm2
     # mount /dev/myVG/myLG1  /lvm1
     # mount /dev/myVG/myLG2  /lvm2
  ```

## 네트워크
- 장치 정보 : ifconfig eth0
- ifup eth0 / ifdown eth0
- root 만 접근가능 하도록 설정

```
# touch /etc/nologin
```

- IP 고정하기  

```
# cat /etc/sysconfig/network-scripts/ifcfg-eth0
# Advanced Micro Devices [AMD] 79c970 [PCnet32 LANCE]
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes
HWADDR=00:0c:29:7c:ca:a6
```
```
# system-config-network
# service network restart
```

- nslookup : DNS test  

```
# nslookup
> server
Default server: 192.168.214.2
Address: 192.168.214.2#53

> www.naver.com
Server:         192.168.214.2
Address:        192.168.214.2#53

Non-authoritative answer:
www.naver.com   canonical name = www.naver.com.nheos.com.
Name:   www.naver.com.nheos.com
Address: 125.209.222.141
Name:   www.naver.com.nheos.com
Address: 125.209.222.142
> exit
```

- /etc/sysconfig/network : hostname 설정
- /etc/sysconfig/network-scripts/ifcfg-eth0 : 네트워크 정보
- /etc/nsswitch.conf :  정책  > hosts:  files dns
- /etc/hosts :
- /etc/resolv.con : DNS 서버 정보

## DNS(Domain Name System) 서버 : \#53
- 캐싱 전용 네임 서버 : URL의 IP 주소를 알려주는 네임 서버  

```
# yum install caching-nameserver
# vi /etc/named.caching-nameserver.conf   <-- 상위 버전에서는 /etc/named.conf
  127.0.0.1  -> any
  localhost  -> any
# named-checkconf /etc/named.caching-nameserver.conf
# service named start
# nslookup    <-- nameserver 테스트
> server 192.168.214.100        
Default server: 192.168.214.100
Address: 192.168.214.100#53
> www.naver.com
...

# vi /etc/resolv.conf
nameserver 192.168.214.100

# chkconfig --list named
# chkconfig --level 235 named on
```

- 마스터 네임 서버 : 도메인에 속한 컴퓨터들의 이름을 관리하고, 외부에서 해당 컴퓨터 IP주소를 알기 원할 때 알려주는 네임 서버

```
# vi /etc/named.rfc1912.zones   <-- 상위 버전에서는 /etc/named.conf 에 같이 관리
  zone "test.com" IN {
      type master;
      file "test.com.db";
      allow-update { none; };
  };

# vi /var/named/chroot/var/named/test.com.db
  $TTL    3H
  @       SOA @   root. ( 2 1D 1H 1W 1H )
          IN  NS  @
          IN  A   192.168.214.100
  www     IN  A   192.168.214.200
  ftp     IN  A   192.168.214.100
  blog    IN  A   192.168.214.150
  ==> TTL : time to live
      @ : zone name       

# named-checkzone  test.com  /var/named/chroot/var/named/test.com.db
  zone test.com/IN: loaded serial 2
  OK

# service named restart
```

## FTP 서버구축 : vsftpd
- 설치 여부 확인  

```
# rpm -qa|grep vsftpd
```

- 설치  

```
# yum install vsftpd`
# service  vsftpd  start [ stop, start, restart, status ]
# netstat -a|grep ftp
```

- 부팅시 자동으로 실행되도록 설정  

```
# chkconfig --list vsftpd
  vsftpd          0:해제  1:해제  2:해제  3:해제  4:해제  5:해제  6:해제   <-- run level
# chkconfig --level 235 vsftpd on    
# ll /etc/rc2.d/S*vsftp*
  lrwxrwxrwx 1 root root 16  2월 24 10:23 /etc/rc2.d/S60vsftpd -> ../init.d/vsftpd
# chkconfig --level 2 vsftpd off     <-- 링크 삭제
# ll /etc/rc2.d/K*vsftp*
  lrwxrwxrwx 1 root root 16  2월 24 10:26 /etc/rc2.d/K50vsftpd -> ../init.d/vsftpd
```

- 연결시도후 실패할 경우 방화벽 확인  

```
 # system-config-securitylevel  or system-config-firewall
 # iptables -F
```

- root로도 연결하려면  

```
* root 주석처리
/etc/vsftpd/ftpusers    <-- not allowed to login
/etc/vsftpd/user_list   <-- denied user
```

- Anonymous User 연결  

```
# grep ^ftp /etc/passwd
  ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
# cd /var/ftp  <-- ftp의 home directory
# ls -l
  drwxr-xr-x 2 root root 4096  9월  4  2009 pub

# vi /etc/vsftpd/vsftpd.conf
  anon_upload_enable=YES    <-- upload 허용
# chmod 777 /var/ftp/pub  or   chown ftp.ftp /var/ftp/pub
```

- xinetd 형태로 설치  

```
# service vsftpd stop
# vi /etc/vsftpd/vsftpd.conf
	listen=NO
# cp /usr/share/doc/vsftpd2.x.x/vsftpd.xinetd
	/etc/xinetd.d/vsftpd
# vi /etc/xinetd.d/vsftpd
	disable=no
# service xinetd restart
```

## SELinux
- network의 application level의 보안 정책    

```
# getenforce
  Enforcing
# getsebool -a | grep ftp
  allow_ftpd_anon_write --> off
  allow_ftpd_full_access --> off
  allow_ftpd_use_cifs --> off
  allow_ftpd_use_nfs --> off
  ftp_home_dir --> off
  ftpd_connect_db --> off
  ftpd_use_fusefs --> off
  ftpd_use_passive_mode --> off
  httpd_enable_ftp_server --> off
  tftp_anon_write --> off
  tftp_use_cifs --> off
  tftp_use_nfs --> off
# setsebool -P allow_ftpd_full_access 1

# vi /etc/sysconfig/seLinux
# setenforce 0
# getenforce
  Permissive
```

## NFS (Network File System) 서버
- NFS server : 최소 runlevel 3

```
# rpm -qa | grep nfs-utils
# vi /etc/exports
  /share  192.168.214.*(rw,sync)   # (rw,sync,no_root_squash)
# mkdir /share
# chmod 707 /share
# service nfs start
# exportfs -v
  /share  192.168.214.*(rw,wdelay,root_squash,no_subtree_check,anonuid=65534,anongid=65534)
```

  - nfs는 rpc를 사용하는데.. rpc는 고정된 port가 아니기 때문에, 중재자(111 포트)가 필요  

```
# ps -e|grep port  or  rpcbind  
# rpcinfo -p
  프로그램 버전 원형   포트
  100000    2   tcp    111  portmapper
```

- client  

```
# showmount -e 192.168.214.100
  Export list for 192.168.214.100:
  /share 192.168.214.*
# mkdir /myShare
# mount -t nfs 192.168.214.100:/share /myShare
# df
192.168.214.100:/share  11165408 4216736   6372352  40% /myShare

- root 계정으로 파일을 생성시 NFS 서버 설정에 따라 계정이 다르게 보임
-rw-r--r--. 1 nfsnobody nfsnobody    0 2017-02-24 15:04 rootfile    <-- root_squash
-rw-r--r--. 1 root      root         0 2017-02-24 15:06 rootfile2   <-- no_root_squash
```

## NTP (Network Time Protocol)
- Stratum 1~15 계층 구조로 시간을 맞출 수 있음
- 설치

```
# rpm -qa|grep ntp
# yum install ntp
```

- 서버 (CentOS5)

```
# vi /etc/ntp.conf
	server 127.127.1.0  # local clock
	fudge  127.127.1.0  stratum 10
# service ntpd start
# chkconfig --level 35 ntpd on
# ntpq -p
  remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*LOCAL(0)        .LOCL.          10 l   27   64  377    0.000    0.000   0.001
```

- 클라이언트 (CentOS6)

```
# vi /etc/ntp.conf
	server 192.168.214.100
# service ntpd start
# chkconfig --level 35 ntpd on
# ntpq -p     <== stratum 16 으로 아직 맞춰지지 않았음
  remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
192.168.214.100 .INIT.          16 u   37   64    0    0.000    0.000   0.000
# ntpq -p     <== stratum 11 (server st == 10)
  remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*192.168.214.100 LOCAL(0)        11 u    1   64    1    0.646    0.025   0.000
```

## autofs
- NFS client service  

```
# rpm -qa | grep autofs
# ps -e | grep auto
 1745 ?        00:00:00 automount
# lsmod | grep auto
 autofs4                21076  3

# vi /etc/auto.master
  /net    -hosts          : /net 아래에서 호스트명으로 접속(cd)시 자동 마운트
  /misc   /etc/auto.misc  : auto.misc 에 설정된 정보로 자동 마운트
# vi /etc/auto.misc
  data            -fstype=nfs             192.168.214.100:/share

# cd /net/192.168.214.100/share  
# cd /misc/data
```

## Samba
- Unix 계열, Windows 계열 모두 가능
- 윈도우 : 공유 폴더

```
# rpm -qa|grep samba
# smbclient -L 192.168.214.1/smbShare -U linuxuser
  Domain=[M13054] OS=[Windows 7 Enterprise 7601 Service Pack 1] Server=[Windows 7 Enterprise 6.1]

          Sharename       Type      Comment
          ---------       ----      -------
          ADMIN$          Disk      원격 관리
          C$              Disk      기본 공유
          IPC$            IPC       원격 IPC
          samShare        Disk
  session request to 192.168.214.1 failed (Called name not present)
  session request to 192 failed (Called name not present)
  session request to *SMBSERVER failed (Called name not present)
  NetBIOS over TCP disabled -- no workgroup available

# mount -t cifs //192.168.214.1/smbShare  /sambaMount -o username=linuxuser
```

## Command
```
- 일반명령 : /bin/  or /usr/bin
- 관리명령 : /sbin/ or /usr/sbin
- $PATH 에서 명령을 찾아서 실행
$ shutdown -h +10
-bash: shutdown: command not found
$ /sbin/shutdown -h +10
shutdown: you must be root to do that!
```
- id : 내 정보
- passwd : 패스워드 변경
- ipcs : ipc status
- whereis : 어디에 있나?
- pwd : 현재 디렉토리
- date : 시간 확인
- cal : 현재 달
- chsh : change shell
- touch : 0byte file 생성
- cat / head / tail / more / less : 파일 보기
- file : 어떤 종류의 파일인지 보기
- history: !number !character
- alias c=clear
- echo $hello <-- variable
- ps -f (현재 터미널에서 사용중인 프로세스)
- export : 지역변수 -> 환경변수
- env : 환경변수 보기
- set : 지역변수 + 환경변수 보기
- who : 누가 접속해 있나
- who -r : run level 확인
- tty : 접속한 가상 터미널 확인
- date : 시스템 시간 확인
- clock : hardware 시간 확인
- rdate : 시간 재 설정
- df : 파일시스템 확인
- swapon : swap 공간 확인
- free : 메모리 공간 확인
- mount : 마운트, 장치를 특정 디렉토리에 연결
- umount : 언마운트
- eject : 언마운트 + 장치 제거
- pstree : 프로세스 부모 자식 관계 보기
- stty -a : 터미널 시그널 확인
- blkid /dev/sda3 : 블럭 ID
- scp src dest : 리모트 복사
- lsmod : 커널 모듈 확인
