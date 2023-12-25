University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2023/2024  
Group: K33212  
Author: Druzhinin Ilya Antonovich  
Lab: Lab4   
Date of create: 24.12.2023  
Date of finished: 25.12.2023  

## Лабораторная работ №4 "Эмуляция распределенной корпоративной сети связи, настройка iBGP, организация L3VPN, VPLS"

## Описание
Компания "RogaIKopita Games" выпустила игру "Allmoney Impact", нагрузка на арендные сервера возрасли и вам поставлена задача стать LIR и организовать свою AS чтобы перенести все сервера игры на свою инфраструктуру. После организации вашей AS коллеги из отдела DEVOPS попросили вас сделать L3VPN между 3 офисами для служебных нужд. (Рисунок 1) Данный L3VPN проработал пару недель и коллеги из отдела DEVOPS попросили вас сделать VPLS для служебных нужд.


## Цель работы
Изучить протоколы BGP, MPLS и правила организации L3VPN и VPLS.

## Ход работы  
### Задание 1  

Описываем схему сети в файле lab4.clab.yaml согласно заданию  

```
name: lab4

mgmt:
  network: statics4
  ipv4-subnet: 170.20.20.0/24

topology:
  
  nodes:
    R01.NY: 
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9 
      mgmt-ipv4: 170.20.20.12

    R01.LND:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.13

    R01.HKI:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.14

    R01.SPB:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.15

    R01.LBN:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.16
    
    R01.SVL:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.17

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.18
    
    PC2:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.19

    PC3:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 170.20.20.20

  links:
    - endpoints: ["PC1:eth1","R01.SPB:eth2"]
    - endpoints: ["PC2:eth1","R01.NY:eth1"]
    - endpoints: ["PC3:eth1","R01.SVL:eth2"]
    - endpoints: ["R01.NY:eth2","R01.LND:eth1"]
    - endpoints: ["R01.LND:eth2","R01.HKI:eth1"]
    - endpoints: ["R01.LND:eth3","R01.LBN:eth1"]
    - endpoints: ["R01.HKI:eth2","R01.SPB:eth1"]
    - endpoints: ["R01.HKI:eth3","R01.LBN:eth2"]
    - endpoints: ["R01.LBN:eth3","R01.SVL:eth1"]
```  
<br></br>
Запускаем Container Lab командой ```containerlab deploy```  

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/inspect.png" width=1000>  
<br/><br/> 

С помощью команды ```containerlab graph``` выведем схему топологию сети
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/lab-topo.png" width=1000>  
<br/><br/> 

Дадим определение основным понятиям данной лабораторной работы

- BGP () - (Border Gateway Protocol) - это протокол маршрутизации, который используется для обмена информацией между автономными системами (AS) в Интернете. BGP позволяет определять наилучший маршрут для передачи данных между различными AS и управлять трафиком между ними.
- iBGP () - (Interior Border Gateway Protocol) - это внутренний протокол BGP, который используется для обмена информацией между маршрутизаторами внутри одной и той же автономной системы (AS). iBGP позволяет распространять информацию о маршрутах между различными точками AS, что упрощает маршрутизацию трафика внутри AS.
- L3VPN () - (Layer 3 Virtual Private Network) - это тип виртуальной частной сети, который использует протоколы маршрутизации на уровне 3 OSI модели для передачи данных между удаленными сетями. L3VPN позволяет создавать изолированные сети, которые могут использоваться для передачи конфиденциальной информации между организациями или филиалами. В отличие от L2VPN, которая работает на уровне 2 OSI модели, L3VPN предоставляет более гибкие возможности настройки маршрутизации и контроля трафика.
- VPLS (Virtual Private LAN Service) - это технология, которая позволяет создавать виртуальные локальные сети (VLANs) на основе MPLS-сети. VPLS используется для объединения удаленных локальных сетей в одну единую сеть.
- MPLS (Multiprotocol Label Switching) - это технология, которая используется для ускорения передачи данных в сетях путем добавления меток (labels) к пакетам данных. MPLS позволяет обрабатывать трафик более эффективно и быстро, уменьшая задержки и повышая пропускную способность.
- VRF (Virtual Routing and Forwarding)— это технология, имеющаяся в маршрутизаторах сетей IP, которая позволяет нескольким экземплярам таблицы маршрутизации сосуществовать в маршрутизаторе и работать одновременно






