---
title: tcpdump
description: 工具包
date: 2023-11-17
weight: 30000
---


<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< note >}}


{{< /note >}}

- 抓取16310和3306端口的数据包
```bash
tcpdump -nn -vv tcp dst port 3306  -w 3306.pcap
```

1. option 可选参数：将在后边一一解释。
2. proto 类过滤器：根据协议进行过滤，可识别的关键词有： tcp, udp, icmp, ip, ip6, arp, rarp,ether,wlan, fddi, tr, decnet
3. type 类过滤器：可识别的关键词有：host, net, port, portrange，这些词后边需要再接参数。
4. direction 类过滤器：根据数据流向进行过滤，可识别的关键字有：src, dst，同时你可以使用逻辑运算符进行组合，比如 src or dst


## 过滤器

当你在使用多个过滤器进行组合时，有可能需要用到括号，而括号在 shell 中是特殊符号，因为你需要使用引号将其包含


### 显示格式



| 参数  | 值 | 功能                                                                                   |
|:------|:--|:-------------------------------------------------------------------------------------|
| `-A`  | 无 | 以ASCII码方式显示每一个数据包(不显示链路层头部信息)                                    |
| `-X`  | 无 | 以hex和ASCII两种形式显示包的内容，当你需要查看包中的内容时，使用hex格式输出是非常有用的。 |
| `-XX` | 无 | 以hex和ASCII两种形式显示包的内容，且增加以太网header的显示                              |


- `tcpdump -A -tttt -nn   port 8032`

```bash
2025-03-13T12:19:16 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn   port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:19:27.056555 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966300875:3966300895, ack 1259329553, win 229, options [nop,nop,TS val 237957599 ecr 658287522], length 20
E..H.G@.@.K........0...`.h..K.......e......
....'<.........select user()
2025-03-13 12:19:27.057018 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], seq 1:73, ack 20, win 235, options [nop,nop,TS val 658351246 ecr 237957599], length 72
E..|..@.@......0.....`..K....h.............
'=................def....user()...................root@172.17.138.136...........
2025-03-13 12:19:27.057073 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], ack 73, win 229, options [nop,nop,TS val 237957600 ecr 658351246], length 0
E..4.H@.@.K........0...`.h..K..Y....e......
....'=..

```


- `tcpdump -X -tttt -nn   port 8032`

```bash
2025-03-13T12:17:44 [root@x86centos7-136 /root] # tcpdump -X -tttt -nn   port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:18:23.332415 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966300855:3966300875, ack 1259329481, win 229, options [nop,nop,TS val 237893875 ecr 658204910], length 20
	0x0000:  4500 0048 8a45 4000 4006 4b8f ac11 8a88  E..H.E@.@.K.....
	0x0010:  ac11 8230 deda 1f60 ec68 f2b7 4b0f d7c9  ...0...`.h..K...
	0x0020:  8018 00e5 6516 0000 0101 080a 0e2d f8f3  ....e........-..
	0x0030:  273b 68ee 1000 0000 0300 0173 656c 6563  ';h........selec
	0x0040:  7420 7573 6572 2829                      t.user()
2025-03-13 12:18:23.332898 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], seq 1:73, ack 20, win 235, options [nop,nop,TS val 658287522 ecr 237893875], length 72
	0x0000:  4500 007c f812 4000 4006 dd8d ac11 8230  E..|..@.@......0
	0x0010:  ac11 8a88 1f60 deda 4b0f d7c9 ec68 f2cb  .....`..K....h..
	0x0020:  8018 00eb 87f1 0000 0101 080a 273c aba2  ............'<..
	0x0030:  0e2d f8f3 0100 0001 011c 0000 0203 6465  .-............de
	0x0040:  6600 0000 0675 7365 7228 2900 0cff 0080  f....user().....
	0x0050:  0400 00fd 0000 1f00 0014 0000 0313 726f  ..............ro
	0x0060:  6f74 4031 3732 2e31 372e 3133 382e 3133  ot@172.17.138.13
	0x0070:  3607 0000 04fe 0000 0200 0000            6...........
2025-03-13 12:18:23.332964 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], ack 73, win 229, options [nop,nop,TS val 237893876 ecr 658287522], length 0
	0x0000:  4500 0034 8a46 4000 4006 4ba2 ac11 8a88  E..4.F@.@.K.....
	0x0010:  ac11 8230 deda 1f60 ec68 f2cb 4b0f d811  ...0...`.h..K...
	0x0020:  8010 00e5 6502 0000 0101 080a 0e2d f8f4  ....e........-..
	0x0030:  273c aba2                                '<..


```

- `tcpdump -XX -tttt -nn   port 8032`

```bash
2025-03-13T12:21:54 [root@x86centos7-136 /root] #  tcpdump -XX -tttt -nn   port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:22:02.223555 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966300895:3966300915, ack 1259329625, win 229, options [nop,nop,TS val 238112766 ecr 658351246], length 20
	0x0000:  e443 4bfd bc88 0016 3e85 dd8c 0800 4500  .CK.....>.....E.
	0x0010:  0048 8a49 4000 4006 4b8b ac11 8a88 ac11  .H.I@.@.K.......
	0x0020:  8230 deda 1f60 ec68 f2df 4b0f d859 8018  .0...`.h..K..Y..
	0x0030:  00e5 6516 0000 0101 080a 0e31 4ffe 273d  ..e........1O.'=
	0x0040:  a48e 1000 0000 0300 0173 656c 6563 7420  .........select.
	0x0050:  7573 6572 2829                           user()
