University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2023/2024  
Group: K33212  
Author: Druzhinin Ilya Antonovich  
Lab: Lab2  
Date of create: 20.11.2023  
Date of finished: 26.11.2023  

## Лабораторная работ №3 "Эмуляция распределенной корпоративной сети связи, настройка OSPF и MPLS, организация первого EoMPLS"

## Описание
Наша компания "RogaIKopita Games" с прошлой лабораторной работы выросла до серьезного игрового концерна, ещё немного и они выпустят свой ответ Genshin Impact - Allmoney Impact. И вот для этой задачи они купили небольшую, но очень старую студию "Old Games" из Нью Йорка, при поглощении выяснилось что у этой студии много наработок в области компьютерной графики и совет директоров "RogaIKopita Games" решил взять эти наработки на вооружение. К сожалению исходники лежат на сервере "SGI Prism", в Нью-Йоркском офисе никто им пользоваться не умеет, а из-за короновируса сотрудники офиса из Санкт-Петерубурга не могут добраться в Нью-Йорк, чтобы забрать данные из "SGI Prism". Ваша задача подключить Нью-Йоркский офис к общей IP/MPLS сети и организовать EoMPLS между "SGI Prism" и компьютером инженеров в Санк-Петербурге.

## Цель работы
Изучить протоколы OSPF и MPLS, механизмы организации EoMPLS.

## Ход работы

Описываем схему сети в файле lab3.clab.yaml согласно заданию  

```
name: lab3

topology:
  
  nodes:
    R01.NY: 
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9 
      mgmt-ipv4: 172.16.0.2

    R01.LND:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.16.0.3

    R01.HKI:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.16.0.4

    R01.SPB:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.16.0.5

    R01.MSK:
      kind: vr-ros
      image: ubuntu:latest
      mgmt-ipv4: 172.16.0.6
    
    R01.LBN:
      kind: vr-ros
      image: ubuntu:latest
      mgmt-ipv4: 172.16.0.7

    SGI-Prism:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.16.0.8

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.16.0.9

  links:
    - endpoints: ["SGI-Prism:eth1","R01.NY:eth1"]
    - endpoints: ["R01.NY:eth2","R01.LND:eth1"]
    - endpoints: ["R01.LND:eth2","R01.HKI:eth1"]
    - endpoints: ["R01.NY:eth3","R01.LBN:eth1"]
    - endpoints: ["R01.LBN:eth2","R01.HKI:eth2"]
    - endpoints: ["R01.LBN:eth3","R01.MSK:eth1"]
    - endpoints: ["R01.MSK:eth2","R01.SPB:eth1"]
    - endpoints: ["R01.HKI:eth3","R01.SPB:eth2"]
    - endpoints: ["R01.SPB:eth3","PC1:eth1"]

mgmt:
  network: static
  ipv4-subnet: 172.16.0.0/24
```  
<br></br>
Запускаем Container Lab командой ```containerlab deploy```  

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2012-20-26-1.png" width=1000>  
<br/><br/> 

С помощью команды ```containerlab graph``` выведем схему топологию сети
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2012-21-30.png" width=1000>  
<br/><br/> 

### Настройка R01.MSK
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2013-42-08.png" width=1000>  

- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью команд ```/ip pool``` добавляем новый пулл ip адресов
- с помощью команд ```/ip address``` назначаем ip адреса интерфейсам ether
- далее с помощью ```/ip dhcp-client``` добавим новый dhcp клиент на роутере
- с помощью ```/ip route``` добавим новые пути в таблицу маршрутизации на роутере

<br/><br/> 

### Настройка R01.FRT
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2014-42-24.png" width=1000>
<br/><br/> 

### Настройка R01.BRL
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2015-03-27.png" width=1000>
<br/><br/> 

### Настройка PC.MSK
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2015-43-21.png" width=1000>  

- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- далее с помощью ```/ip dhcp-client``` добавим новый dhcp клиент на роутере
- с помощью ```/ip route``` добавим новые пути в таблицу маршрутизации на роутере

<br/><br/> 

### Настройка PC.FRT
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2015-57-32.png" width=1000>  
<br/><br/> 

### Настройка PC.BRL
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-08-48.png" width=1000> 
<br/><br/> 

### Результаты пингов
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-10-22.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-13-05.png" width=700> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-14-04.png" width=700>
<br/><br/> 

## Вывод
Таким образом, были изучены принципы планирования IP адресов, настройки статической маршрутизации и сетевые функции устройств.
