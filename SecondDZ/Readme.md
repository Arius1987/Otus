# Доманшяя работа №2
## Построение Underlay сети(OSPF) // ДЗ

Топология сети:
![image](https://github.com/user-attachments/assets/0595340a-ea9e-4554-b47a-d56a67ac5223)


Для всех коммутаторов настроен ospf с анонсом сетей /31 между спайнами и лифами, а также анансом interface vlan подсетей исп.для связей лифов с серверами.
Конфиг коммутатора 1.1.1.1:

> #sh run\
! Command: show running-config\
! device: 1-1-1-1 (vEOS-lab, EOS-4.28.0F)\
!\
! boot system flash:/vEOS-lab.swi\
!\
no aaa root\
!\
transceiver qsfp default-mode 4x10G\
!\
service routing protocols model ribd\
!\
hostname 1-1-1-1\
!\
spanning-tree mode mstp\
!\
interface Ethernet1\
   no switchport\
   ip address 192.168.0.0/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet2\
   no switchport\
   ip address 192.168.1.0/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet3\
   no switchport\
   ip address 192.168.2.0/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet4\
   no switchport\
   ip address 192.168.3.0/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet5\
   no switchport\
   ip address 192.168.4.0/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Loopback0\
   ip address 1.1.1.1/32\
   ip ospf area 0.0.0.0\
!\
interface Management1\
!\
ip routing\
!\
router ospf 1\
   router-id 1.1.1.1\
   no passive-interface Ethernet1\
   no passive-interface Ethernet2\
   no passive-interface Ethernet3\
   no passive-interface Ethernet4\
   no passive-interface Ethernet5\
   max-lsa 12000\
!\

Проверим таблицу маршрутизации для ospf:

> 1-1-1-1#sh ip route ospf\
VRF: default\
Codes: C - connected, S - static, K - kernel,\
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,\
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,\
       N2 - OSPF NSSA external type2, B - Other BGP Routes,\
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,\
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,\
       A O - OSPF Summary, NG - Nexthop Group Static Route,\
       V - VXLAN Control Service, M - Martian,\
       DH - DHCP client installed default route,\
       DP - Dynamic Policy Route, L - VRF Leaked,\
       G  - gRIBI, RC - Route Cache Route\
 O        2.2.2.2/32 [110/30] via 192.168.0.1, Ethernet1\
                              via 192.168.1.1, Ethernet2\
                              via 192.168.2.1, Ethernet3\
                              via 192.168.3.1, Ethernet4\
                              via 192.168.4.1, Ethernet5\
 O        3.3.3.3/32 [110/30] via 192.168.0.1, Ethernet1\
                              via 192.168.1.1, Ethernet2\
                              via 192.168.2.1, Ethernet3\
                              via 192.168.3.1, Ethernet4\
                              via 192.168.4.1, Ethernet5\
 O        10.10.10.10/32 [110/20] via 192.168.0.1, Ethernet1\
 O        11.11.11.11/32 [110/20] via 192.168.1.1, Ethernet2\
 O        12.12.12.12/32 [110/20] via 192.168.2.1, Ethernet3\
 O        13.13.13.13/32 [110/20] via 192.168.3.1, Ethernet4\
 O        14.14.14.14/32 [110/20] via 192.168.4.1, Ethernet5\
 O        172.16.0.0/29 [110/20] via 192.168.0.1, Ethernet1\
                                 via 192.168.1.1, Ethernet2\
 O        172.16.1.0/29 [110/20] via 192.168.0.1, Ethernet1\
                                 via 192.168.1.1, Ethernet2\
 O        172.16.2.0/29 [110/20] via 192.168.1.1, Ethernet2\
                                 via 192.168.2.1, Ethernet3\
 O        172.16.3.0/29 [110/20] via 192.168.2.1, Ethernet3\
                                 via 192.168.3.1, Ethernet4\
 O        172.16.4.0/29 [110/20] via 192.168.3.1, Ethernet4\
 O        172.16.5.0/29 [110/20] via 192.168.3.1, Ethernet4\
 O        192.168.10.0/31 [110/20] via 192.168.0.1, Ethernet1\
 O        192.168.11.0/31 [110/20] via 192.168.1.1, Ethernet2\
 O        192.168.12.0/31 [110/20] via 192.168.2.1, Ethernet3\
 O        192.168.13.0/31 [110/20] via 192.168.3.1, Ethernet4\
 O        192.168.14.0/31 [110/20] via 192.168.4.1, Ethernet5\
 O        192.168.20.0/31 [110/20] via 192.168.0.1, Ethernet1\
 O        192.168.21.0/31 [110/20] via 192.168.1.1, Ethernet2\
 O        192.168.22.0/31 [110/20] via 192.168.2.1, Ethernet3\
 O        192.168.23.0/31 [110/20] via 192.168.3.1, Ethernet4\
 O        192.168.24.0/31 [110/20] via 192.168.4.1, Ethernet5\


Все необходимые маршруты есть. 
Аналогично конфиг лифа 10.10.10.10:

> 10-10-10-10#sh run\
! Command: show running-config\
! device: 10-10-10-10 (vEOS-lab, EOS-4.28.0F)\
!\
! boot system flash:/vEOS-lab.swi\
!\
no aaa root\
!\
transceiver qsfp default-mode 4x10G\
!\
service routing protocols model ribd\
!\
hostname 10-10-10-10\
!\
spanning-tree mode mstp\
!\
vlan 2-3\
!\
interface Ethernet1\
   no switchport\
   ip address 192.168.0.1/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet2\
   switchport access vlan 2\
!\
interface Ethernet3\
   no switchport\
   ip address 192.168.10.1/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet4\
   no switchport\
   ip address 192.168.20.1/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!
interface Ethernet5\
!
interface Ethernet6\
   switchport access vlan 3\
!
interface Loopback0\
   ip address 10.10.10.10/32\
   ip ospf area 0.0.0.0\
!\
interface Management1\
!\
interface Vlan2\
   ip address 172.16.0.1/29\
   ip ospf area 0.0.0.0\
!\
interface Vlan3\
   ip address 172.16.1.1/29\
   ip ospf area 0.0.0.0\
!\
ip routing\
!\
router ospf 1\
   router-id 10.10.10.10\
   no passive-interface Ethernet1\
   no passive-interface Ethernet2\
   no passive-interface Ethernet3\
   no passive-interface Ethernet4\
   max-lsa 12000\
!\
end\

Его ospf маршруты:

> 10-10-10-10#sh ip route ospf\
VRF: default\
Codes: C - connected, S - static, K - kernel,\
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,\
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,\
       N2 - OSPF NSSA external type2, B - Other BGP Routes,\
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,\
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,\
       A O - OSPF Summary, NG - Nexthop Group Static Route,\
       V - VXLAN Control Service, M - Martian,\
       DH - DHCP client installed default route,\
       DP - Dynamic Policy Route, L - VRF Leaked,\
       G  - gRIBI, RC - Route Cache Route\
 O        1.1.1.1/32 [110/20] via 192.168.0.0, Ethernet1\
 O        2.2.2.2/32 [110/20] via 192.168.10.0, Ethernet3\
 O        3.3.3.3/32 [110/20] via 192.168.20.0, Ethernet4\
 O        11.11.11.11/32 [110/30] via 192.168.0.0, Ethernet1\
                                  via 192.168.10.0, Ethernet3\
                                  via 192.168.20.0, Ethernet4\
 O        12.12.12.12/32 [110/30] via 192.168.0.0, Ethernet1\
                                  via 192.168.10.0, Ethernet3\
                                  via 192.168.20.0, Ethernet4\
 O        13.13.13.13/32 [110/30] via 192.168.0.0, Ethernet1\
                                  via 192.168.10.0, Ethernet3\
                                  via 192.168.20.0, Ethernet4\
 O        14.14.14.14/32 [110/30] via 192.168.0.0, Ethernet1\
                                  via 192.168.10.0, Ethernet3\
                                  via 192.168.20.0, Ethernet4\
 O        172.16.2.0/29 [110/30] via 192.168.0.0, Ethernet1\
                                 via 192.168.10.0, Ethernet3\
                                 via 192.168.20.0, Ethernet4\
 O        172.16.3.0/29 [110/30] via 192.168.0.0, Ethernet1\
                                 via 192.168.10.0, Ethernet3\
                                 via 192.168.20.0, Ethernet4\
 O        172.16.4.0/29 [110/30] via 192.168.0.0, Ethernet1\
                                 via 192.168.10.0, Ethernet3\
                                 via 192.168.20.0, Ethernet4\
 O        172.16.5.0/29 [110/30] via 192.168.0.0, Ethernet1\
                                 via 192.168.10.0, Ethernet3\
                                 via 192.168.20.0, Ethernet4\
 O        192.168.1.0/31 [110/20] via 192.168.0.0, Ethernet1\
 O        192.168.2.0/31 [110/20] via 192.168.0.0, Ethernet1\
 O        192.168.3.0/31 [110/20] via 192.168.0.0, Ethernet1\
 O        192.168.4.0/31 [110/20] via 192.168.0.0, Ethernet1\
 O        192.168.11.0/31 [110/20] via 192.168.10.0, Ethernet3\
 O        192.168.12.0/31 [110/20] via 192.168.10.0, Ethernet3\
 O        192.168.13.0/31 [110/20] via 192.168.10.0, Ethernet3\
 O        192.168.14.0/31 [110/20] via 192.168.10.0, Ethernet3\
 O        192.168.21.0/31 [110/20] via 192.168.20.0, Ethernet4\
 O        192.168.22.0/31 [110/20] via 192.168.20.0, Ethernet4\
 O        192.168.23.0/31 [110/20] via 192.168.20.0, Ethernet4\
 O        192.168.24.0/31 [110/20] via 192.168.20.0, Ethernet4\


Все остальные коммутаторы настроены аналогично согласно ip адресации на схеме сверху. 