2025-03-13 12:22:02.223885 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], seq 1:73, ack 20, win 235, options [nop,nop,TS val 658506413 ecr 238112766], length 72
	0x0000:  0016 3e85 dd8c e443 4bfd bc88 0800 4500  ..>....CK.....E.
	0x0010:  007c f814 4000 4006 dd8b ac11 8230 ac11  .|..@.@......0..
	0x0020:  8a88 1f60 deda 4b0f d859 ec68 f2f3 8018  ...`..K..Y.h....
	0x0030:  00eb d91c 0000 0101 080a 2740 02ad 0e31  ..........'@...1
	0x0040:  4ffe 0100 0001 011c 0000 0203 6465 6600  O...........def.
	0x0050:  0000 0675 7365 7228 2900 0cff 0080 0400  ...user().......
	0x0060:  00fd 0000 1f00 0014 0000 0313 726f 6f74  ............root
	0x0070:  4031 3732 2e31 372e 3133 382e 3133 3607  @172.17.138.136.
	0x0080:  0000 04fe 0000 0200 0000                 ..........
2025-03-13 12:22:02.223944 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], ack 73, win 229, options [nop,nop,TS val 238112767 ecr 658506413], length 0
	0x0000:  e443 4bfd bc88 0016 3e85 dd8c 0800 4500  .CK.....>.....E.
	0x0010:  0034 8a4a 4000 4006 4b9e ac11 8a88 ac11  .4.J@.@.K.......
	0x0020:  8230 deda 1f60 ec68 f2f3 4b0f d8a1 8010  .0...`.h..K.....
	0x0030:  00e5 6502 0000 0101 080a 0e31 4fff 2740  ..e........1O.'@
	0x0040:  02ad                                     ..

```


### 显示详细程度


| 参数   | 值 | 功能   |
|:-------|:--|:-----|
| `-v`   | 无 | 详细   |
| `-vv`  | 无 | 更详细 |
| `-vvv` | 无 | 最详细 |


- `tcpdump -A -tttt -nn port 8032`
```bash
2025-03-13T12:28:22 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:28:38.502067 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966300995:3966301015, ack 1259329985, win 229, options [nop,nop,TS val 238509045 ecr 658823514], length 20
E..H.S@.@.K........0...`.h.CK.......e......
.7[.'D.Z.......select user()
2025-03-13 12:28:38.502446 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], seq 1:73, ack 20, win 235, options [nop,nop,TS val 658902692 ecr 238509045], length 72
E..|..@.@......0.....`..K....h.W.....V.....
'F...7[...........def....user()...................root@172.17.138.136...........
2025-03-13 12:28:38.502509 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], ack 73, win 229, options [nop,nop,TS val 238509045 ecr 658902692], length 0
E..4.T@.@.K........0...`.h.WK..	....e......
.7[.'F..



```



- `tcpdump -A -tttt -nn -v   port 8032`
```bash
2025-03-13T12:25:57 [root@x86centos7-136 /root] #  tcpdump -A -tttt -nn -v   port 8032
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:26:06.678166 IP (tos 0x0, ttl 64, id 35405, offset 0, flags [DF], proto TCP (6), length 72)
    172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], cksum 0x6516 (incorrect -> 0x476a), seq 3966300935:3966300955, ack 1259329769, win 229, options [nop,nop,TS val 238357221 ecr 658714882], length 20
E..H.M@.@.K........0...`.h..K.......e......
.5
.'C1........select user()
2025-03-13 12:26:06.678514 IP (tos 0x0, ttl 64, id 63510, offset 0, flags [DF], proto TCP (6), length 124)
    172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], cksum 0x628f (correct), seq 1:73, ack 20, win 235, options [nop,nop,TS val 658750868 ecr 238357221], length 72
E..|..@.@......0.....`..K....h......b......
'C...5
...........def....user()...................root@172.17.138.136...........
2025-03-13 12:26:06.678546 IP (tos 0x0, ttl 64, id 35406, offset 0, flags [DF], proto TCP (6), length 52)
    172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], cksum 0x6502 (incorrect -> 0x110a), ack 73, win 229, options [nop,nop,TS val 238357221 ecr 658750868], length 0
E..4.N@.@.K........0...`.h..K..1....e......
.5
.'C..


```


- `tcpdump -A -tttt -nn -vv   port 8032`
```bash
2025-03-13T12:26:37 [root@x86centos7-136 /root] #  tcpdump -A -tttt -nn -vv   port 8032
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:26:48.019458 IP (tos 0x0, ttl 64, id 35407, offset 0, flags [DF], proto TCP (6), length 72)
    172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], cksum 0x6516 (incorrect -> 0x18fe), seq 3966300955:3966300975, ack 1259329841, win 229, options [nop,nop,TS val 238398562 ecr 658750868], length 20
E..H.O@.@.K........0...`.h..K..1....e......
.5.b'C.........select user()
2025-03-13 12:26:48.019943 IP (tos 0x0, ttl 64, id 63511, offset 0, flags [DF], proto TCP (6), length 124)
    172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], cksum 0x1f38 (correct), seq 1:73, ack 20, win 235, options [nop,nop,TS val 658792209 ecr 238398562], length 72
E..|..@.@......0.....`..K..1.h./.....8.....
'D_..5.b..........def....user()...................root@172.17.138.136...........
2025-03-13 12:26:48.019992 IP (tos 0x0, ttl 64, id 35408, offset 0, flags [DF], proto TCP (6), length 52)
    172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], cksum 0x6502 (incorrect -> 0xcdb1), seq 20, ack 73, win 229, options [nop,nop,TS val 238398563 ecr 658792209], length 0
E..4.P@.@.K........0...`.h./K..y....e......
.5.c'D_.


```


- `tcpdump -A -tttt -nn -vvv   port 8032`

```bash
2025-03-13T12:27:11 [root@x86centos7-136 /root] #  tcpdump -A -tttt -nn -vvv   port 8032
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:27:19.324573 IP (tos 0x0, ttl 64, id 35409, offset 0, flags [DF], proto TCP (6), length 72)
    172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], cksum 0x6516 (incorrect -> 0xfcda), seq 3966300975:3966300995, ack 1259329913, win 229, options [nop,nop,TS val 238429867 ecr 658792209], length 20
E..H.Q@.@.K........0...`.h./K..y....e......
.6&.'D_........select user()
2025-03-13 12:27:19.324933 IP (tos 0x0, ttl 64, id 63512, offset 0, flags [DF], proto TCP (6), length 124)
    172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], cksum 0x2a49 (correct), seq 1:73, ack 20, win 235, options [nop,nop,TS val 658823514 ecr 238429867], length 72
E..|..@.@......0.....`..K..y.h.C....*I.....
'D.Z.6&...........def....user()...................root@172.17.138.136...........
2025-03-13 12:27:19.324999 IP (tos 0x0, ttl 64, id 35410, offset 0, flags [DF], proto TCP (6), length 52)
    172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], cksum 0x6502 (incorrect -> 0xd8c2), seq 20, ack 73, win 229, options [nop,nop,TS val 238429868 ecr 658823514], length 0
E..4.R@.@.K........0...`.h.CK.......e......
.6&.'D.Z


