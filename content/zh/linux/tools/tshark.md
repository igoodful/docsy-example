---
title: tshark
description: tshark
date: 2023-10-30
weight: 30000
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
先用 tcpdump 或 tshark 在目标服务器上抓包生成 pcap 文件，再将其拿到装有 Wireshark 的主机上进行分析
{{< /alert >}}


{{<note>}}
<!---->


- -f 抓包前过滤，过滤特定协议、端口、源ip、目的ip。这个是在抓包的过程中过滤，如果是分析本地文件则用不到
   - 协议名可以为： tcp, udp, dns, icmp, http等
   - port 3306 ：指定目标端口：比如本地端口：src port 3306  比如目标端口：dst port 3306 本地端口和目标端口都包含：port 3306
   - src host 172.17.134.77 指定源ip，也就是数据包发送端
   - dst host 172.17.134.76 指定目标ip，也就是数据包接受端
   - 可以使用and和or，比如：-f "(tcp port 80 or tcp port 443) and host 172.17.134.77"

- -d: 将指定的数据按有关协议解包输出，

- -R 抓包后过滤

- -r 读取本地文件

- -i 指定监听网卡，默认是第一个非本地循环接口，比如：-i eth0

- -s 指定抓取前多少字节：比如：-s 512

- -c 指定抓取数据包的个数，默认捕获无限个。比如：-c 10

- -T 指定输出格式，默认为text，比如：-T fields -T text -T json -T pdml

- -e 指定输出的字段：比如：-T fields -e frame.number -e frame.time -e frame.len -e ip.src -e ip.dst -e ip.proto -e ip.ttl
-e tcp.srcport -e tcp.dstport -e tcp.seq -e tcp.ack
-e udp.srcport -e udp.dstport
-e http.request.url -e http.request.method -e http.response.code
-e dns.qry.name -e dns.resp.name

{{</note>}}


## 安装

```sql
yum install -y epel-release

yum install -y wireshark

tshark -v

tshark --help
```



## 使用



### tshark -c
> tshark -f "tcp port 80" -c 5
```sql
Running as user "root" and group "root". This could be dangerous.
Capturing on 'eth0'
  1 0.000000000 172.17.120.253 -> 172.17.134.77 TCP 66 62232 > http [SYN] Seq=0 Win=64240 Len=0 MSS=1288 WS=256 SACK_PERM=1
  2 0.000000357 172.17.120.253 -> 172.17.134.77 TCP 66 62231 > http [SYN] Seq=0 Win=64240 Len=0 MSS=1288 WS=256 SACK_PERM=1
  3 0.000178047 172.17.134.77 -> 172.17.120.253 TCP 54 http > 62232 [RST, ACK] Seq=1 Ack=1 Win=0 Len=0
  4 0.000210710 172.17.134.77 -> 172.17.120.253 TCP 54 http > 62231 [RST, ACK] Seq=1 Ack=1 Win=0 Len=0
  5 0.279433753 172.17.120.253 -> 172.17.134.77 TCP 66 62233 > http [SYN] Seq=0 Win=64240 Len=0 MSS=1288 WS=256 SACK_PERM=1
5 packets captured
```



```bash

tshark -i eth0

tshark -i eth0 -c 10

tshark -i eth0 -f "tcp dst port 80"

tshark -i eth0 -R "ip.addr == 47.103.25.27"


```


```bash
tshark -f 'tcp port 3332 and host 10.186.61.83'

```

- 在MySQL所在服务器上执行如下命令，抓取MySQL中执行的SQL语句
```sql
tshark -f 'tcp port 16315' -Y "mysql.query" -d tcp.port==16315,mysql -T fields -e frame.time -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport -e mysql.query
```

```bash
tshark -f 'tcp port 3306' \
-Y "mysql.query" \
-d tcp.port==3306,mysql \
-T fields -e frame.time -e ip.src -e ip.dst -e mysql.query

tshark -f 'tcp port 3306' -Y "mysql.query" -d tcp.port==3306,mysql -T fields -e frame.time -e ip.src -e ip.dst -e mysql.query
```

{{<caution>}}
<!---->
在服务器上执行抓包
- -Y "mysql.query" 指定显示过滤器表达式，此处表示仅显示 mysql.query 相关的包，否则会有很多握手之类的包

