
Первая, самая простая лабораторная работа. В ней мы соединим два proxmox прямым линком и настроим vxlan между двумя lxc контейнерами с ip адресами: 10.0.0.1/24 и 10.0.0.2/24

![image](https://github.com/user-attachments/assets/493b6043-89a7-44d0-9567-3f48dfafdbe0)


Для настройки L2 vxlan в меню proxmox нужно зайти в меню конфигурирования кластера-SDN-Zones-Add-Vxlan:
![image](https://github.com/user-attachments/assets/3a4ee1fe-a999-4345-81c8-e15086d63428)

Далее создаем зону с любым названием, указываем ip адрера других гипервизоров с кторорыми будем устанавливать vxlan пиринг


![image](https://github.com/user-attachments/assets/13ec5e41-4ab2-4311-8a69-88639ef4c12d)

Дальше идем в VNets, прорисываем nvi 20, задаем ранее созданную зону vxlzn1 и включаем поддержку vlan-ов. Да, в proxmox мы можем использовать траспорт xvlan для vlan (позже мы проверим, как это работает). Можно поставить галочку isolate ports - аналог private vlan в cisco - ограничение хождение трафика между виртуальными машинами
![image](https://github.com/user-attachments/assets/dce014f5-aa77-46df-ae64-b5705b7959ff)






![image](https://github.com/user-attachments/assets/dfd0ffa5-61b6-435e-98ba-2e4db8a5437b)

![image](https://github.com/user-attachments/assets/d9872eff-4e11-4780-95bb-80a22f4e23c2)

