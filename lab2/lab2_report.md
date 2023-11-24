## Отчёт о Лабораторной работе №1 <br>"Установка ContainerLab и развертывание тестовой сети связи"

### Информация об отчёте

***Учебное заведение: Университет ИТМО***  
***Факультет: Инфокоммуникационные технологии***  
***Образовательная программа: Инфокоммуникационные технологии и системы связи***  
***Учебная группа: K33212***  
***Отчёт подготовил: Кардаков Максим Дмитриевич***  
***Дата: 30.10.2023***  

### Описание работы

В данной лабораторной работе вы познакомитесь с инструментом ContainerLab, развернете тестовую сеть связи, настроите оборудование на базе Linux и RouterOS.

### Цель работы

Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.

### Ход работы

**1.** Во время подготовлений к данной лабораторной работе были установлены [**Docker**](https://www.docker.com/)(```sudo apt install docker-ce```) и **make**(```sudo apt install make```). Благодаря этому получилось склонировать репозиторий [hellt/vrnetlab](https://github.com/hellt/vrnetlab), в который позже был локально добавлен файл [chr-6.47.9.vmdk](https://download.mikrotik.com/routeros/6.47.9/chr-6.47.9.vmdk). Командой ```make docker-image```, исполненной в папке vrnetlab, был собран образ. После чего был установлен ContainerLab с помощью команды ```bash -c "$(curl -sL https://get.containerlab.dev)"```  
**2.** Была создана трехуровненая сеть связи классического предприятия, соответвующая данному в задании изображению ![sample sceme](https://itmo-ict-faculty.github.io/introduction-in-routing/education/labs2023_2024/lab1/3tiernetwork.png) <br> Файл создания и первичной конфигурации получившейся сети связи:
  ```
  name: lab1_scheme

  topology:
    nodes:
      R01.TEST:
        kind: vr-ros
        image: vrnetlab/vr-routeros:6.47.9
        mgmt-ipv4: 192.168.0.2
      
      SW01.L3.01.TEST:
        kind: vr-ros
        image: vrnetlab/vr-routeros:6.47.9
        mgmt-ipv4: 192.168.0.3

      SW02.L3.01.TEST:
        kind: vr-ros
        image: vrnetlab/vr-routeros:6.47.9
        mgmt-ipv4: 192.168.0.4

      SW02.L3.02.TEST:
        kind: vr-ros
        image: vrnetlab/vr-routeros:6.47.9
        mgmt-ipv4: 192.168.0.5

      PC1:
        kind: linux
        image: alpine:latest
        mgmt-ipv4: 192.168.0.6

      PC2:
        kind: linux
        image: alpine:latest
        mgmt-ipv4: 192.168.0.7

    links:
      - endpoints: ["R01.TEST:eth1","SW01.L3.01.TEST:eth1"]
      - endpoints: ["SW01.L3.01.TEST:eth2","SW02.L3.01.TEST:eth1"]
      - endpoints: ["SW02.L3.01.TEST:eth2","PC1:eth1"]
      - endpoints: ["SW01.L3.01.TEST:eth3", "SW02.L3.02.TEST:eth1"]
      - endpoints: ["SW02.L3.02.TEST:eth2", "PC2:eth1"]

  mgmt:
    network: static
    ipv4-subnet: 192.168.0.0/24
  ```

В данном файле мы объявляем название схемы сети (name), информацию о характере сети (mgmt) и два основных компонента - nodes и links, то есть узлы в сети и связи между ними.  

Результат деплоя данной схемы: ![Screenshot from 2023-10-30 09-20-06](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/05c9cc91-d9f9-4f23-8389-157dd6fd1148)

Получившееся изображение трёхуровневой сети связи: ![result](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/8d2cd628-a6e7-451e-bc85-330d6e0e756d)


**3.** Далее была проведена настройка IP-адресов и VLAN-ов, создание DHCP. Также были настроены имена устройств.<br>
  **Настройка Роутера R01.TEST**  
  ![Screenshot from 2023-10-30 09-30-19](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/8e89be97-92e5-4708-bb60-149987e99dd4)  
  - /interface vlan  
    Создано два виртуальных интерфейса (VLAN) на интерфейсе ether2:  
      vlan10 с идентификатором VLAN 10.  
      vlan20 с идентификатором VLAN 20.

  - /ip pool  
    Создано два IP-пула для выдачи адресов клиентам:  
      pool10 с диапазоном адресов от 192.168.10.10 до 192.168.10.254 для VLAN 10.  
      pool20 с диапазоном адресов от 192.168.20.10 до 192.168.20.254 для VLAN 20.

  - /ip dhcp-server  
    Создано два DHCP-сервера, каждый из которых обслуживает соответствующий VLAN:  
    server1 для VLAN 10 с использованием IP-пула pool10.  
    server2 для VLAN 20 с использованием IP-пула pool20.

  - /ip address  
    Назначены IP-адреса интерфейсам:  
    IP-адрес 172.16.228.138/30 для интерфейса ether1.  
    IP-адрес 192.168.10.1/24 для интерфейса vlan10.  
    IP-адрес 192.168.20.1/24 для интерфейса vlan20.

  - /ip dhcp-client  
    Включен DHCP-клиент на интерфейсе ether1.

  - /ip dhcp-server network  
    Устанавлены адреса сетей и шлюзы для DHCP-серверов:  
    Для server1 (VLAN 10) сеть 192.168.10.0/24 и шлюз 192.168.10.1.  
    Для server2 (VLAN 20) сеть 192.168.20.0/24 и шлюз 192.168.20.1.

  - /system identity  
    Устанавлено имя устройства R01.TEST.
  
  
  **Настройка Маршрутизатора SW01.L3.01.TEST**  
  ![Screenshot from 2023-10-30 09-59-21](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/32b1d27c-f757-48e4-8db0-09665e5e62da)

  - /interface bridge  
    Создано три моста (bridge) с именами bridge, bridge10 и bridge20.  
    Мосты используются для объединения интерфейсов VLAN на устройстве.

  - /interface vlan  
    Создано четыре виртуальных интерфейса VLAN, каждый из которых связан с интерфейсом ether2 (по-видимому, ether2 настроен для маршрутизации между VLAN):  
    vlan10 с идентификатором VLAN 10.  
    vlan20 с идентификатором VLAN 20.  
    vlan30 с идентификатором VLAN 10.  
    vlan40 с идентификатором VLAN 20.

  - /interface bridge port  
    Привязаны интерфейсы VLAN к соответствующим мостам:  
    vlan10 и vlan30 привязаны к bridge10.  
    vlan20 и vlan40 привязаны к bridge20.

  - /ip address  
    Назначен IP-адрес интерфейсу ether1 с адресом 172.16.228.138/30 и сетью 172.16.228.136.

  - /ip dhcp-client  
    Включены DHCP-клиенты на интерфейсах ether1, bridge10 и bridge20.

  - /system identity  
    Устанавлено имя устройства SW01.L3.01.TEST.
  
  **Настройка Маршрутизатора SW02.L3.01.TEST**  
  ![Screenshot from 2023-10-30 09-42-57](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/4066cb98-19f7-4e08-a99c-3419781e095c)  
  _Использованы примерно те же команды, что и при настройке SW01.L3.01.TEST_

  **Настройка Маршрутизатора SW02.L3.02.TEST**  
  ![Screenshot from 2023-10-30 09-49-18](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/3f8a5c63-86fa-40e0-9744-8a9b37f68e77)  
  _Использованы примерно те же команды, что и при настройке SW01.L3.01.TEST_

  **Результаты пингов, проверки локальной связности.**
  ![Screenshot from 2023-10-30 09-51-52](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/0e2fd07d-c31c-4439-8f35-8118a9f7d063)
  ![Screenshot from 2023-10-30 09-53-17](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/094bd32a-92b6-4cac-94e2-31b4187b9f76)
  ![Screenshot from 2023-10-30 09-54-05](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/5c389912-e618-4f17-b0fe-3dd266c9cbe1)
  ![Screenshot from 2023-10-30 09-54-53](https://github.com/shuNya19/2023_2024-introduction_in_routing-k33212-kardakov-m-d/assets/66511121/9a8c6a7b-2adb-476f-a6e1-2d9a218e9154)