- -d tcp.port==3332,mysql 指定该抓包会话的协议详细解析器模块，此处表示将 3332 端口上的 TCP 包以 MySQL 协议进行解析：tshark -d help

- -T fields -e mysql.query，即可获取符合 MySQL 协议的 SQL 语句




{{</caution>}}



```sql
tshark -f 'tcp port 16310 and src host 172.17.134.77 and dst host 172.17.134.76' \
-Y "mysql.query" \
-d tcp.port==16310,mysql \
-T fields -e frame.time -e ip.src -e ip.dst -e mysql.query
```

```sql
Running as user "root" and group "root". This could be dangerous.
Capturing on 'eth0'
"Nov  6, 2023 15:47:07.582286159 CST"	172.17.134.77	172.17.134.76	dbscale request node info
"Nov  6, 2023 15:47:07.591296442 CST"	172.17.134.77	172.17.134.76	dbscale request cluster user status count
"Nov  6, 2023 15:47:17.585002557 CST"	172.17.134.77	172.17.134.76	dbscale request node info
"Nov  6, 2023 15:47:17.594690626 CST"	172.17.134.77	172.17.134.76	dbscale request cluster user status count
"Nov  6, 2023 15:47:23.720624711 CST"	172.17.134.77	172.17.134.76	select * from test.t1 limit 1000
"Nov  6, 2023 15:47:27.583261981 CST"	172.17.134.77	172.17.134.76	dbscale request node info
"Nov  6, 2023 15:47:27.593556956 CST"	172.17.134.77	172.17.134.76	dbscale request cluster user status count
"Nov  6, 2023 15:47:37.588544882 CST"	172.17.134.77	172.17.134.76	dbscale request node info
"Nov  6, 2023 15:47:37.601025113 CST"	172.17.134.77	172.17.134.76	dbscale request cluster user status count
"Nov  6, 2023 15:47:47.583067322 CST"	172.17.134.77	172.17.134.76	dbscale request node info
"Nov  6, 2023 15:47:47.592767958 CST"	172.17.134.77	172.17.134.76	dbscale request cluster user status count

```

## tcp连接与释放

- 模拟步骤
```bash
# 在172.17.137.12执行
tshark -f 'tcp port 8026 and host 172.17.130.48'
# 在172.17.130.48执行
mysql -uroot -proot -h172.17.137.12 -P8026
# 在172.17.130.48的mysql命令行上执行exit退出mysql连接
exit;
```

