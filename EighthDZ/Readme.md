# Доманшяя работа №8
## VxLAN. Оптимизация таблиц маршрутизации // ДЗ

Для выполнения работы скопирован стенд из домашней работы №7, к нему добален бордер 6.6.6.6 выступающий в роли шлюза по умолчанию.
![image](https://github.com/user-attachments/assets/8e5166fa-5f9f-488a-92f0-d3c37028b093)

Все хосты на исключением 4.4.4.4 и 6.6.6.6 не притерпели изменений конфигурации.
Конфиг бордера:

![image](https://github.com/user-attachments/assets/0dd4c078-3ccb-4d41-8733-8c3cb5f12199)


Рассмотрим конфиг 4.4.4.4:

![image](https://github.com/user-attachments/assets/8a7169a0-546c-4428-bbe7-c0dd2fd83b23)
![image](https://github.com/user-attachments/assets/f8e0be77-050c-4b6a-8749-e53707f0cd77)

В конфиге мы видим, что прописан 0.0.0.0/0 маршртут через 10.0.0.1 (6.6.6.6)
А также настроена редистрибьюция данного маршрута в evpn. Теперь все роутеры в пределах даннного vrf получат шлюз последней надежды, убедимся в этом на примере лифа 5.5.5.5:

![image](https://github.com/user-attachments/assets/1440aa03-4ab8-4c40-b71e-c15d5cf2324b)

Проверим таблицу маршрутизации лифа 3-3-3-3:

![image](https://github.com/user-attachments/assets/359a563a-4832-4f40-b149-301ab1eb2e0f)

Проверим доступность внешнего маршрута из пользовательских сегментов:

![image](https://github.com/user-attachments/assets/8ccf6cf2-d341-4a90-b8c1-6dae0158b5e6)

![image](https://github.com/user-attachments/assets/c80ec4be-9c8d-4018-b3b7-9e38fd6f70e7)

Все в порядке
