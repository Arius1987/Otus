
Первая, самая простая лабораторная работа. В ней мы соединим два proxmox прямым линком и настроим vxlan между двумя lxc контейнерами с ip адресами: 10.0.0.1/24 и 10.0.0.2/24

![image](https://github.com/user-attachments/assets/493b6043-89a7-44d0-9567-3f48dfafdbe0)


Для настройки L2 vxlan в меню proxmox нужно зайти в меню конфигурирования кластера-SDN-Zones-Add-Vxlan:

![image](https://github.com/user-attachments/assets/3a4ee1fe-a999-4345-81c8-e15086d63428)

Далее создаем зону с любым названием, указываем ip адрера других гипервизоров с которыми будем устанавливать vxlan пиринг:

![image](https://github.com/user-attachments/assets/13ec5e41-4ab2-4311-8a69-88639ef4c12d)

Дальше идем в VNets, прорисываем nvi 20, задаем ранее созданную зону vxlzn1 и включаем поддержку vlan-ов. Да, в proxmox мы можем использовать траспорт vxlan для vlan (позже мы проверим, как это работает). Можно поставить галочку isolate ports - аналог private vlan в cisco - ограничение хождения трафика между виртуальными машинами

![image](https://github.com/user-attachments/assets/dce014f5-aa77-46df-ae64-b5705b7959ff)

После чего заходим в главное меню SDN и жмем "Apply"

![image](https://github.com/user-attachments/assets/1417cf61-0108-4d04-bb13-7d220832e316)


После чего заходим в настройки вирульной машины/контейнера и задаем в качетве сетевого адаптера новосозданный бридж и устанавливаем vlan 100:

![image](https://github.com/user-attachments/assets/e1fc9a19-c820-4274-a0e1-e21e34e2d3db)


Агалогично поступаем на втором гипервизоре (выполнив также все предыдущие настройки на нем):

![image](https://github.com/user-attachments/assets/d9872eff-4e11-4780-95bb-80a22f4e23c2)

Проверим ip связанность между контейнерами:

![image](https://github.com/user-attachments/assets/f62e5dbb-4cb1-4611-9345-240e7da1488d)

Работает. Изменим vlan таг на одном из контейнеров:

![image](https://github.com/user-attachments/assets/70a0b06d-0686-44b1-9cb7-5530d31ee8fb)

Пинг:

![image](https://github.com/user-attachments/assets/11761427-6f87-456f-90e4-c3a84d9d30de)

Пинги пропали. Теперь вернем наш старый, правильный vlan 100 и посмотрим дамп наших пингов:

![image](https://github.com/user-attachments/assets/6ffb7dc0-351c-406c-ab42-9d70229f1296)

Здесь мы видим underlay ip адреса гирвизоров 172.16.0.1 и 172.16.0.2, vxlan инкапсуляцию исп. vni 20, vlan 100 и overlay ip виртуалок 10.0.0.1 и 10.0.0.2

Как я писал в превью, для работы с vxlan proxmox использует пакет frr. Все настройки выполненные в вэб-интерфейсе оседают в конфигирационном файлах /etc/frr/frr.conf и /etc/network/interfaces.d/*
В нашем случае нас интересует файл /etc/network/interfaces.d/sdn. Давайте посмотрим, что внутри:

![image](https://github.com/user-attachments/assets/63ba36d3-a2c4-4acb-9b6b-3e60aad0b6a3)

Ничего интересного. Как я написал - это вводная, самая простая лабораторная работа




