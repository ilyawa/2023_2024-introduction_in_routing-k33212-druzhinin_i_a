University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)  
Year: 2023/2024  
Group: K33212  
Author: Druzhinin Ilya Antonovich  
Lab: Lab1  
Date of create: 20.10.2023  
Date of finished: 29.10.2023  

## Лабораторная работа 1 "Установка ContainerLab и развертывание тестовой сети связи"

## Описание

В данной лабораторной работе вы познакомитесь с инструментом ContainerLab, развернете тестовую сеть связи, настроите оборудование на базе Linux и RouterOS.

## Цель работы

Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.

## Ход работы

* на рабочий компьютер был установлен докер
* также был установлен ```make``` и склонирован проект hellt/vrnetlab
* в папку routeros был зашружен файл chr-6.47.9.vmdk
* с помощью ```make docker-image``` собран образ
  
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A10%3A51.jpg" width=1000>


* ContainerLab был установлен из официального репозитория с помощью скрипта ```# download and install the latest release (may require sudo)
bash -c "$(curl -sL https://get.containerlab.dev)"```

Создаем yaml файл с описанием топологии лабы

```
name: lab1

topology:
  nodes:
    R01:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.0.2

    PC1:
      kind: linux
      image: alpine:latest
      cmd: sleep infinity
      mgmt-ipv4: 172.20.0.3

    PC2:
      kind: linux
      image: alpine:latest
      cmd: sleep infinity
      mgmt-ipv4: 172.20.0.4
      
    SW01.L3.01:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.0.5

    SW02.L3.01:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.0.6

    SW02.L3.02:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt-ipv4: 172.20.0.7

  links:
      - endpoints: ["R01:eth1", "SW01.L3.01:eth1"]
      - endpoints: ["SW01.L3.01:eth2", "SW02.L3.01:eth1"]
      - endpoints: ["SW02.L3.01:eth2", "PC1:eth1"]
      - endpoints: ["SW01.L3.01:eth3", "SW02.L3.02:eth1"]
      - endpoints: ["SW02.L3.02:eth2", "PC2:eth1"]
      
mgmt:
  network: static
  ipv4-subnet: 172.20.0.0/24
```

Отдельным пунктом конфигурируем mgmt сеть,  которая позволит нам подключитиься к сетевым устройствам для их настройки


Запускаем Container Lab командой ```containerlab deploy```
  
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2021-16-13.png" width=1000>
  

Лаба успешно запущена теперь можно получить схему топологии сети использую команду ```containerlab graph```

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=1000>

<br/><br/> 
  
Заходим на CHR с помощью telnet 

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2021-51-01.png" width=1000>


<br/>
<br/> 
  
## Настройка роутера R01

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2021-50-47.png" width=1000>

- с помощью команд ```/interface vlan``` настраиваем два новых интерфеса типа VLAN
- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью команд ```/ip pool``` добавляем новые пулы ip адресов
- команды ```/ip dhcp-server``` позволяют создать два новых dhcp сервера, связать с ними пулы адресов и вланы
- далее с помощью ```/ip address``` назначаем ip адреса для физических и vlan интерфейсов
- следующими командами добавляем новый DHCP клиент для интерфейса ether1
- добавляем новые сети и шлюзы по умолчанию для DHCP сервера
- устанавливаем имя системы R01


<br/> 
<br/> 
  
## Настройка SW01

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-15-42.png" width=700>

- с помощью ```/interface bridge``` добавляем два сетевых моста
- ```/interface vlan``` добавляем vlan и связываем их с физическими интерфейсами
- с помощью команд ```/interface wireless security-profiles``` настраиваем профиль безопасности по умолчанию
- с помощью ```/interface bridge port``` связываем мосты с виртуальными интерфейсами vlan
- конфигурируем dhcp клиент
- устанавливаем имя системы SW01
  
<br/>
<br/> 
  
## Настройка SW02.01

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-30-15.png" width=1000>

- создаем сетевой мост  
- конфигурируем vlan  
- настраиваем профиль безопасности  
- конфигурируем dhcp клиент  
- меняем имя системы  

<br/>
<br/> 
  
## Настройка SW02.02

<img src = "https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-52-37.png" width=1000>

<img src = "https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-52-48.png" width=1000>

- создаем сетевой мост  
- конфигурируем vlan  
- настраиваем профиль безопасности  
- конфигурируем dhcp клиент  
- меняем имя системы  

<br/>
<br/> 
  
## Результаты пингов

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-54-07-1.png" width=600>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-54-07.png" width=600>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-54-28.png" width=600>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-55-00.png" width=600>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2022-55-18.png" width=600>


