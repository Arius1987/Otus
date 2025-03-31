# Доманшяя работа №4
## Построение Underlay сети (iBGP) // ДЗ

![image](https://github.com/user-attachments/assets/536d4434-eb6e-4060-8add-81642c920b0d)

Конфиг спайна 1.1.1.1:

![image](https://github.com/user-attachments/assets/a70fc6ed-ba5f-42db-ab4c-d0d2ebeffcf5)

Таблица маршрутизации:

![image](https://github.com/user-attachments/assets/5a754b82-a9c1-4e87-ac3a-d37dc4aad580)

Таблица BGP соседества:

![image](https://github.com/user-attachments/assets/bab55db9-31f4-436c-a783-66817b189b0a)

Доступность сетей серверов:

![image](https://github.com/user-attachments/assets/f991708f-5d7a-40e8-b777-12e8152dcbef)

![image](https://github.com/user-attachments/assets/73da02c9-a431-4b82-a9c3-8b9c8b31866b)

В последнем скрине видим, что сервера недоступны, проблема решается настройкой ip sla на 1.1.1.1 или выключением vlan interface 2, 3 на свитче 11.11.11.11, чтоб сетка выпала из анонса BGP, после чего трафик пойдет через 10.10.10.10

![image](https://github.com/user-attachments/assets/f7690060-1def-4e52-87d5-ffe9fe83e182)

![image](https://github.com/user-attachments/assets/3fcc9305-c22f-412d-90e1-25699f183a2d)

Аналогично проблеме описанной выше


Конфиг лифа 10.10.10.10:


![image](https://github.com/user-attachments/assets/edb91554-3839-44e0-b7df-ca8195565601)


Таблица маршрутизации:

![image](https://github.com/user-attachments/assets/4d45a52b-0c79-4d2f-abd4-301b0c313109)

Пинги:

![image](https://github.com/user-attachments/assets/37fd555e-74b3-4798-9578-e66c826d3873)

ip адреса 172.16.0.3/29 и 172.16.1.3 пинговаться не будут, так как траффик пойдет через connected сетку с упавшим линком. 

![image](https://github.com/user-attachments/assets/8fdb861e-477e-4fa4-aebb-d08f6b83da45)

Так же проблема описана выше: к 172.16.2.3 трафик пойдет через 11.11.11.11 (указано в таблице маршрутизации) с упавшим connected линком, как и к 172.16.3.1 - лечим ip sla или выключением vlan interface 4,5 на 11.11.11.11