```



### 指定每一个包捕获的长度


| 参数 | 值     | 功能                                          |
|:-----|:-------|:--------------------------------------------|
| `-s` | `0`    | 捕获整个包的内容，适合需要获取最详细的真实数据 |
| `-s` | `1024` | 只监听eth0网卡接口                            |
| `-s` | `192`  | 查看可用的网络接口列表                        |




```bash
2025-03-13T12:36:05 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn -s 50  port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 50 bytes
2025-03-13 12:36:10.499849 IP 172.17.138.136.57050 > 172.17.130.48.8032: [|tcp]
E..W..@.@.J........0...`.h.7K!.6....
2025-03-13 12:36:10.500765 IP 172.17.130.48.8032 > 172.17.138.136.57050: [|tcp]
E....Z@.@......0.....`..K!.6.h.Z....
2025-03-13 12:36:10.500803 IP 172.17.130.48.8032 > 172.17.138.136.57050: [|tcp]
E."$.[@.@......0.....`..K!...h.Z....
2025-03-13 12:36:10.500827 IP 172.17.130.48.8032 > 172.17.138.136.57050: [|tcp]
E..,.a@.@......0.....`..K!'..h.Z....
2025-03-13 12:36:10.500837 IP 172.17.138.136.57050 > 172.17.130.48.8032: [|tcp]
E..4..@.@.K........0...`.h.ZK!......
2025-03-13 12:36:10.500892 IP 172.17.138.136.57050 > 172.17.130.48.8032: [|tcp]
E..4..@.@.K
.......0...`.h.ZK!'.....
2025-03-13 12:36:10.500919 IP 172.17.138.136.57050 > 172.17.130.48.8032: [|tcp]
E..4..@.@.K	.......0...`.h.ZK!8.....
2025-03-13 12:36:10.500973 IP 172.17.130.48.8032 > 172.17.138.136.57050: [|tcp]
E....d@.@......0.....`..K!8..h.Z....
2025-03-13 12:36:10.500993 IP 172.17.138.136.57050 > 172.17.130.48.8032: [|tcp]
E..4..@.@.K........0...`.h.ZK!@6...-
2025-03-13 12:36:10.501064 IP 172.17.130.48.8032 > 172.17.138.136.57050: [|tcp]
E....f@.@......0.....`..K!@6.h.Z....
2025-03-13 12:36:10.501078 IP 172.17.138.136.57050 > 172.17.130.48.8032: [|tcp]
E..4..@.@.K........0...`.h.ZK!E...."

