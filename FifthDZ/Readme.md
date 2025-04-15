# Доманшяя работа №5
## Настройка L2 evpn // ДЗ
Схема сети:
![image](https://github.com/user-attachments/assets/20c63ae7-9400-4b3f-ba41-2b474173321c)

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
