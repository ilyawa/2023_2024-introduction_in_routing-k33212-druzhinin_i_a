University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2023/2024  
Group: K33212  
Author: Druzhinin Ilya Antonovich  
Lab: Lab2  
Date of create: 20.11.2023  
Date of finished: 26.11.2023  

## Лабораторная работа №2 "Эмуляция распределенной корпоративной сети связи, настройка статической маршрутизации между филиалами"

## Описание
В данной лабораторной работе вы первый раз познакомитесь с компанией "RogaIKopita Games" LLC которая занимается разработкой мобильных игр с офисами в Москве, Франкфурте и Берлине. Для обеспечения работы своих офисов "RogaIKopita Games" вам как сетевому инженеру необходимо установить 3 роутера, назначить на них IP адресацию и поднять статическую маршрутизацию. В результате работы сотрудник из Москвы должен иметь возможность обмениваться данными с сотрудником из Франкфурта или Берлина и наоборот.

## Цель работы
Ознакомиться с принципами планирования IP адресов, настройке статической маршрутизации и сетевыми функциями устройств.

## Ход работы

Описываем схему сети в файле lab2.clab.yaml согласно заданию  

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2012-19-11-1.png" width=1000>  

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2012-19-20.png" width=1000>  
<br/><br/> 

Запускаем Container Lab командой ```containerlab deploy```  

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2012-20-26-1.png" width=1000>  
<br/><br/> 

С помощью команды ```containerlab graph``` выведем схему топологию сети
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2012-21-30.png" width=1000>  
<br/><br/> 

### Настройка R01.MSK
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2013-42-08.png" width=1000>  

- с помощью команд ```/interface vlan``` настраиваем два новых интерфеса типа VLAN
- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью команд ```/ip pool``` добавляем новые пулы ip адресов
- команды ```/ip dhcp-server``` позволяют создать два новых dhcp сервера, связать с ними пулы адресов и вланы
- далее с помощью ```/ip address``` назначаем ip адреса для физических и vlan интерфейсов
- следующими командами добавляем новый DHCP клиент для интерфейса ether1
- добавляем новые сети и шлюзы по умолчанию для DHCP сервера
- устанавливаем имя системы R01

<br/><br/> 

### Настройка R01.FRT
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2014-42-24.png" width=1000>
<br/><br/> 

### Настройка R01.BRL
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2015-03-27.png" width=1000>
<br/><br/> 

### Настройка PC.MSK
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2015-43-21.png" width=1000>  

- с помощью команд ```/interface vlan``` настраиваем два новых интерфеса типа VLAN
- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью команд ```/ip pool``` добавляем новые пулы ip адресов
- команды ```/ip dhcp-server``` позволяют создать два новых dhcp сервера, связать с ними пулы адресов и вланы
- далее с помощью ```/ip address``` назначаем ip адреса для физических и vlan интерфейсов
- следующими командами добавляем новый DHCP клиент для интерфейса ether1
- добавляем новые сети и шлюзы по умолчанию для DHCP сервера
- устанавливаем имя системы R01

<br/><br/> 

### Настройка PC.FRT
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2015-57-32.png" width=1000>  
<br/><br/> 

### Настройка PC.BRL
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-08-48.png" width=1000> 
<br/><br/> 

### Результаты пингов
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-10-22.png" width=1000> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-13-05.png" width=1000> 
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab2/Screenshot%20from%202023-11-26%2016-14-04.png" width=1000>
<br/><br/> 

## Вывод
Таким образом, были изучены принципы планирования IP адресов, настройки статической маршрутизации и сетевые функции устройств.
