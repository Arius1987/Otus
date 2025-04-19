# Доманшяя работа №6
## Настройка L3 evpn // ДЗ
Схема сети:
![image](https://github.com/user-attachments/assets/52d22649-764f-4bca-a6cd-21f93c79fc12)

Ранинг конфиг первого спайна:

>sh run
! Command: show running-config\
! device: 1-1-1-1 (vEOS-lab, EOS-4.28.0F)\
!\
! boot system flash:/vEOS-lab.swi\
!\
no aaa root\
!\
transceiver qsfp default-mode 4x10G\
!\
service routing protocols model multi-agent\
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
!\
interface Loopback0\
   ip address 1.1.1.1/32\
   ip ospf area 0.0.0.0\
!\
interface Management1\
!\
ip routing\
!\
router bgp 64512\
   router-id 1.1.1.1\
   timers bgp 3 9\
   maximum-paths 16\
   neighbor iBGP-lab peer group\
   neighbor iBGP-lab remote-as 64512\
   neighbor iBGP-lab update-source Loopback0\
   neighbor iBGP-lab bfd\
   neighbor iBGP-lab route-reflector-client\
   neighbor iBGP-lab send-community extended\
   neighbor 3.3.3.3 peer group iBGP-lab\
   neighbor 4.4.4.4 peer group iBGP-lab\
   !\
   address-family evpn\
      neighbor iBGP-lab activate\
!\
router ospf 1\
   router-id 1.1.1.1\
   max-lsa 12000\
!\
end\

Ранинг конфиг первого лифа:

> 3-3-3-3#sh run\
! Command: show running-config\
! device: 3-3-3-3 (vEOS-lab, EOS-4.28.0F)\
!\
! boot system flash:/vEOS-lab.swi\
!\
no aaa root\
!\
transceiver qsfp default-mode 4x10G\
!\
service routing protocols model multi-agent
!\\
hostname 3-3-3-3\
!\
spanning-tree mode mstp\
!\
vlan 10\
!\
vrf instance VRFlab\
!\
interface Ethernet1\
   no switchport\
   ip address 192.168.2.1/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet2\
   no switchport\
   ip address 192.168.0.1/31\
   ip ospf network point-to-point\
   ip ospf area 0.0.0.0\
!\
interface Ethernet3\
   switchport access vlan 10\
!\
interface Loopback0\
   ip address 3.3.3.3/32\
   ip ospf area 0.0.0.0\
!\
interface Management1\
!\
interface Vlan10\
   vrf VRFlab\
   ip address virtual 172.16.0.1/29\
!
interface Vxlan1\
   vxlan source-interface Loopback0\
   vxlan udp-port 4789\
   vxlan vlan 10 vni 1010\
   vxlan vrf VRFlab vni 1020\
!\
ip virtual-router mac-address 02:00:00:00:00:00\
!\
ip routing\
ip routing vrf VRFlab\
!\
router bgp 64512\
   router-id 3.3.3.3\
   timers bgp 3 9\
   maximum-paths 16\
   neighbor iBGP-lab peer group\
   neighbor iBGP-lab remote-as 64512\
   neighbor iBGP-lab update-source Loopback0\
   neighbor iBGP-lab bfd\
   neighbor iBGP-lab send-community extended\
   neighbor 1.1.1.1 peer group iBGP-lab\
   neighbor 2.2.2.2 peer group iBGP-lab\
   !\
   vlan 10\
      rd 3.3.3.3:64512\
      route-target both 64512:1010\
      redistribute learned\
   !\
   address-family evpn\
      neighbor iBGP-lab activate\
   !\
   vrf VRFlab\
      rd 30.30.30.30:1020\
      route-target import evpn 64512:1020\
      route-target export evpn 64512:1020\
!\
router ospf 1\
   router-id 3.3.3.3\
   max-lsa 12000\
!\
end\
3-3-3-3#

Проверим таблицу мак адресов:

![image](https://github.com/user-attachments/assets/8a0e21c2-141b-49e7-be9f-077ac9dddbb3)

Вимим, что пришел мак адрес L3 evpn хоста 172.16.1.2/30 

Таблица bgp evpn:

![image](https://github.com/user-attachments/assets/882c5b6f-e3ff-44d5-8c7c-f07368f3598e)

Пинг с хоста 172.16.0.3/29 к 172.16.1.2/30:

![image](https://github.com/user-attachments/assets/493c937d-99eb-4563-8324-eb6bf8567536)