- tshark内容如下：
```sql
2024-04-10T15:25:51 [root@dev /root] # tshark -f 'tcp port 8026 and host 172.17.130.48'
Running as user "root" and group "root". This could be dangerous.
Capturing on 'ens3'
    1 0.000000000 172.17.130.48 → 172.17.137.12 TCP 74 53562 → 8026 [SYN] Seq=0 Win=29200 Len=0 MSS=1460 SACK_PERM=1 TSval=984302109 TSecr=0 WS=128
    2 0.000142500 172.17.137.12 → 172.17.130.48 TCP 74 8026 → 53562 [SYN, ACK] Seq=0 Ack=1 Win=28960 Len=0 MSS=1460 SACK_PERM=1 TSval=2755490396 TSecr=984302109 WS=128
    3 0.000283369 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [ACK] Seq=1 Ack=1 Win=29312 Len=0 TSval=984302113 TSecr=2755490396
    4 1.866758284 172.17.137.12 → 172.17.130.48 TCP 150 8026 → 53562 [PSH, ACK] Seq=1 Ack=1 Win=29056 Len=84 TSval=2755492262 TSecr=984302113
    5 1.866914250 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [ACK] Seq=1 Ack=85 Win=29312 Len=0 TSval=984303980 TSecr=2755492262
    6 1.867014904 172.17.130.48 → 172.17.137.12 TCP 273 53562 → 8026 [PSH, ACK] Seq=1 Ack=85 Win=29312 Len=207 TSval=984303980 TSecr=2755492262
    7 1.867032568 172.17.137.12 → 172.17.130.48 TCP 66 8026 → 53562 [ACK] Seq=85 Ack=208 Win=30080 Len=0 TSval=2755492263 TSecr=984303980
    8 1.874830872 172.17.137.12 → 172.17.130.48 TCP 77 8026 → 53562 [PSH, ACK] Seq=85 Ack=208 Win=30080 Len=11 TSval=2755492271 TSecr=984303980
    9 1.875065307 172.17.130.48 → 172.17.137.12 TCP 105 53562 → 8026 [PSH, ACK] Seq=208 Ack=96 Win=29312 Len=39 TSval=984303988 TSecr=2755492271
   10 1.895988734 172.17.137.12 → 172.17.130.48 TCP 149 8026 → 53562 [PSH, ACK] Seq=96 Ack=247 Win=30080 Len=83 TSval=2755492292 TSecr=984303988
   11 1.935819700 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [ACK] Seq=247 Ack=179 Win=29312 Len=0 TSval=984304049 TSecr=2755492292
   12 167.688045080 172.17.130.48 → 172.17.137.12 TCP 71 53562 → 8026 [PSH, ACK] Seq=247 Ack=179 Win=29312 Len=5 TSval=984469801 TSecr=2755492292
   13 167.688138633 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [FIN, ACK] Seq=252 Ack=179 Win=29312 Len=0 TSval=984469801 TSecr=2755492292
   14 167.699762872 172.17.130.48 → 172.17.137.12 TCP 66 [TCP Retransmission] 53562 → 8026 [FIN, ACK] Seq=252 Ack=179 Win=29312 Len=0 TSval=984469813 TSecr=2755492292
   15 167.699816854 172.17.137.12 → 172.17.130.48 TCP 78 8026 → 53562 [ACK] Seq=179 Ack=253 Win=30080 Len=0 TSval=2755658095 TSecr=984469801 SLE=252 SRE=253
   16 167.700699358 172.17.137.12 → 172.17.130.48 TCP 66 8026 → 53562 [FIN, ACK] Seq=179 Ack=253 Win=30080 Len=0 TSval=2755658096 TSecr=984469801
   17 167.700802621 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [ACK] Seq=253 Ack=180 Win=29312 Len=0 TSval=984469814 TSecr=2755658096
```

#### 1. tcp连接：三次握手

```viz-dot
digraph "connect" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "Client" -> "Server" [label="第一步：SYN"];
        "Server" -> "Client" [label="第二步：SYN, ACK"];
        "Client" -> "Server" [label="第三步：ACK"];
}

```

```sql
    1 0.000000000 172.17.130.48 → 172.17.137.12 TCP 74 53562 → 8026 [SYN] Seq=0 Win=29200 Len=0 MSS=1460 SACK_PERM=1 TSval=984302109 TSecr=0 WS=128
    2 0.000142500 172.17.137.12 → 172.17.130.48 TCP 74 8026 → 53562 [SYN, ACK] Seq=0 Ack=1 Win=28960 Len=0 MSS=1460 SACK_PERM=1 TSval=2755490396 TSecr=984302109 WS=128
    3 0.000283369 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [ACK] Seq=1 Ack=1 Win=29312 Len=0 TSval=984302113 TSecr=2755490396
```


#### 2. tcp释放：四次挥手

```viz-dot
digraph "connect" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "Client" -> "Server" [label="第一步：SYN"];
        "Server" -> "Client" [label="第二步：SYN, ACK"];
        "Client" -> "Server" [label="第三步：ACK"];
}

```

```sql
   15 167.699816854 172.17.137.12 → 172.17.130.48 TCP 78 8026 → 53562 [ACK] Seq=179 Ack=253 Win=30080 Len=0 TSval=2755658095 TSecr=984469801 SLE=252 SRE=253
   16 167.700699358 172.17.137.12 → 172.17.130.48 TCP 66 8026 → 53562 [FIN, ACK] Seq=179 Ack=253 Win=30080 Len=0 TSval=2755658096 TSecr=984469801
   17 167.700802621 172.17.130.48 → 172.17.137.12 TCP 66 53562 → 8026 [ACK] Seq=253 Ack=180 Win=29312 Len=0 TSval=984469814 TSecr=2755658096
```



## 抓取mysql

