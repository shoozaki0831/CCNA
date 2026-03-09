CCNA構築構成図

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

(NAT setting)
access-list 1 permit 192.168.0.0 0.0.255.255
ip nat insaide source list 1 interface g0/0 overlord
(IF setting)
interface g0/0
ip nat inside
interface g0/1
ip nat outsaide

# 学習ポイント
inside/outside の意味
overload の仕組み
NATテーブルの確認方法 


検証コマンドは以下の通り
show ip nat statistics
show ip nat trunslation




