University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2023/2024  
Group: K33212  
Author: Druzhinin Ilya Antonovich  
Lab: Lab1  
Date of create: 20.10.2023  
Date of finished: 29.10.2023  

### Лабораторная работа 1 "Установка ContainerLab и развертывание тестовой сети связи"

## Описание

В данной лабораторной работе вы познакомитесь с инструментом ContainerLab, развернете тестовую сеть связи, настроите оборудование на базе Linux и RouterOS.

## Цель работы

Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.

## Ход работы

* на рабочий компьютер был установлен докер
* также был установлен ```make``` и склонирован проект hellt/vrnetlab
* в папку routeros был зашружен файл https://download.mikrotik.com/routeros/6.47.9/chr-6.47.9.vmdk
* с помощью ```make docker-image``` собран образ
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A10%3A51.jpg" width=900>