```


```bash
2025-03-13T12:36:50 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn -s 0  port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:36:58.689688 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966301274:3966301309, ack 1261576290, win 3118, options [nop,nop,TS val 239009232 ecr 659354698], length 35
E..W..@.@.J........0...`.h.ZK2 b....e%.....
.>..'L.J.......select * from t1 limit 10000
2025-03-13 12:36:58.690586 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [.], seq 1:15929, ack 35, win 235, options [nop,nop,TS val 659402880 ecr 239009232], length 15928
E.>l.n@.@..A...0.....`..K2 b.h.}.....:.....
'M...>.......#....def.apple.t1.t1.id.id.?.......B...!....def.apple.t1.t1.a.a.?......	@...!....def.apple.t1.t1.b.b.?.......@...!....def.apple.t1.t1.c.c.............!....def.apple.t1.t1.d.d.............h....6
2003588754
1714682759&fugiat tenetur nemo dicta repellendus.(dolores ex consequatur voluptates earum.L....7
1898674299
1698116023.sed fugit porro qui eos..quis rerum ut nostrum qui.h..	.8
1310715836	472875023.quae aut illo quia.<sint occaecati excepturi odit mollitia repellendus voluptateB..
.9
1153628287	525456967.ut et quia officiis..laborum eius aut sed.j....11
1252007743	393713658"quibusdam qui soluta enim debitis..consequatur aliquam excepturi fugiat est modi!m....13
2011751560	6018940445ullam nulla aut dolorum aut exercitationem molestiae..nihil ut ad et dicta quae qui......16
1101884996	681324844:officiis dignissimos eveniet eum harum eligendi eum animi./sint consequatur hic reiciendis quisquam ullam.r....18
1406095383	2112877817reprehenderit delectus nihil ex eaque alias recusandae.!ut iure cupiditate sit provident.x....19
1327128558.63469099#ut illum sit qui dolores assumenda!<quis aliquam animi praesentium id corrupti dolores assumenda.....21
1808606693	461836962;voluptatibus sunt repudiandae rerum quaerat eius modi ipsa.3est est perspiciatis minima voluptatem error alias.T....23
1777484703	466466395.quia et ad aut est fugiat. velit aspernatur odit accusamus.s....26
1404551453	607025100.esse reprehenderit voluptatem.;delectus nobis qui exercitationem voluptatem reprehenderit.h....28	950391508.54391662(rerum ut animi consequatur ex provident!(consequatur repellat accusantium facere.{....29
1579771261	221448712&delectus facilis est sit quod in sunt.;fugiat dolore quibusdam veritatis cum reprehenderit ratione`....31
1165586830	472855020%aut neque magnam numquam dolorem qui!!molestias neque ad vero incidunt.M....38
2131804237
1085387974 quas qui ab molestias rerum est..facere aut et eum.V....39
1559885358	657598532 assumenda rem molestias et enim..qui quo doloremque ea autem!.....41
1580698885	6817852124consequuntur voluptas voluptatem voluptatem maiores.<molestias doloribus quidem velit tenetur praesentium quia eoZ....44
1134554347	295498719.nulla velit laborum dolor.&consequuntur distinctio consequatur a.h....45	431367440	363238924)alias velit excepturi molestiae voluptas.&ullam nisi quod expedita ducimus sint.i....47
2058649460
1044508113-voluptate sunt doloribus laudantium vitae ex.!non soluta placeat veritatis sed.x....50
1784021583	284706965,id possimus et architecto est ipsum aut qui.2et consequatur enim tempora alias deserunt magnam.m....51
1838438141
1488555335&eum voluptatibus nulla omnis voluptas.,saepe tenetur deserunt aliquam nesciunt nam.s....52
1451864737	772003953.illum veritatis qui ea illum.<dicta molestias provident laboriosam ea officiis deleniti eo`....54
1801623328
1089786999,veniam cum laborum ipsam doloribus voluptas..voluptate velit voluptas.m.. .57
1643677817
1454894314<saepe consequatur adipisci explicabo velit excepturi quia en.qui nobis perferendis.b..!.58
1874543227	1589569307voluptatem et omnis dolorem accusamus voluptatem velit..quia ducimus sit!}..".59
1800902219
1091213662.omnis delectus quasi ipsum rem eum et tempore.4aut et commodi repellendus repellendus amet facilis....#.60
1672640316
1442092269+quo dignissimos excepturi veritatis fugiat.:sequi voluptas asperiores cupiditate quos accusamus minus....$.61
1066193538	640824809<quia unde iusto architecto animi impedit doloremque dolores!7tempora eos sit alias aliquid accusantium laborum quis._..%.62
2030791338
1732011009"pariatur debitis consectetur enim."iste sequi aperiam recusandae aut....&.63
1138469771	760920572:accusantium dolores est aperiam laborum reiciendis dolore.6eos omnis sed doloremque occaecati officiis aut rerum.I..'.66	959226029	718988846.eos alias et.#nobis quaerat qui debitis ut optio.t..(.69
2145923559	7756993169recusandae occaecati tenetur similique et necessitatibus.!et at esse culpa blanditiis sint.V..).71
1752971302.59797143!eum soluta officiis vero impedit..et et corporis id quia quia.^..*.73	915259646	843778569.est unde earum sint.1nihil velit nesciunt aut nostrum aut animi velit.\..+.75
1751197889
1648444437,molestias eum enim accusamus pariatur earum..dolores qui aut odio....,.76
2101120581
1649846608<praesentium rem dolor sint qui quibusdam voluptas delectus q)laudantium corrupti corrupti et nesciunt.q..-.77
1804405168
1363094347.aut error ullam voluptates.;expedita aliquam quisquam omnis impedit quisquam asperioresu....79
1988847298
1275372393.eos laudantium blanditiis aut.<laborum a aspernatur quo veniam tempora repudiandae qui poss.../.81
1460964521	399822228-praesentium ipsum et repellat voluptas rerum.<cupiditate quia corporis placeat nihil incidunt excepturi vo}..0.85
1690693294	402527489.ut saepe optio ea expedita qui ut ut ut iusto.5ipsa quidem architecto occaecati architecto ut dolor....1.86
1938402883
1867683110<qui et suscipit explicabo voluptatem doloribus aperiam porro0quisquam impedit voluptas officia error officia.R..2.87	985666107	210541711&voluptatem quis praesentium qui iusto..ad et minima eaque....3.88
1875752492	720666135/quas tempore cumque consequuntur est provident.<voluptas voluptatem ut omnis cumque commodi tenetur dolores.L..4.92
1046709360	364531149.neque aut dolorem voluptas..et eius inventore sint....5.93
1929299726
1102854722<sapiente non perspiciatis molestias sequi est explicabo adip,est ut quia et quia esse ea id suscipit est.p..6.96
1979400899.49908375.explicabo possimus hic sed.<quia velit amet consectetur magni sed voluptas porro perspicX..7.101
2058690815
1164866466.ut nostrum et mollitia ad modi..illo autem mollitia possimus.o..8.102
1765812615	584068030$dolorem sit quia corrupti sit autem.0ratione consequuntur quasi impedit enim tempora.l..9.103
1160495982	612633379,illum quia perspiciatis excepturi occaecati.%aliquid autem aut maxime dignissimos.V..:.104	533636418.63711580sunt sapiente qui a tenetur tenetur cumque illo..quae quasi ex.\..;.105	398769330	238269571.qui et quisquam ut quia quis.%numquam quia doloribus voluptas quia.V..<.106
1207213121
1154199624#tempore qui dolore magni atque vel..iusto error qui soluta!...=.108
2050374349	785693409<neque voluptate repudiandae quod ut sit nostrum qui sint inv,debitis ipsam quo non dolorem nostrum fugit.`..>.109	262733139	251487199&nostrum inventore est hic et dolor ut. et placeat quae atque accusamus....?.110
1508290664	2668815611quia vero aut totam consequatur ipsum doloremque.<pariatur cupiditate perspiciatis magnam cumque ut voluptatemf..@.113
1057706898	389385606,repudiandae recusandae corporis consequatur..aperiam est aspernatur a ullam.f..A.116
1051197483
10026896993velit sunt eum quis consequatur voluptatem qui quo..eum sed odio omnis qui.L..B.117
1451211338
1416738403.totam dolore totam est velit!.et quis quia fugit.p..C.122
1420540344	844906388&ex quia iusto explicabo cupiditate ut./delectus provident aut eum quia occaecati quia.e..D.126
1993206572
1570489921.et impedit nam quo delectus.-libero at et commodi aspernatur et sunt sunt....E.128	694812007	569617170;voluptas consequatur repellat beatae dolor debitis qui sed.+sit quaerat ratione necessitatibus facilis.W..F.129
1558353483	957579668.ut laborum suscipit sed.$omnis ut distinctio ullam molestias.v..G.131	585432533.42032061*magni in dolor quia praesentium sit harum!3aut voluptas aperiam sit id dolor eum perspiciatis.N..H.133
1756643709
1261459357.non quaerat dolor harum..porro recusandae quae rem.~..I.134	973664476	864671632<sapiente omnis illum qui aut ut quaerat necessitatibus vero.(qui libero molestiae est occaecati iste.]..J.137	461649890.89619879%reprehenderit autem omnis voluptatem..ea adipisci ut vel consectetur.l..K.140
1196477269	502769231.quia illo recusandae.<esse corrupti molestiae aut ducimus qui molestias recusandaeT..L.141
2076588933
1275990964,inventore ea voluptas consequatur nobis aut..qui sed nam....M.142	964748188	962715668<corrupti velit vero non minus ipsum aspernatur doloremque co1quo et tempore repudiandae consequatur nemo iste.i..N.143
2010474512
1899398968<beatae molestias voluptatem distinctio incidunt voluptas vol.et ut facere vel....O.144
1321549041	704736397<enim incidunt ullam nemo voluptatem voluptas necessitatibus 2accusamus eligendi magnam ipsam magnam ex aliquam.S..P.145
1215402978	195539887"possimus voluptatem quos ut error..in qui ipsum animi et....Q.147	617394488	372883829.asperiores ut non rerum corporis veniam autem.9ipsa perspiciatis quis maiores illum nesciunt reiciendis!w..R.149	625888470	576004563!consequatur doloribus et commodi.<sapiente saepe perferendis iste incidunt omnis et voluptatemz..S.150
1422307419	593397986#exercitationem totam in rem minima.<quibusdam incidunt consequatur enim praesentium laborum doloh..T.151	929420453	829905565'aut qui velit voluptatum aut quibusdam.'perferendis quae facilis sunt voluptas.x..U.154
1123891823	536501231<voluptas dicta deleniti quo laborum eos suscipit error exerc!enim facere culpa atque animi in.|..V.155
1690607303	249619147<a illo voluptatem consequatur occaecati distinctio eveniet r%nihil et aut dignissimos repudiandae.p..W.156
2078860412	443505184&dolore ex facilis iure magnam et enim./facere cupiditate placeat totam sit ipsum ipsa.^..X.157
1245352695	394912528%consequatur sunt totam sunt quisquam..quibusdam quia ut praesentium.m..Y.158	884057050	412312998)ut perferendis alias aut totam omnis non!*autem est atque corporis minus sequi quam!v..Z.159	931240011	765080581*maiores ut qui odio explicabo praesentium.2incidunt dolores fugit ratione non quia sit totam.}..[.163
1631026724
1107291152;vitae magnam sit odio harum rerum et aut ducimus illum eum.&non a temporibus dignissimos eum quis.p..\.164	959694642	785730686.culpa assumenda natus est!<harum est et placeat dolores officia delectus est quos quos!v..].165	872206278.914353744sequi in facere at et sapiente minus sequi quo quis!)placeat earum id autem praesentium fugit....^.167
2017062345	4226584250enim necessitatibus in expedita quisquam maxime.6itaque qui consectetur maiores officiis deleniti quis.o.._.169
2023233523
1311942311-beatae maiores nulla illo reiciendis aliquid.&qui nobis sed molestias quo aut ipsam!n..`.170	479198514.865489348molestiae laboriosam veritatis ducimus laborum in ipsam!.enim animi neque est officia.d..a.171	510988487	1670601022repellat aperiam tenetur alias ipsa reprehenderit..voluptas id ducimus sit._..b.174	670794604	388415451!et quisquam itaque provident aut!$quam maiores maiores et quae non ad.s..c.175
2069737752
1258514903<adipisci sit provident neque delectus ratione atque dolorum..qui earum id soluta sit ut.e..d.177
1107204556	6447584416impedit distinctio repellendus molestias ratione iste..ut enim quas itaque.e..e.182
1459991242
12128376150nobis perferendis accusantium perspiciatis illo..et eligendi quas et nisi....f.184
1891394577	683114058<voluptatem aperiam laudantium laudantium molestiae molestias,itaque et assumenda maiores fugiat et neque!l..g.185
1330735975	713361001)voluptates qui voluptas voluptatem ullam.(nobis voluptatem enim veniam voluptatum.R..h.186
1982917631
1788149495.repellat architecto atque..quo qui similique inventore.{..i.188
1550033064
10391685189repellat natus quos cumque corporis commodi consequuntur!&et blanditiis doloremque quis ut quae.y..j.189
2011431886.558000429occaecati ducimus porro libero ipsam molestias vel neque.&quia aut aspernatur inventore et modi.u..k.190
2011933767	9797658608pariatur doloribus molestiae et saepe possimus quas est."laborum nulla sed et sint tempore.v..l.192
1620332406	300261875+repudiandae facere non asperiores eligendi.0velit ullam magni qui nisi consequuntur impedit.M..m.193	542662142	124461732.non eveniet optio et!.aliquid ipsam optio accusamus....n.194	551804373	302201180<illum delectus voluptas similique eum atque excepturi et ame-itaque dolorem dolorem nobis aliquid tempora.}..o.196
1401638410	142652402/optio eligendi magni sapiente atque a delectus.3debitis inventore aspernatur sint vel repellat hic.e..p.198
1360205808	535525181.provident cum rerum aut!2dignissimos ipsam atque ratione et minima dolorem.`..q.200
2111934700	531569080.eaque placeat voluptatem qui.(exercitationem natus quo eos reiciendis.Q..r.201	654648756	324252914)recusandae deleniti dolore neque quas et..quae minus id.X..s.203
1154086856	273478870"aliquam sint ea amet omnis veniam..nihil consequatur rem enim.d..t.207
2044131822	766829257.eos a alias vel id sit.2ut dolorum et voluptatem illo tenetur ut incidunt!f..u.208
1987718992	475055909 doloremque unde porro sunt modi.+inventore nesciunt ut sint sed deleniti et.Z..v.211
1552064980
1122059789.unde numquam ullam deleniti."omnis fugit ut vero iste adipisci.f..w.213
1808357109
1443281264%exercitationem illum sint esse omnis.%sequi et quae autem beatae error rem.u..x.214	657572976	542778273.rerum ut debitis unde dicta et.<architecto quasi facere non recusandae consectetur quo numquD..y.215
1646288419
1645832522.ab sunt id nulla beatae..eum qui maiores.W..z.217
2068453306	410353234&quia est optio deserunt voluptates ut!.in atque eligendi sed.P..{.218	740680075	190563579.qui asperiores error voluptas..omnis animi ut alias ut.o..|.219
2000059659
1263494898#et modi sint quae blanditiis dolor.0inventore voluptas hic nisi itaque sapiente sit.O..}.220
1852648197	980905093.esse consequuntur veniam quis..voluptatem odio rerum.V..~.221
1599109092	436970564.eius ad est sed reprehenderit..et est molestias consectetur!t....222
1929098728
1121612068,exercitationem consequatur perferendis quas.,beatae reprehenderit mollitia recusandae ut.L....223
1965305195
1032218229.harum ratione est impedit..at saepe ducimus quos......224
1673376235
1515194145)blanditiis possimus veritatis beatae eum!<unde necessitatibus alias cumque quia eligendi ipsum sint pa.....225
1482144184	5355656113quo magni voluptatum eaque voluptates consequuntur.8dolor provident consectetur at facilis officiis tempore.b....226	930466389	1578042810possimus ut amet repudiandae omnis consequuntur..temporibus aut possimus.f....228
1037848256	4514575766enim quia consequatur accusamus aut perspiciatis unde..vero qui qui qui est.F....234	916050347	129504740.ab tempora omnis..ratione unde modi cum odio.b....236	262739529.1831057 sed officiis non nihil quisquam.*consectetur quam nesciunt dolore sint est!b....239	495667550	363099540'qui accusamus qui provident cupiditate.!veritatis assumenda qui ullam et.h....240
2112831340	583744287.unde qui est omnis dolor.4consequatur ratione minima architecto occaecati qui.N....241	706225251	342100768#tenetur aut dolorem corporis nobis..tenetur quasi ut.A....244
1956267418
1083295748.ut ut omnis..rerum ut vel minus eaque.u....245
1632710583	625537546*voluptas architecto debitis earum dolorem.0voluptatem modi recusandae eos quas quae cumque.}....246
1397035167	4842992669rerum pariatur sunt incidunt doloribus voluptatem itaque.)animi quis non natus omnis est quia quos.^....248
1042143012
1036069605)explicabo at quis voluptates hic debitis!.quas facere omnis libero!.....250	775707896	6618158708autem doloremque ut iste voluptatem ratione dicta nihil.<laudantium dolores et pariatur accusamus assumenda velit nih.....251
1280903405	459701685<pariatur voluptate molestias quidem laudantium rerum eligend0id qui maiores adipisci et odit quia asperiores.|....252
2094282986	7326038270enim recusandae consequuntur commodi voluptatum.1magnam optio cumque commodi est voluptates rerum.Z....253
1841001036	849437271.nesciunt distinctio atque.%est sequi eum odio sit sint sed sunt.o....254
1751642770.12946130.itaque aspernatur vel sed.;consequuntur voluptatem voluptatem similique et non ut sit......257	208116252	175443854<voluptate repudiandae aut ea quia dolorem voluptates molesti/voluptas magnam qui iusto id delectus voluptas.l....258
1718234877	3894126195ut in iusto autem quae est officia amet occaecati et..incidunt velit est quisquam.j....261
17445703



```








### 抓包结束

| 参数 | 值     | 功能                     |
|:-----|:-------|:-----------------------|
| `-c` | `1000` | 只读取1000个数据包就停止 |
| 无   | 无     | 永不停止，除非`ctrl+C `   |


### 序列号显示


| 参数 | 值 | 功能           |
|:-----|:--|:-------------|
| `-S` | 无 | 绝对序列号显示 |
| 无   | 无 | 相对序列号显示 |

```bash
2025-03-13T12:41:12 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn -s 0  port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:41:21.167554 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966301327:3966301345, ack 1262698757, win 3118, options [nop,nop,TS val 239271710 ecr 659637391], length 18
E..F.M@.@.J........0...`.h..KCA.....e......
.B..'QD........show tables
2025-03-13 12:41:21.169148 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], seq 1:120, ack 18, win 235, options [nop,nop,TS val 659665359 ecr 239271710], length 119
E.....@.@......0.....`..KCA..h.......g.....
'Q...B.......:....def..TABLES..Tables_in_apple.Tables_in_apple..................sz_promotion_tree.....sz_user.....t1...........
2025-03-13 12:41:21.169253 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], ack 120, win 3118, options [nop,nop,TS val 239271712 ecr 659665359], length 0
E..4.N@.@.J........0...`.h..KCA|....e......
.B. 'Q..


