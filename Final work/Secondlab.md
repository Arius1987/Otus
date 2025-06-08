Вторая лабораторная работа. В ней мы развернем underlay сетку и L3 vxlan. После чего прикрутим ipsec - шифрование (раз предлагают в оф.документации, почему бы не попробовать)

Вот схема сети:

![image](https://github.com/user-attachments/assets/ebdd9d5d-5ff9-41a5-ba9b-0fbc33bed232)


В вэб-интерфейсе proxmox (192.168.5.23) задим в Datacenter-SDN-Options. Жмем Controllers-Add-Evpn:

![image](https://github.com/user-attachments/assets/b96c58e1-577e-489c-be68-8161f9eaf8db)

3.3.3.3 - lo интерфейс прокмокса 192.168.5.24
65000 - Наша AS (iBGP)






При построениии underlay я напоролся на первую проблему- дело в том, что не сморя на завлянную поддержку isis и bgp использовать их из графического интерфейса мы не можем, т.к. согласно оф.документации proxmox:

![image](https://github.com/user-attachments/assets/f0966a5c-9248-483a-b52a-fc486b49200c)

Он существует только для экспорта маршрутов EVPN в домен ISIS.
А что BGP?

![image](https://github.com/user-attachments/assets/bb10c844-4a5a-4987-89bc-21fb3c0b13f9)

...он также может быть использован для экспорта маршрутов EVPN во внешний одноранговый узел BGP - что также не подходит для пострение undelay сети. 

В случае небольшой сети мы можем использовать статические маршруты. Но я считаю это плохой идеей и потому предлагаю пока закрыть вэб-интерфейс proxmox и залезть под его капот - в пакет frr. Там мы развернем простенький ospf.

Отредактируем файл /etc/frr/daemons:

bgpd=yes\
ospfd=yes\
bgpd=yes

Остальное не изменений, сохраняем, выходим. Рестаруем демона frr:

systemctl restart frr

Далее нужно в linux нужно настроить lo интерфес. Для этого идем в /etc/network/interfaces и проводим lo интерфейс подобному виду:

![image](https://github.com/user-attachments/assets/be74c107-2bbd-4a1d-83da-d7d24487cdc9)

Рестуртуем службу сети:

systemctl restart networking

Далее будем настраивать frr, он настраивается на подобие роутеров на базе cisco ios



