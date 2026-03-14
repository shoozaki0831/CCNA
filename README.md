CCNA構築構成図1

＃＃構成　PC-switch-router1-router2(nat)-server  


pc　192.168.10.2
|
switch 
|
router1
192.168.10.1 G0/0
10.0.12.1    G0/1
|
router2
10.0.12.2    G0/0
200.0.0.1    G0/1
|
server
200.0.0.2

===SW　設定===
conf t
＃VLAN作成
vlan 10
name shoroom1
vlan 20
name shoroom2

＃VLANをACCESSに(PC側)
interface fa0/1
switchport mode access
switchport access vlan 10
exit
interface fa0/2
switchport mode access
switchport access vlan 20
exit

＃VLANをtrunkにまとめる（RT側）
interface fa0/3
switchport mode trunk


＝＝＝RT1設定＝＝＝
conf t

IF 起動
interaface g0/0
no shutdown
interface g0/1
no shutdown

RoAS 設定
interface g0/0.10
encapsulation dot1q.10
ip address 192.168.10.1 255.255.255.0 →GWに設定
interface g0/1.20
encapsulation dot1q.20
ip address 192.168.20.1 255.255.255.0
exit

default GW 設定
ip route 0.0.0.0 10.0.12.1

DHCP 設定（PC側）
ip dhcp pool vlan 10
network 192.168.10.0 255.255.255.0
default-gateway 192.168.10.1
exit
ip dhcp pool vlan 20
network 192.168.20.0 255.255.255.0
default-gateway 192.168.20.1

OSPF setting
router ospf 1
network 10.0.12.0 0.0.0.255 area 0
network 192.168.10.0 0.0.0.255 area 0　→NW address指定
network 192.168.20.0 0.0.0.255 area 0　→NW address指定

===RT2setting===
conf t 

IF,NAT起動
interface g0/0
ip address 10.0.12.2 255.255.255.252
ip nat inside
no shutdown
exit

interface g0/1
ip address 200.0.0.1 255.255.255.0
ip nat outside
no shutdown
exit

NAPT setting 
access-list permit 192.168.0.0 0.0.255.255
ip nat inside source list 1 interface g0/1 overload →IF指定するのは、外側（インターネット側）

exit

OSPFsetting
router ospf 1
network 10.0.12.0 0.0.0.255 area 0


検証コマンドは以下の通り
show ip nat statistics
show ip nat trunslation