```


```bash
2025-03-13T12:40:34 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn -S -s 0  port 8032
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:40:53.199664 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [P.], seq 3966301309:3966301327, ack 1262698638, win 3118, options [nop,nop,TS val 239243742 ecr 659402887], length 18
E..F.K@.@.J........0...`.h.}KC@.....e......
.B..'M.........show tables
2025-03-13 12:40:53.201739 IP 172.17.130.48.8032 > 172.17.138.136.57050: Flags [P.], seq 1262698638:1262698757, ack 3966301327, win 235, options [nop,nop,TS val 659637391 ecr 239243742], length 119
E.....@.@......0.....`..KC@..h.......p.....
'QD..B.......:....def..TABLES..Tables_in_apple.Tables_in_apple..................sz_promotion_tree.....sz_user.....t1...........
2025-03-13 12:40:53.201853 IP 172.17.138.136.57050 > 172.17.130.48.8032: Flags [.], ack 1262698757, win 3118, options [nop,nop,TS val 239243745 ecr 659637391], length 0
E..4.L@.@.J........0...`.h..KCA.....e......
.B..'QD.

```







### 网卡

限制网卡

| 参数 | 值     | 功能                                      |
|:-----|:-------|:----------------------------------------|
| `-i` | `any`  | 监听所有的网卡接口，用来查看是否有网络流量 |
| `-i` | `eth0` | 只监听eth0网卡接口                        |
| `-D` | 无     | 查看可用的网络接口列表                    |


