## Вторая лабораторная работа.

В ней мы развернем underlay сетку и L3 vxlan. После чего прикрутим ipsec - шифрование (раз предлагают в оф.документации, почему бы не попробовать)

Вот схема сети:

![image](https://github.com/user-attachments/assets/01cfdeaf-9f22-4d6f-8a89-3e977ed2408d)



Сначала, как бы это странно не выглядело мы настроим overlay сеть силами графического интерфейса proxmox, данные настройки запишутся в файл /etc/frr/frr.conf, затем мы добавим добавим в него наш ospf-ый underlay.

Приступим: в вэб-интерфейсе proxmox (192.168.5.23) задим в Datacenter-SDN-Options. Жмем Controllers-Add-Evpn:

![image](https://github.com/user-attachments/assets/b96c58e1-577e-489c-be68-8161f9eaf8db)

3.3.3.3 - lo интерфейс прокмокса 192.168.5.24 с которым поднимем evpn пиринг

65000 - Наша AS (iBGP)

Далее в Datacenter-SDN-Zones

![image](https://github.com/user-attachments/assets/2e2380b1-2572-4fac-8bbc-423f5bffeee3)


Вкратце о настройках:

id - название

Controller - выбор настроенного ранее контроллера

VRF VXLAN TAG - номер vni

VNet MAC Address - согласно документации: "адрес любой рассылки, который назначается всем виртуальным сетям в этой зоне. Будет сгенерирован автоматически, если не определен". Я так и не понял, зачем это.

Exit Nodes - гипервизоры, которые должны быть настроены как шлюзы выхода из сети EVPN через реальную сеть. Настроенные узлы объявят маршрут по умолчанию в сети EVPN.

Primary Exit Node - если вы используете несколько выходных гипервизоров, вы можете перенаправляйть трафик через выбранный основной выходной узел вместо балансировки нагрузки на всех узлах. Необязательно, но необходимо, если вы хотите использовать SNAT или если ваш вышестоящий маршрутизатор не поддерживает ECMP

Advertise Subnets - анонс коннектед сеток в EVPN (я так понимаю, это анонс машрутов 5го типа). Неоходимо, если у вас "Тихие" виртуальные машины/ контейнеры (например, если у вас несколько IP-адресов и шлюз anycast не видит трафик с этих IP-адресов, IP-адреса не смогут быть доступны внутри сети EVPN)

Disable ARP ND Suppression - необходимо, если вы используете плавающие IP-адреса в своих виртуальных машинах (IP-и MAC-адреса перемещаются между системами).

Route-target Import - импорт, rt. Можно написать несколько через пробел

Опции далее, думаю, разбирать не стоит

После чего идем в Datacenter-SDN-Vnets-Create

![image](https://github.com/user-attachments/assets/53324136-805a-4fb5-a09b-86e885ef6720)

Где нужно выбрать имя Vnet, созданную ранее Zone и Tag (идентификатор VlanIf)

Далее в окошке справа "Suntets" создаем подсети для наших виртуалок (там же к ним можно и прикрутить dhcp сервер) 

![image](https://github.com/user-attachments/assets/a7c8f4a0-2b81-4e83-8712-349650f2a99b)

Стоит обратить внимание, что указанный шлюз станент Anycast gateway для наших вируальных машин. 

Далее в Datacenter-SDN. Жмем Apply - это дейсвие создаст виртульный адаптер, который мы будем прикреплять к виртуалкам и контейнерам. 

Откроем файл /etc/frr/frr.conf чтоб посмотреть, что мы там наконфигурировали в вэб-интерфейсе:

![image](https://github.com/user-attachments/assets/0a83fcf2-4143-4571-9b90-d0393124c905)

![image](https://github.com/user-attachments/assets/72e3809a-76b8-40be-bf6f-731bb41d79c8)


Почему-то в этом файле я не нашел ip anycast gateway (172.16.0.1), признаться я так и не понял, куда пишутся эти строки конфига

Теперь настало время настраивать underlay и с ним я напоролся на первую проблему- дело в том, что не сморя на завлянную поддержку isis и bgp использовать их из графического интерфейса мы не можем, т.к. согласно оф.документации proxmox:

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

Теперь запустим виртуальную консоль vtysh. Она эмулирует консоль cisco ios. записанные в ней настройки пишутся в /etc/frr/frr.conf

![image](https://github.com/user-attachments/assets/a8c92ed8-463e-4c43-8fa5-956f9a2532dd)

Ранинг конфиг мы уже смотрели при просмотре файла /etc/frr/frr.conf. Ознакомимся со списком интерфейсов:

![image](https://github.com/user-attachments/assets/6e5d0a78-425b-40c0-add3-53e6fdb7ea5e)

Где:
ens4 - линк к роутеру R5

lo - лупбэк

vmbr0 - бринжд для менеджмента, а также по умолчанию он используется для подключения виртуальных машин

myvnet - тот самый интерфейс с ip 172.16.0.1 - anycast gateway - который сохраняется непонятно где.

Настроим ospf:

![image](https://github.com/user-attachments/assets/04d24616-45bc-44d9-b6cf-c019cfa56033)

![image](https://github.com/user-attachments/assets/7aac0b88-d17d-4871-af0a-c7c117e1b4ae)

Выходим из меню конфигурирования:
exit
Сохранияем конфиг
wr mem
Выходим из vtysh
exit

Рестартуем frr:
systemctl restart frr

Далее по схеме следает настройка роутера R5. В ней нет ничего особенного, потому приведу его running config:

![image](https://github.com/user-attachments/assets/370fd5a2-68b3-467f-be2d-62f634e02ad0)

![image](https://github.com/user-attachments/assets/53a3b759-d174-4796-8386-3698e93588a1)

![image](https://github.com/user-attachments/assets/161824e7-2347-4962-b669-6e31194968db)

Проверим ip связанность с lo интерфейсом proxmox-а

![image](https://github.com/user-attachments/assets/6a5c7cd6-6f24-46cb-825c-cb596e5dba3d)


Работает

Далее настроим proxmox 192.168.5.24 (lo 3.3.3.3) аналогично 192.168.5.23, разница только в ip адресах и тем, что на 3.3.3.3  мы содаем Sunets 172.17.0.0/24 gw - 172.17.0.1. Сам процесс настройки я описывать не буду

Проверю ip связанность между proxmox-ами


![image](https://github.com/user-attachments/assets/6e07296e-dd25-41b8-9da3-3e6c1174b091)


Работает. Но есть одна штука о которой писал ранее и напомню еще раз: файл /etc/frr/frr.conf конфигурирается из вэб-интерфейса proxmox, а значит если мы в нем что-то наконфигурируем и нажмем apply, то сразу же потеряем настроки ospf. И их придется восстанавливать руками. Можно предложить такие решение этой проблемы:
 - статические маршруты в /etc/network/intrfaces (т.е. ospf мы не настраиваем)
 - ограничение прав доступа к конфигарированию SDN в вэб-интерфейсе проксмомкса
 - задние ограничений прав записи для файла /etc/frr/frr.conf
 - помочь материально разрабам, чтоб они написали underlay в графике

Теперь приступим к проверке.
...и признаюсь сразу - не работает. После нескольких дней борьбы когда я убал ospf, и убрал в графике пиринг к lo интерфейсом, добавил сетку назначения в vnet-subnets  - оно завелось. OSPF я убрал потому, что надоело много-много раз перенастраивать vtysh вручную.
Вот настройки контейнера 1:

![image](https://github.com/user-attachments/assets/3734fe70-21c2-48da-8e10-02d3ab8c60c7)

Вот настройки контейнера 2:

![image](https://github.com/user-attachments/assets/ad557de4-360c-4ca2-a008-1f5d315b0b26)

Теперь пошло:

![image](https://github.com/user-attachments/assets/338754d8-2425-4314-ada8-82baaf8c9335)

Я не могу сказать, в чем тут проблема. Возможно на реальном релезе будет работать нормально - потому не буду редактировать текст выше.
Еще раз правки, после которых пошли пинги:

1. Обе сети указаны вручную

![image](https://github.com/user-attachments/assets/34f2bc06-264b-4bd1-9c3b-34b0c814f4cc)

2. Пиринг на реально существующие интерфейсы:

![image](https://github.com/user-attachments/assets/4a01c8d2-2707-4bed-942c-a201e8b4a2b5)

Рассмортим show команды для vtysh:

![image](https://github.com/user-attachments/assets/62173390-7a8a-4cad-b934-cab6c9ae89e4)

![image](https://github.com/user-attachments/assets/33de697f-a9b9-4ec2-be87-7c52fe00df8c)

![image](https://github.com/user-attachments/assets/15764781-8b16-4e2f-a3a4-99c5b49d226e)

![image](https://github.com/user-attachments/assets/e02db2f0-40b3-4b36-b2dd-ec55ba7147e8)


Маршрут к хосту 172.17.0.1 обозначен как локальный, а к 172.17.0.2/32 пришедший по BGP. Я понимаю, что даренному коню в зубы не смотрят, но что это? :) 

Вот дамп, видно, что все ok:

![image](https://github.com/user-attachments/assets/d08a6df0-b804-4a1b-876e-e8df3c4aabb2)

В заключение этой лабы попробуем зашифровать трафик. Как говорил эстонцец в анекдоте с дохлой вороной: "Пригодиццца". А нам реально таки пригодится:

Изменим mtu на 1370:



apt install strongswan

Открываем /etc/ipsec.conf

Дописываем:

conn %default
    ike=aes256-sha1-modp1024!  # the fastest, but reasonably secure cipher on modern HW
    esp=aes256-sha1!
    leftfirewall=yes           # this is necessary when using Proxmox VE firewall rules

conn output
    rightsubnet=%dynamic[udp/4789]
    right=%any
    type=transport
    authby=psk
    auto=route

conn input
    leftsubnet=%dynamic[udp/4789]
    type=transport
    authby=psk
    auto=route

  Генерируем ключ:

  openssl rand -base64 128

  И добавляем сгенерированный ключ в /etc/ipsec.secrets

  



