<br/><br/> 
### Настройка R01.NY
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/ny1.png" width=1000>  

- с помощью команд ```/interface bridge``` создадим виртуальные интерфейсы EoMPLS для транспорта MPLS трафика в ethernet сети и Lo0 для создания loopback
- с помощью команд ```/routing bgp instance``` создадим и настроим протокол BGP на маршрутизаторе MikroTik. Экземпляр BGP позволяет маршрутизатору обмениваться информацией о маршрутах с другими маршрутизаторами, работающими по протоколу BGP.
- с помощью команд ```/routing ospf instance``` настроим работу OSPF на маршрутизаторе
- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью команд ```/ip address``` назначим IP адреса на физических интерфейсах
- с помощью команд ```/ip route vrf``` создадим виртуальную таблицу маршрутизации на роутере
- с помощью команд ```/ip dhcp-client``` настроим работу DHCP
- с помощью команд ```/mpls ldp interface``` настроим интерфейс, который будет использоваться для распростронения меток MPLS
- далее с помощью ```/system identity``` задаем имя для роутера


<br/><br/> 

### Настройка R01.LND
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/lnd.png" width=1000>
<br/><br/> 

### Настройка R01.HKI
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/hki.png" width=1000>
<br/><br/> 

### Настройка R01.SPB
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/spb1.png" width=1000>  
<br/><br/> 

### Настройка R01.LBN
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/lbn.png" width=1000>  
<br/><br/> 

### Настройка R01.SVL
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/svl1.png" width=1000> 
<br/><br/> 


### Проверка связности VRF
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/ny-print.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/spb-print.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/svl-print.png" width=700>
<br/><br/> 

### Результаты пингов
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/ny-ping2.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/ny-ping3.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/spb-ping1.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/spb-ping2.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/svl-ping1.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/svl-ping2.png" width=700> 
<br/><br/> 

### Задание 2  
Для выполнения 2го задания лабораторной требуется предварительно разобрать VRF на роутерах NY, SPB, SVL, добавить на эти роутеры бриджы vpls и настроить ip адресацию на пк.

### Настройка R01.NY
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/ny.png" width=1000>  

- с помощью команд ```/interface bridge``` создадим виртуальные интерфейсы EoMPLS для транспорта MPLS трафика в ethernet сети и Lo0 для создания loopback
- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью команд ```/routing ospf instance``` настроим работу OSPF на маршрутизаторе
- с помощью команд ```/routing bgp instance```  настроим работу BGP на маршрутизаторе
- с помощью команд ```/routing bgp instance vrf``` настроим работу BGP для работы в рамках вирутальной таблицы
- с помощью команд ```/ip address``` назначим IP адреса на физических интерфейсах
- с помощью команд ```/ip dhcp-client``` настроим работу DHCP
- с помощью команд ```/routing ospf network``` конфигурируем сетевые настройки OSPF
- с помощью команд ```/mpls ldp interface``` настроим интерфейс, который будет использоваться для распростронения меток MPLS
- далее с помощью ```/system identity``` задаем имя для роутера


<br/><br/> 

### Настройка R01.SPB
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/spb.png" width=1000>  
<br/><br/> 

### Настройка R01.SVL
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/svl.png" width=1000> 
<br/><br/> 

### Настройка PC1
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/pc1.png" width=1000> 
<br/><br/> 

### Настройка PC2
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/pc2.png" width=1000> 
<br/><br/> 

### Настройка PC3
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/pc3.png" width=1000> 
<br/><br/> 

### Результаты пингов
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/pc1-ping.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/pc2-ping.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab4/pc3-ping.png" width=700> 
<br/><br/> 

## Вывод
Таким образом, были изучены протоколы BGP, MPLS и правила организации L3VPN и VPLS.