```bash
2025-03-13T11:59:27 [root@RDS-146 /root] # tcpdump -D
1.eth0
2.virbr0
3.docker0
4.nflog (Linux netfilter log (NFLOG) interface)
5.nfqueue (Linux netfilter queue (NFQUEUE) interface)
6.usbmon1 (USB bus number 1)
7.vethcd2f3ea
8.usbmon2 (USB bus number 2)
9.veth8b1c26e
10.vethb48e25c
11.veth95cde42
12.vethe310fd5
13.vethd609c6c
14.veth971bea4
15.vethc48191e
16.vethc239259
17.any (Pseudo-device that captures on all interfaces)
18.lo [Loopback]
2025-03-13T12:04:24 [root@RDS-146 /root] #

```


### 是否解析主机名和端口号


| 参数  | 值 | 功能                     |
|:------|:--|:-----------------------|
| `-n`  | 无 | 不要解析主机名           |
| `-nn` | 无 | 不要解析主机名或者端口名 |
| `-N`  | 无 | 不打印出host的域名部分   |



#### 时间戳



| 参数    | 值 | 功能                                   |
|:--------|:--|:-------------------------------------|
| `-t`    | 无 | 在每行的输出中不输出时间               |
| `-tt`   | 无 | 在每行的输出中会输出时间戳             |
| `-ttt`  | 无 | 输出每两行打印的时间间隔(以毫秒为单位) |
| `-tttt` | 无 | 在每行打印的时间戳之前添加日期的打印   |


