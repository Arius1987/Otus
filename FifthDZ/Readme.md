# Доманшяя работа №5
## Настройка L2 evpn // ДЗ
Схема сети:

![image](https://github.com/user-attachments/assets/973c05bb-b86d-4740-9143-40914c1858f3)


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
>sh run\
! Command: show running-config\
! device: 3-3-3-3 (vEOS-lab, EOS-4.28.0F)\
!\
! boot system flash:/vEOS-lab.swi\
!\
no aaa root\
!\
transceiver qsfp default-mode 4x10G\
!\
service routing protocols model multi-agent\
!\
hostname 3-3-3-3\
!\
spanning-tree mode mstp\
!\
vlan 10\
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
!
interface Loopback1\
   ip address 30.30.30.30/32\
   ip ospf area 0.0.0.0\
!\
interface Management1\
!\
interface Vxlan1\
   vxlan source-interface Loopback1\
   vxlan udp-port 4789\
   vxlan vlan 10 vni 1010\
!\
ip routing\
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
router ospf 1\
   router-id 3.3.3.3\
   max-lsa 12000\
!\
end\
3-3-3-3#\

Проверяем на лифе доступность evpn маршрутов:

![image](https://github.com/user-attachments/assets/fd4b8e11-ba85-4e06-bd3d-f9e63ff032af)

Все в порядке.
Проверим доступность конечного хоста L2 сегмента:

![image](https://github.com/user-attachments/assets/3920d980-3138-45b6-b6d5-6f779bcd8233)

Проверим дамп vxlan:

![image](https://github.com/user-attachments/assets/586cff97-b525-45bf-93ef-c34efb867007)
