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
  
<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A10%3A51.jpg" width=700>


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

<br/><br/> 

Запускаем Container Lab командой ```containerlab deploy```
  

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2021-16-13.png" width=700>
  

Лаба успешно запущена теперь можно получить схему топологии сети использую команду ```containerlab graph```


<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>


<br/><br/> 
  
Заходим на CHR с помощью telnet 

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2021-51-01.png" width=700>


<br/>
<br/> 
  
### Настройка роутера R01

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/Screenshot%20from%202023-10-28%2021-50-47.png" width=700>


<br/><br/> 
  
### Настройка SW01

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>


<br/><br/> 
  
### Настройка SW02.01

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>


<br/><br/> 
  
### Настройка SW02.02

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>


<br/><br/> 
  
### Результаты пингов

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>

<img src="https://github.com/ilyawa/2023_2024-introduction_in_routing-k33212-druzhinin_i_a/blob/main/images/lab1/IMAGE%202023-10-29%2019%3A13%3A26.jpg" width=700>