### 基于协议过滤

protocol 可选值：ip, ip6, arp, rarp, atalk, aarp, decnet, sca, lat, mopdl, moprc, iso, stp, ipx, or netbeui

| 参数   | 值 | 功能           |
|:-------|:--|:---------------|
| `tcp`  | 无 |                |
| `udp`  | 无 |                |
| `icmp` | 无 |                |
| `arp`  | 无 |                |
| `ip6`  | 无 | 只显示IPV6流量 |

ip 根据版本的不同，可以再细分为 IPv4 和 IPv6 两种，如果你只指定了 tcp，这两种其实都会包含在内。






### 基于ip过滤：host



| 参数      | 值                | 功能                              |
|:----------|:------------------|:--------------------------------|
| `host`    | `10.10.10.10`     | 源ip或目的ip为`10.10.10.10`       |
| `src`     | `10.10.10.10`     | 源ip为`10.10.10.10`               |
| `dst`     | `10.10.10.10`     | 目的ip为`10.10.10.10`             |
| `net`     | `192.168.10.0/24` | 源网段或目的网段`192.168.10.0/24` |
| `src net` | `192.168.10.0/24` | 源网段`192.168.10.0/24`           |
| `dst net` | `192.168.10.0/24` | 目的网段`192.168.10.0/24`         |


- `tcpdump host 10.10.10.10`：只查看来自或者发送到IP地址`10.10.10.10`的流量





### 基于端口进行过滤：port


| 参数            | 值             | 功能                                |
|:----------------|:---------------|:----------------------------------|
| `port`          | `3306`         | 源端口或目的端口为`3306`            |
| `port`          | `3306 or 3307` | 源端口或目的端口为`3306`或`3307`    |
| `src port`      | `3306`         | 源端口为`3306`                      |
| `dst port`      | `3306`         | 目的端口为`3306`                    |
| `portrange`     | `3306-3309`    | 源端口范围或目的端口范围`3306-3309` |
| `src portrange` | `3306-3309`    | 源端口范围`3306-3309`               |
| `dst portrange` | `3306-3309`    | 目的端口范围`3306-3309`             |




### 基于包的大小过滤流量


| 参数      | 值    | 功能 |
|:----------|:------|:-----|
| `less`    | `32`  |      |
| `greater` | `64`  |      |
| `<=`      | `128` |      |












### 分析包内容：-r

```bash
2025-03-13T11:32:13 [root@x86centos7-136 /root] # tcpdump tcp -r mysql_8032.pcap
reading from file mysql_8032.pcap, link-type EN10MB (Ethernet)
08:59:58.569882 IP x86centos7-136.43238 > 172.17.130.48.pro-ed: Flags [S], seq 1129028557, win 29200, options [mss 1460,sackOK,TS val 225989113 ecr 0,nop,wscale 7], length 0
08:59:58.570025 IP 172.17.130.48.pro-ed > x86centos7-136.43238: Flags [S.], seq 892056628, ack 1129028558, win 28960, options [mss 1460,sackOK,TS val 646382760 ecr 225989113,nop,wscale 7], length 0
08:59:58.570221 IP x86centos7-136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 1, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 0
08:59:58.570589 IP 172.17.130.48.pro-ed > x86centos7-136.43238: Flags [P.], seq 1:79, ack 1, win 227, options [nop,nop,TS val 646382760 ecr 225989113], length 78
08:59:58.570661 IP x86centos7-136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 79, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 0
08:59:58.570801 IP x86centos7-136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 1:198, ack 79, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 197
08:59:58.570887 IP 172.17.130.48.pro-ed > x86centos7-136.43238: Flags [.], ack 198, win 235, options [nop,nop,TS val 646382760 ecr 225989113], length 0
08:59:58.571002 IP 172.17.130.48.pro-ed > x86centos7-136.43238: Flags [P.], seq 79:90, ack 198, win 235, options [nop,nop,TS val 646382761 ecr 225989113], length 11
08:59:58.571140 IP x86centos7-136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 198:235, ack 90, win 229, options [nop,nop,TS val 225989114 ecr 646382761], length 37

```



#### 不显示主机名而只显示IP地址
```bash
2025-03-13T11:35:12 [root@x86centos7-136 /root] # tcpdump -n  tcp -r mysql_8032.pcap
reading from file mysql_8032.pcap, link-type EN10MB (Ethernet)
08:59:58.569882 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [S], seq 1129028557, win 29200, options [mss 1460,sackOK,TS val 225989113 ecr 0,nop,wscale 7], length 0
08:59:58.570025 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [S.], seq 892056628, ack 1129028558, win 28960, options [mss 1460,sackOK,TS val 646382760 ecr 225989113,nop,wscale 7], length 0
08:59:58.570221 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 1, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 0
08:59:58.570589 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [P.], seq 1:79, ack 1, win 227, options [nop,nop,TS val 646382760 ecr 225989113], length 78
08:59:58.570661 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 79, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 0
08:59:58.570801 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 1:198, ack 79, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 197
08:59:58.570887 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [.], ack 198, win 235, options [nop,nop,TS val 646382760 ecr 225989113], length 0


```

