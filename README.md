##CCNA NETWORK LAB



##NetWork Toporogy

 (構成)
  pc　    IP:192.168.10.2 GW:192.168.10.1               
  ||  
switch   
  ||
router1  G0/0:trunk(192.168.10.1と192.168.20.1) G0/1:10.0.12.1
  ||       
router2  G0/0:10.0.12.2 G0/1:200.0.0.1    
  ||
server   IP:200.0.0.2

##Technologies Used
-VLAN
-Router on a Stick
-DHCP
-OSPF
-NAPT

##Configuretion
SW setting
RT1 setting
RT2 setting

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
説明
IFfa0/1 IFfa0/2 VLANポートをアクセスにする
IFfa0/3 ポートをトランクモードにして、VLAN10.20共に通信可能にする


＝＝＝RT1設定＝＝＝
conf t

IF 起動
interaface g0/0
no shutdown
interface g0/1
no shutdown
説明
IF g0/0.g0/1 を起動させる。

RoAS 設定
interface g0/0.10
encapsulation dot1q.10
ip address 192.168.10.1 255.255.255.0 →GWに設定
interface g0/1.20
encapsulation dot1q.20
ip address 192.168.20.1 255.255.255.0
exit
説明
VLAN10.20にそれぞれトランキングプロトコルを指定する。

default GW 設定
ip route 0.0.0.0 10.0.12.1
説明
宛先不明の通信をRT2に繋げるため

DHCP 設定（PC側）
ip dhcp pool vlan 10
network 192.168.10.0 255.255.255.0
default-gateway 192.168.10.1
exit
ip dhcp pool vlan 20
network 192.168.20.0 255.255.255.0
default-gateway 192.168.20.1
説明：DHCPサーバーから、自動的にVLAN10には、192.168.10.0/24のIP付与、VLAN20には192.168.20.0/24のIP付与を設定
　　　また、GWもそれぞれ自動設定

OSPF setting
router ospf 1
network 10.0.12.0 0.0.0.3 area 0
network 192.168.10.0 0.0.0.255 area 0　→NW address指定
network 192.168.20.0 0.0.0.255 area 0　→NW address指定
説明：NW：10.0.12.0/30 の自動経路設定
　　　NW：192.168.10.0 の自動経路設定
　　　NW:192.168.20.0　の自動経路設定

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
説明：IF G0/0,G0/1へIP付与と内部NAT外部NATの設定

NAPT setting 
access-list permit 192.168.0.0 0.0.255.255
ip nat inside source list 1 interface g0/1 overload →IF指定するのは、外側（インターネット側）
exit
説明：192.168.0.0 0.0.255.255　からの通信を許可するリスト1を作成。
    リスト1をIF G0/1（外側）へ適用させる。（overloadコマンド）
　　　
OSPFsetting
router ospf 1
network 10.0.12.0 0.0.0.255 area 0
説明：NW 10.0.12.0/24　の自動経路設定


検証コマンドは以下の通り
show ip route
show ip ospf neighbors
show ip nat statistics
show ip nat trunslation





