## Отчет по Лабораторной работе №4 <br> "Эмуляция распределенной корпоративной сети связи, настройка iBGP, организация L3VPN, VPLS"
### Информация об отчёте

***Учебное заведение: Университет ИТМО***  
***Факультет: Инфокоммуникационные технологии***  
***Образовательная программа: Инфокоммуникационные технологии и системы связи***  
***Учебная группа: K33212***  
***Отчёт подготовил: Кардаков Максим Дмитриевич***  
***Дата: 24.12.2023***  

### Описание работы  

Компания "RogaIKopita Games" выпустила игру "Allmoney Impact", нагрузка на арендные сервера возрасли и вам поставлена задача стать LIR и организовать свою AS чтобы перенести все сервера игры на свою инфраструктуру. После организации вашей AS коллеги из отдела DEVOPS попросили вас сделать L3VPN между 3 офисами для служебных нужд. (Рисунок 1) Данный L3VPN проработал пару недель и коллеги из отдела DEVOPS попросили вас сделать VPLS для служебных нужд.

### Цель работы  

Изучить протоколы BGP, MPLS и правила организации L3VPN и VPLS.

### Ход работы  

**1. Схема связи** </br>

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/9093fbf1-b0b9-4ba3-9581-2d9eefac0e25)

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/57b7e9f6-debd-483c-adff-285212299693)

**2. Часть 1 - VRF. Настройка устройств**

Для начала создадим связь между устройствами. Также на всех устройствах был создан мост Loopback с уникальными ip. На примере роутеров HKI и SPB:

* HKI

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/3b7c5400-ce17-4aa0-8cc0-cfb94b8b90f6)

* SPB

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/e70aefed-490a-4961-a528-a7f16820ba77)



**3. VRF** </br>

На всех устройствах, связанных с ПК был создан VRF (VRF_DEVOPS)

* NY

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/3687929b-80d8-47d4-b823-17d30fc1a623)

* SVL

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/eba2bfe5-634e-4a32-b072-e3bb0518e018)

* SPB

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/26e307a4-be8c-49be-a33f-ba1005de6a06)


Соответственно для каждого из этих роутеров появилась новая таблица маршрутизации.

**3. OSPF** </br>

На примере роутера NY настраиваем OSPF

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/e6013ae1-f443-4258-a6c3-c4d8b356cd47)


![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/aa8f7b5e-41dd-4964-bfc1-adb17814834d)


![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/f451d52e-2377-4d9c-b4f5-1dfeba60913d)

Видно, что данный протокол не трогает наш VRF

**4. BGP** </br>

Далее, также на примере роутера NY, настроим iBGP

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/7fb291d6-066e-4562-89e2-1532efbf936b)

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/2b567f4c-28e0-4cc0-be24-aec2721bb716)

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/ca1bd0ac-844e-46a9-81fc-851d862cd7e3)

Router reflect мы настроим на роутерах LND и HKI 

После аналогичной настройке лондонском роутере мы увидим установленное соединение

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/2a92795c-52af-458e-be49-9bd088375162)

Точно также настроим остальные связи между роутерами
Для каждого роутера появятся соответствующие настройки

* HKI

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/16eca438-4e5f-465f-8476-ac964141daa1)

После настроим vrf на bgp для устройств, соединенных с пк

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/a67be009-ac7c-4e99-99f3-93757df4cf5a)


**5. MPLS** </br>

Для каждого роутера настроим MPLS. Пример для роутера NY

* NY

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/723a3a0a-a20f-4e9f-a1cf-ed2e26db8b2a)


![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/e0d59389-129a-4f02-b5d9-3f791816030f)


После настройки всех роутеров и их интерфейсов.

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/da18da65-2582-4a60-80de-7b6d53b75ce9)



После настройки router reflect (BGP) у нас обновится таблица VRF_DEVOPS

* NY

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/a5ffb79a-aa10-4895-b112-c753b7e6c15b)

* SVL

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/81e8f2f0-9c6c-4d15-aaf0-19da3d2bda9a)


* SPB

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/f9acc769-fc50-4d77-86a9-37897b5f96f9)

**4. Проверка** </br>

* PC3 - PC2; PC3 - PC1

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/a3329f48-08ec-4b77-8e62-4c216ab0506b)


* PC2 - PC3; PC2 - PC1

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/fe045f4b-3c81-46cb-a07f-264662f4b988)


* PC1 - PC2; PC1 - PC3

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/48404acb-62ba-41a6-b2dd-5a2781b7b89f)


**5. Часть 2 - VPLS. Настройка мостов**</br>

Разбираем таблицу vrf и настраиваем vpls. 

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/dd75e2c5-1eb4-4f7c-85ca-28e87db54305)

Для соответственного удаленного устройства создадим интерфейсы vpls


* NY

Создаем мост VPLS

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/f3056907-0004-4ac7-a4d3-d9c4b7c4046e)

И два интерфейса для удаленных устройств

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/23ba5746-7b81-4f0b-86e9-4f35882d67c6)

Далее мы свяжем их на других устройствах

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/1a80893c-6d34-444e-8483-0b7fee05f3cc)

Аналогично настрои еще 2 роутера

* SPB

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/43efffb8-b887-4a3e-ba6d-7034b150d91d)

* SVL

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/c6ac3d29-6a5b-4569-b2a4-9395def057e8)


**6. Проверка** </br>


* PC3 - PC2; PC3 - PC1

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/4b9a6a02-adb6-40d9-bc89-ae44a2541e53)

* PC2 - PC3; PC2 - PC1

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/967bdf38-603e-4ac0-b5e3-13d5ad39e3ac)


* Для NY

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/b6cfc52c-ffc9-4d5b-afac-b87579742216)


* PC1 - PC2; PC1 - PC3

![image](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/873a81c4-5e78-48f9-89a1-bf81f98b0534)



**7. Выводы**  

В ходе выполнения данной лабораторной работы мы на практике познакомились c эмуляцией распределенной корпоративной сети связи, настройкой iBGP, организацией L3VPN, VPLS