#### 显示时间
```bash
2025-03-13T11:37:24 [root@x86centos7-136 /root] # tcpdump -n -tttt  tcp -r mysql_8032.pcap
reading from file mysql_8032.pcap, link-type EN10MB (Ethernet)
2025-03-13 08:59:58.569882 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [S], seq 1129028557, win 29200, options [mss 1460,sackOK,TS val 225989113 ecr 0,nop,wscale 7], length 0
2025-03-13 08:59:58.570025 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [S.], seq 892056628, ack 1129028558, win 28960, options [mss 1460,sackOK,TS val 646382760 ecr 225989113,nop,wscale 7], length 0
2025-03-13 08:59:58.570221 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 1, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 0
2025-03-13 08:59:58.570589 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [P.], seq 1:79, ack 1, win 227, options [nop,nop,TS val 646382760 ecr 225989113], length 78
2025-03-13 08:59:58.570661 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 79, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 0
2025-03-13 08:59:58.570801 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 1:198, ack 79, win 229, options [nop,nop,TS val 225989113 ecr 646382760], length 197
2025-03-13 08:59:58.570887 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [.], ack 198, win 235, options [nop,nop,TS val 646382760 ecr 225989113], length 0
2025-03-13 08:59:58.571002 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [P.], seq 79:90, ack 198, win 235, options [nop,nop,TS val 646382761 ecr 225989113], length 11
2025-03-13 08:59:58.571140 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 198:235, ack 90, win 229, options [nop,nop,TS val 225989114 ecr 646382761], length 37
2025-03-13 08:59:58.571371 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [P.], seq 90:182, ack 235, win 235, options [nop,nop,TS val 646382761 ecr 225989114], length 92
2025-03-13 08:59:58.573565 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 235:253, ack 182, win 229, options [nop,nop,TS val 225989116 ecr 646382761], length 18
2025-03-13 08:59:58.573728 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [P.], seq 182:254, ack 253, win 235, options [nop,nop,TS val 646382763 ecr 225989116], length 72
2025-03-13 08:59:58.612955 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 254, win 229, options [nop,nop,TS val 225989156 ecr 646382763], length 0
2025-03-13 09:00:05.171761 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [P.], seq 253:275, ack 254, win 229, options [nop,nop,TS val 225995714 ecr 646382763], length 22
2025-03-13 09:00:05.172105 IP 172.17.130.48.pro-ed > 172.17.138.136.43238: Flags [P.], seq 254:311, ack 275, win 235, options [nop,nop,TS val 646389362 ecr 225995714], length 57
2025-03-13 09:00:05.172141 IP 172.17.138.136.43238 > 172.17.130.48.pro-ed: Flags [.], ack 311, win 229, options [nop,nop,TS val 225995715 ecr 646389362], length 0

```







## 案例

当你在使用多个过滤器进行组合时，有可能需要用到括号，而括号在 shell 中是特殊符号，因为你需要使用引号将其包含


### 指定源ip、目的ip、目的端口

- `tcpdump -A -tttt -nn -s 0  'src 172.17.138.136 and dst port 16315 and dst 172.17.140.189'`

```bash
2025-03-13T12:54:41 [root@x86centos7-136 /root] # tcpdump -A -tttt -nn -s 0  'src 172.17.138.136 and dst port 16315 and dst 172.17.140.189'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2025-03-13 12:54:56.938800 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [S], seq 3333701992, win 29200, options [mss 1460,sackOK,TS val 240087481 ecr 0,nop,wscale 7], length 0
E..<0.@.@.............?...Ah......r.o..........
.Oq.........
2025-03-13 12:54:56.939006 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [.], ack 1392029488, win 229, options [nop,nop,TS val 240087482 ecr 240076120], length 0
E..40.@.@.............?...AiR..0....o......
.Oq..OEX
2025-03-13 12:54:56.939284 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [.], ack 79, win 229, options [nop,nop,TS val 240087482 ecr 240076121], length 0
E..40.@.@.............?...AiR..~....o......
.Oq..OEY
2025-03-13 12:54:56.939649 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [P.], seq 0:115, ack 79, win 229, options [nop,nop,TS val 240087482 ecr 240076121], length 115
E...0.@.@.............?...AiR..~....p......
.Oq..OEYo...................................dbscale_internal..8.+%.l.v.|...@.[....information_schema.mysql_native_password.
2025-03-13 12:54:56.939884 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [P.], seq 115:547, ack 90, win 229, options [nop,nop,TS val 240087483 ecr 240076121], length 432
E...0.@.@.............?...A.R.......q?.....
.Oq..OEY.....SELECT @@AUTO_INCREMENT_INCREMENT,@@AUTO_INCREMENT_OFFSET,@@CHARACTER_SET_CLIENT,@@CHARACTER_SET_CONNECTION,@@CHARACTER_SET_DATABASE,@@CHARACTER_SET_RESULTS,@@CHARACTER_SET_SERVER,@@FOREIGN_KEY_CHECKS,@@INNODB_LOCK_WAIT_TIMEOUT,@@LOCK_WAIT_TIMEOUT,@@MAX_SORT_LENGTH,@@NET_READ_TIMEOUT,@@NET_WRITE_TIMEOUT,@@SQL_LOG_BIN,@@SQL_MODE,@@SQL_SAFE_UPDATES,@@SQL_SELECT_LIMIT,@@TIME_ZONE,@@TRANSACTION_ISOLATION,@@TRANSACTION_READ_ONLY
2025-03-13 12:54:56.940361 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [P.], seq 547:571, ack 1270, win 247, options [nop,nop,TS val 240087483 ecr 240076121], length 24
E..L0.@.@..m..........?...C.R..%....o......
.Oq..OEY.....SELECT CURRENT_USER
2025-03-13 12:54:56.940617 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [P.], seq 571:576, ack 1354, win 247, options [nop,nop,TS val 240087483 ecr 240076122], length 5
E..90.@.@.............?...C.R..y....o......
.Oq..OEZ.....
2025-03-13 12:54:56.940673 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [F.], seq 576, ack 1354, win 247, options [nop,nop,TS val 240087483 ecr 240076122], length 0
E..40.@.@.............?...C.R..y....o......
.Oq..OEZ
2025-03-13 12:54:56.940742 IP 172.17.138.136.53208 > 172.17.140.189.16315: Flags [.], ack 1355, win 247, options [nop,nop,TS val 240087483 ecr 240076122], length 0
E..40.@.@.............?...C.R..z....o......
.Oq..OEZ
2025-03-13 12:54:56.946262 IP 172.17.138.136.53210 > 172.17.140.189.16315: Flags [S], seq 3731645164, win 29200, options [mss 1460,sackOK,TS val 240087489 ecr 0,nop,wscale 7], length 0
E..<.-@.@..&..........?..lb.......r.o..........
.Oq.........
2025-03-13 12:54:56.946393 IP 172.17.138.136.53210 > 172.17.140.189.16315: Flags [.], ack 759922993, win 229, options [nop,nop,TS val 240087489 ecr 240076128], length 0
E..4..@.@..-..........?..lb.-K.1....o......
.Oq..OE`


```




### `tcpdump 'src 10.0.2.3 and (dst port 3389 or 22)'`





#### 抓包多个端口

```bash
tcpdump -i any tcp and port '(16315 or 16310)' -vv -w 16315_16310.cap

```




