```sql
tshark -f 'tcp port 3306' -Y "mysql.query" -d tcp.port==3306,mysql -T fields -e frame.time -e ip.src -e ip.dst -e mysql.query
```
- -Y，指定显示过滤器表达式
- -d，指定指定协议，tshark -d help
- -T fields 指定字段
- -e 指定字段













## 查看可用的协议
- 查看可用的协议
```sql
tshark -d help
```
输出为：
```bash
tshark: Parameter "help" doesn't follow the template "<layer_type>==<selector>,<decode_as_protocol>"
tshark: Unknown layer type -- help
tshark: Valid layer types are:
	aim.family (Family ID)
	ansi_637.tele_id (ANSI IS-637-A Teleservice ID)
	ansi_a.ota (IS-683-A (OTA))
	ansi_a.pld (IS-801 (PLD))
	ansi_a.sms (IS-637-A (SMS))
	ansi_map.ota (IS-683-A (OTA))
	ansi_map.pld (IS-801 (PLD))
	ansi_map.tele_id (IS-637 Teleservice ID)
	ansi_tcap.nat.opcode (ANSI TCAP National Opcodes)
	arcnet.protocol_id (ARCNET Protocol ID)
	ax25.pid (AX.25 protocol ID)
	bacapp.vendor_identifier (BACapp Vendor Identifier)
	bacnet.vendor (BACnet Vendor Identifier)
	bctp.tpi (BCTP Tunneled Protocol Indicator)
	ber.oid (BER OID Dissectors)
	ber.syntax (BER Syntax Dissectors)
	bsap.pdu_type (BSAP Message Type)
	bssap.pdu_type (BSSAP Message Type)
	btavctp.service (AVCTP Service)
	btl2cap.cid (L2CAP CID)
	btl2cap.psm (L2CAP PSM)
	btl2cap.service (L2CAP Service)
	btrfcomm.channel (RFCOMM Channel)
	btrfcomm.service (RFCOMM SERVICE)
	bvlc.function (BVLC Function)
	camel.ros.local.arg (CAMEL Operation Argument (local opcode))
	camel.ros.local.err (CAMEL Error (local opcode))
	camel.ros.local.res (CAMEL Operation Result (local opcode))
	chdlctype (Cisco HDLC frame type)
	cip.class.iface (CIP Class Interface Handle)
	cip.data_segment.iface (CIP Data Segment Interface Handle)
	cmip.attribute_id (CMIP Attribute Id)
	cnip.protocol (CN/IP Protocol)
	dccp.port (DCCP port)
	dcp-af.pt (AF Payload Type)
	dcp-etsi.sync (DCP Sync)
	dcp-tpl.ptr (AF Payload Type)
	ddp.type (DDP packet type)
	diameter.3gpp (DIAMETER_3GPP_AVPS)
	diameter.base (DIAMETER_BASE_AVPS)
	diameter.ericsson (DIAMETER_ERICSSON_AVPS)
	dns.tsig.mac (DNS TSIG MAC Dissectors)
	docsis_mgmt (DOCSIS Mac Management)
	dop.oid (DOP OID Dissectors)
	dvb-ci.sas.app_id_str (SAS application id)
	ecatf.type (EtherCAT frame type)
	enip.srrd.iface (SendRequestReplyData.Interface Handle)
	enip.sud.iface (SendUnitData.Interface Handle)
	ethertype (Ethertype)
	etsi_cat.sms_tpdu (3GPP SMS TPDU)
	fc.ftype (FC Frame Type)
	fcct.server (Server)
	fcp.type (FCP Type)
	fr.ietf (Frame Relay NLPID)
	fr.osinl (Frame Relay OSI NLPID)
	gre.proto (GRE protocol type)
	gsm_a.sms_tpdu (GSM SMS TPDU)
	gsm_map.prop.arg.opcode (Proprietary Opcodes)
	gsm_map.prop.err.opcode (Proprietary Opcodes)
	gsm_map.prop.res.opcode (Proprietary Opcodes)
	gsm_map.sms_tpdu (GSM SMS TPDU)
	gsm_sms.udh.port (GSM SMS port IE in UDH)
	gsm_sms_ud.udh.port (GSM SMS port IE in UDH)
	gsmtap.type (GSMTAP type)
	gtp.cdr_fmt (GTP DATA RECORD TYPE)
	gtp.priv_ext (GTP PRIVATE EXT)
	gtpv2.priv_ext (GTPv2 PRIVATE EXT)
	h225.gef.content (H.225 Generic Extensible Framework)
	h225.gef.name (H.225 Generic Extensible Framework (names))
	h225.nsp.h221 (H.225 NonStandardParameter (h221))
	h225.nsp.object (H.225 NonStandardParameter (object))
	h225.tp (H.225 TunnelledProtocol)
	h245.gef.content (H.245 Generic Extensible Framework)
	h245.gef.name (H.245 Generic Extensible Framework (names))
	h245.nsp.h221 (H.245 NonStandardParameter (h221))
	h245.nsp.object (H.245 NonStandardParameter (object))
	h248.magic_num (H248 Magic Num)
	h450.ros.global.arg (H.450 Operation Argument (global opcode))
	h450.ros.global.err (H.450 Error (global opcode))
	h450.ros.global.res (H.450 Operation Result (global opcode))
	h450.ros.local.arg (H.450 Operation Argument (local opcode))
	h450.ros.local.err (H.450 Error (local opcode))
	h450.ros.local.res (H.450 Operation Result (local opcode))
	hci_h1.type (HCI h1 pdu type)
	hci_h4.type (HCI H4 pdu type)
	hnbap.extension (HNBAP-PROTOCOL-EXTENSION)
	hnbap.ies (HNBAP-PROTOCOL-IES)
	hnbap.proc.imsg (HNBAP-ELEMENTARY-PROCEDURE InitiatingMessage)
	hnbap.proc.sout (HNBAP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	hnbap.proc.uout (HNBAP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	hpext.dxsap (HPEXT XSAP)
	http.port (TCP port for protocols using HTTP)
	iax2.codec (IAX codec number)
	iax2.dataformat (IAX dataformat number)
	idp.packet_type (IDP packet type)
	ieee1722.subtype (AVBTP Subtype)
	ieee802a.ecp_pid (ECP)
	interlink.type_version (Interlink type_version)
	ip.proto (IPv4 protocol)
	ipa.osmo.protocol (ip.access Protocol)
	ipx.packet_type (IPX packet type)
	ipx.socket (IPX socket)
	key_mgmt (Key Management)
	lapd.gsm.sapi (LAPD GSM SAPI)
	lapd.sapi (LAPD SAPI)
	lapdm.sapi (LAPDm SAPI)
	lapsat.sapi (LAPSat SAPI)
	lcsap.extension (LCS-AP-PROTOCOL-EXTENSION)
	lcsap.ies (LCS-AP-PROTOCOL-IES)
	lcsap.proc.imsg (LCS-AP-ELEMENTARY-PROCEDURE InitiatingMessage)
	lcsap.proc.sout (LCS-AP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	lcsap.proc.uout (LCS-AP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	ldap.name (LDAP Attribute Type Dissectors)
	llc.bluetooth_pid (Bluetooth OUI PID)
	llc.cimetrics_pid (Cimetrics OUI PID)
	llc.cisco_pid (Cisco OUI PID)
	llc.dsap (LLC SAP)
	llc.extreme_pid (Extreme OUI PID)
	llc.force10_pid (FORCE10 OUI PID)
	llc.foundry_pid (Foundry OUI PID)
	llc.hpteam_pid (Hewlett Packard OUI PID)
	llc.iana_pid (IANA OUI PID)
	llc.nortel_pid (Nortel OUI PID)
	llc.wlccp_pid (Cisco WLCCP OUI PID)
	llc.xid_dsap (LLC XID SAP)
	llcgprs.sapi (GPRS LLC SAPI)
	lldp.orgtlv.oui (LLDP OUI)
	lppa.ies (LPPA-PROTOCOL-IES)
	lppa.proc.imsg (LPPA-ELEMENTARY-PROCEDURE InitiatingMessage)
	lppa.proc.sout (LPPA-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	lppa.proc.uout (LPPA-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	m3ap.extension (M3AP-PROTOCOL-EXTENSION)
	m3ap.ies (M3AP-PROTOCOL-IES)
	m3ap.proc.imsg (M3AP-ELEMENTARY-PROCEDURE InitiatingMessage)
	m3ap.proc.sout (M3AP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	m3ap.proc.uout (M3AP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	mbtcp.prot_id (protocol identifier)
	media_type (Internet media type)
	meta.proto (META protocol)
	mifare.payload (MiFare Payload)
	mip.nvse_ext (Normal Vendor/Organization Specific Extension)
	mip6.vsm (Mobile IPv6 vendor specific option)
	modbus.data (Modbus Data)
	mpeg_sect.tid (MPEG SECT Table ID)
	mpls.label (MPLS protocol)
	mstp.vendor_frame_type (MSTP Vendor specific Frametypes)
	mtp3.service_indicator (MTP3 Service indicator)
	multipart_media_type (Internet media type (for multipart processing))
	nbap.extension (NBAP-PROTOCOL-EXTENSION)
	nbap.ies (NBAP-PROTOCOL-IES)
	nbap.proc.imsg (NBAP-ELEMENTARY-PROCEDURE InitiatingMessage)
	nbap.proc.sout (NBAP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	nbap.proc.uout (NBAP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	nfs_fhandle.type (NFS Filehandle types)
	null.type (BSD AF_ type)
	osinl (OSI incl NLPID)
	osinl.excl (OSI excl NLPID)
	p1.extension (P1-EXTENSION)
	p1.extension-attribute (P1-EXTENSION-ATTRIBUTE)
	p1.tokendata (P1-TOKENDATA)
	pcap.extension (PCAP-PROTOCOL-EXTENSION)
	pcap.ies (PCAP-PROTOCOL-IES)
	pcap.ies.pair.first (PCAP-PROTOCOL-IES-PAIR FirstValue)
	pcap.ies.pair.second (PCAP-PROTOCOL-IES-PAIR SecondValue)
	pcap.proc.imsg (PCAP-ELEMENTARY-PROCEDURE InitiatingMessage)
	pcap.proc.out (PCAP-ELEMENTARY-PROCEDURE Outcome)
	pcap.proc.sout (PCAP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	pcap.proc.uout (PCAP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	pgm.port (PGM port)
	pn532.payload (PN532 Payload)
	ppp.protocol (PPP protocol)
	q931.codeset (Q.931 Codeset)
	q931.ie (Q.931 IE)
	q932.ros.etsi.local.arg (Q.932 ETSI Operation Argument (local opcode))
	q932.ros.etsi.local.err (Q.932 ETSI Error (local opcode))
	q932.ros.etsi.local.res (Q.932 ETSI Operation Result (local opcode))
	q932.ros.global.arg (Q.932 Operation Argument (global opcode))
	q932.ros.global.err (Q.932 Error (global opcode))
	q932.ros.global.res (Q.932 Operation Result (global opcode))
	q932.ros.local.arg (Q.932 Operation Argument (local opcode))
	q932.ros.local.err (Q.932 Error (local opcode))
	q932.ros.local.res (Q.932 Operation Result (local opcode))
	qsig.ext (QSIG Extension)
	ranap.extension (RANAP-PROTOCOL-EXTENSION)
	ranap.ies (RANAP-PROTOCOL-IES)
	ranap.ies.pair.first (RANAP-PROTOCOL-IES-PAIR FirstValue)
	ranap.ies.pair.second (RANAP-PROTOCOL-IES-PAIR SecondValue)
	ranap.nas_pdu (RANAP NAS PDU)
	ranap.proc.imsg (RANAP-ELEMENTARY-PROCEDURE InitiatingMessage)
	ranap.proc.out (RANAP-ELEMENTARY-PROCEDURE Outcome)
	ranap.proc.sout (RANAP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	ranap.proc.uout (RANAP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	rmcp.class (RMCP Class)
	rnsap.extension (RNSAP-PROTOCOL-EXTENSION)
	rnsap.ies (RNSAP-PROTOCOL-IES)
	rnsap.proc.imsg (RNSAP-ELEMENTARY-PROCEDURE InitiatingMessage)
	rnsap.proc.sout (RNSAP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	rnsap.proc.uout (RNSAP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	ros.oid (ROS OID Dissectors)
	rsvp.proto (RSVP Protocol)
	rtcp.app.name (RTCP Application Name)
	rtcp.psfb.fmt (RTCP Payload Specific Feedback Message Format)
	rtcp.rtpfb.fmt (RTCP Generic RTP Feedback Message Format)
	rtp.ext.rfc5285.id (RTP Generic header extension (RFC 5285))
	rtp.hdr_ext (RTP header extension)
	rtp.pt (RTP payload type)
	rtp_dyn_payload_type (Dynamic RTP payload type)
	rtse.oid (RTSE OID Dissectors)
	rua.extension (RUA-PROTOCOL-EXTENSION)
	rua.ies (RUA-PROTOCOL-IES)
	rua.proc.imsg (RUA-ELEMENTARY-PROCEDURE InitiatingMessage)
	rua.proc.sout (RUA-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	rua.proc.uout (RUA-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	s1ap.extension (S1AP-PROTOCOL-EXTENSION)
	s1ap.ies (S1AP-PROTOCOL-IES)
	s1ap.ies.pair.first (S1AP-PROTOCOL-IES-PAIR FirstValue)
	s1ap.ies.pair.second (S1AP-PROTOCOL-IES-PAIR SecondValue)
	s1ap.proc.imsg (S1AP-ELEMENTARY-PROCEDURE InitiatingMessage)
	s1ap.proc.sout (S1AP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	s1ap.proc.uout (S1AP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	sabp.extension (SABP-PROTOCOL-EXTENSION)
	sabp.ies (SABP-PROTOCOL-IES)
	sabp.proc.imsg (SABP-ELEMENTARY-PROCEDURE InitiatingMessage)
	sabp.proc.sout (SABP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	sabp.proc.uout (SABP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	sbc_ap.extension (SBC-AP-PROTOCOL-EXTENSION)
	sbc_ap.ies (SBC-AP-PROTOCOL-IES)
	sbc_ap.proc.imsg (SBC-AP-ELEMENTARY-PROCEDURE InitiatingMessage)
	sbc_ap.proc.sout (SBC-AP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	sbc_ap.proc.uout (SBC-AP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	sccp.ssn (SCCP SSN)
	sctp.port (SCTP port)
	sctp.ppi (SCTP payload protocol identifier)
	sip.hdr (SIP Extension header)
	sita.proto (SITA protocol number)
	sll.ltype (Linux protocol type)
	sm_pco.protocol (GPRS SM PCO PPP protocol)
	snmp.variable_oid (SNMP Variable OID)
	spp.socket (SPP socket)
	spx.socket (SPX socket)
	sua.prop.tags (Proprietary SUA Tags)
	t124.ns (T.124 H.221 Non Standard Dissectors)
	t124.sd (T.124 H.221 Send Data Dissectors)
	tali.opcode (Tali OPCODE)
	tcp.port (TCP port)
	teredo (Teredo )
	tipc.usr (TIPC user)
	tipcv2.port_name_type (TIPC port name type)
	udp.port (UDP port)
	usb.bulk (USB bulk endpoint)
	usb.control (USB control endpoint)
	usb.descriptor (USB descriptor)
	usb.device (USB device)
	usb.interrupt (USB interrupt endpoint)
	usb.product (USB product)
	usb.protocol (USB protocol)
	usbccid.payload (CCID Payload)
	vines_ip.protocol (Vines protocol)
	vines_llc.ptype (Vines LLC protocol)
	ws.port (TCP port for protocols using WebSocket)
	wtap_encap (Wiretap encapsulation type)
	x.25.spi (X.25 secondary protocol identifier)
	x2ap.extension (X2AP-PROTOCOL-EXTENSION)
	x2ap.ies (X2AP-PROTOCOL-IES)
	x2ap.proc.imsg (X2AP-ELEMENTARY-PROCEDURE InitiatingMessage)
	x2ap.proc.sout (X2AP-ELEMENTARY-PROCEDURE SuccessfulOutcome)
	x2ap.proc.uout (X2AP-ELEMENTARY-PROCEDURE UnsuccessfulOutcome)
	zbee.profile (ZigBee Profile ID)


```