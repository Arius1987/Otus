# Доманшяя работа №7
## VxLAN. Аналоги VPC // ДЗ 

Схема сети:

![image](https://github.com/user-attachments/assets/d821ad62-dac3-4aac-acec-3710660b1e94)

Рассмотим конфиг спайна 1-1-1-1 выступающего в роли роут-рефлектора:
![image](https://github.com/user-attachments/assets/cb7ad9e5-4b0d-4311-8868-8818ab1c0970)

Выполним резервирование линков хоста 172.16.0.2/30 за счет мультихоуминг lacp подключения к лифам 3-3-3-3 и 4-4-4-4. 
Для этого рассмотим конфиг лифа: 3-3-3-3 (нужные нам настройки указаны в блоке interface port-channel 1):

![image](https://github.com/user-attachments/assets/d16c5a7b-ba92-41fa-9dfa-852539d85734)
![image](https://github.com/user-attachments/assets/0861743b-5948-4977-9384-109b1731ba1e)

Для лифа 4-4-4-4 настройки будут идентичны. 
Проверим состояние port-channel к хосту 172.16.0.2:

![image](https://github.com/user-attachments/assets/b6194d9e-2c3e-47ee-be8e-4eb596b5e583)

Говорит, что bundled - поверим на слово

Проверим evpn магруты на лифе 5-5-5-5:

![image](https://github.com/user-attachments/assets/3157dcdb-c684-417f-9826-019febf31653)


После чего проверить L3 ip связанность к хосту 172.16.0.2 из под 172.16.1.2:

![image](https://github.com/user-attachments/assets/8c81ce63-abc8-460a-b5f3-31d0a1fe4e54)

После чего эмулируем выход из строя лифа 3-3-3-3:

![image](https://github.com/user-attachments/assets/2a6a9978-b9ad-4da1-b49a-0cf2ce16c925)

И снова повторно проверяем ip связанность к 172.16.0.2:

![image](https://github.com/user-attachments/assets/bbb4dc4f-250a-463e-9faf-b7de364d3036)

Все работает.
