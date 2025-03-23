# Доманшяя работа №3
## Построение Underlay сети(ISIS) // ДЗ
Топология сети:
![image](https://github.com/user-attachments/assets/6364a9a0-3407-4144-bcee-12e2883d46ff)

Для всех коммутаторов настроен isis с анонсом сетей /31, а также bfd между спайнами и лифами, и анансом interface vlan подсетей исп.для связей лифов с серверами.
Отдельно вынесен 1 лиф - коммутатор в "Area2", для проверки L2 связанности.
Конфиг коммутатора 1.1.1.1:

![image](https://github.com/user-attachments/assets/ef0d4afc-14c1-4787-9b92-3641c459faed)

Проверим таблицу маршрутизации:

![image](https://github.com/user-attachments/assets/fdc6807c-eb8f-4521-ace0-7ace2298ba6c)

Проверим доступность lo интерфейсов, а также ПК в Area2

![image](https://github.com/user-attachments/assets/966d50fb-1a87-4f9e-9873-a31f102b2742)

Посмотрим таблицу LSBD:

![image](https://github.com/user-attachments/assets/44434c3d-3512-4bc3-ae1e-f7071e33ad40)

Все остальные коммутаторы настроены аналогично согласно ip адресации на схеме сверху (за исключением interface vlan - там их нет).

