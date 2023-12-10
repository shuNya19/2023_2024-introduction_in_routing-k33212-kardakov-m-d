## Отчёт о Лабораторной работе №2 <br>"Эмуляция распределенной корпоративной сети связи, настройка статической маршрутизации между филиалами"

### Информация об отчёте

***Учебное заведение: Университет ИТМО***  
***Факультет: Инфокоммуникационные технологии***  
***Образовательная программа: Инфокоммуникационные технологии и системы связи***  
***Учебная группа: K33212***  
***Отчёт подготовил: Кардаков Максим Дмитриевич***  
***Дата: 24.11.2023***  

### Описание работы

В данной лабораторной работе вы первый раз познакомитесь с компанией "RogaIKopita Games" LLC которая занимается разработкой мобильных игр с офисами в Москве, Франкфурте и Берлине. Для обеспечения работы своих офисов "RogaIKopita Games" вам как сетевому инженеру необходимо установить 3 роутера, назначить на них IP адресацию и поднять статическую маршрутизацию. В результате работы сотрудник из Москвы должен иметь возможность обмениваться данными с сотрудником из Франкфурта или Берлина и наоборот.

### Цель работы

Ознакомиться с принципами планирования IP адресов, настройке статической маршрутизации и сетевыми функциями устройств.

### Ход работы

**1. Схема связи** 

Была создана схема сети
```
name: lab2

mgmt:
  network: statics
  ipv4-subnet: 172.20.20.0/24

topology:
  nodes:
    R01.MSK:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.20.10

    R01.BRL:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.20.11

    R01.FRT:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.20.12

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.20.13

    PC2:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.20.14

    PC3:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.20.15

  links:
    - endpoints: ["R01.MSK:eth2", "R01.FRT:eth2"]
    - endpoints: ["R01.MSK:eth1", "R01.BRL:eth1"]
    - endpoints: ["R01.BRL:eth2", "R01.FRT:eth1"]
    - endpoints: ["R01.MSK:eth3", "PC1:eth3"]
    - endpoints: ["R01.FRT:eth3", "PC2:eth3"]
    - endpoints: ["R01.BRL:eth3", "PC3:eth3"]
```
![Screenshot from 2023-12-08 15-11-27](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/ea9880a3-39b0-4b05-af3f-1151bb9b9d0f)


**2. Роутеры** </br>
Далее была проведена настройка роутеров
Роутер Франкфурта
![Screenshot from 2023-12-09 12-56-09](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/91cfec59-06c3-492e-9ffb-934594b39591)

Роутер Москвы
![Screenshot from 2023-12-09 13-04-34](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/b881a472-c8b1-49cf-b9e7-056513d2d400)

Роутер Берлина
![Screenshot from 2023-12-09 13-16-45](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/1d139600-781f-455b-9751-46fa36acd753)

**3. Компьютеры** </br>
Далее была проведена настройка компьютеров
Компьютер Франкфурта
![Screenshot from 2023-12-09 13-24-35](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/ec4cc7e3-d053-4491-9c37-dfb5a124a14f)

Компьютер Москвы
![Screenshot from 2023-12-09 13-28-17](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/76ba0a68-1b72-48b5-9d5d-79d8afcfd27d)

Компьютер Берлина
![Screenshot from 2023-12-09 14-12-52](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/2e201dc7-b7c0-419b-b5b9-a8962f953b22)

**4. Проверка доступности** </br>
FRT -> MSK, FRT -> BRL
![Screenshot from 2023-12-09 17-59-44](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/bf9ba6c3-ec34-4c24-9438-e0267286f5e1)

MSK -> BRL, MSK -> FRT
![Screenshot from 2023-12-09 17-57-35](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/1047a8cf-20ce-453a-9228-41f1e814f50e)

BRL -> MSK, BRL -> FRT
![Screenshot from 2023-12-09 18-00-49](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/e9c13db3-cd54-4b1f-90b4-54dff1936d7a)
