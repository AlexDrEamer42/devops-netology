# Домашнее задание к занятию "3.7. Компьютерные сети.Лекция 2"

1. Cписок доступных сетевых интерфейсов в Linux можно получить с помощью команды `ip link show` в Linux или `ipconfig` в Windows.
2. Для распознавания соседа по сетевому интерфейсу используется протокол LLPD. Для работы с протоколом в Linux требуется установить пакет `lldpd`.
Чтобы определить соседа, нужно запустить команду `lldpctl`.
3. Для разделения L2-коммутатора на несколько виртуальных сетей используется технология VLAN. Для работы с VLAN в Linux требуется установить пакет `vlan`.
Для управления VLAN используется команда `vconfig`. Чтобы настроить VLAN, нужно добавить конфигурацию в файл **etc/network/interfaces**. Пример конфигурации:
    ```
    auto vlan1234
    iface vlan1234 inet static
        address 192.168.10.100
        netmask 255.255.255.0
        vlan_raw_device enp1s0
    ```
4. В Linux существуют следующие типы агрегации интерфейсов (бондинга):
    
    - balance-rr
    - active-backup
    - balance-xor
    - broadcast
    - 802.3ad
    - balance-tlb
    - balance-alb
   
   Для балансировки используются режимы balance-rr, balance-xor, balance-tlb, balance-alb.  
   Пример конфига:
   ```
   auto bond0

   iface bond0 inet static
    address 10.31.1.5
    netmask 255.255.255.0
    network 10.31.1.0
    gateway 10.31.1.254
    slaves eth0 eth1
    bond-mode balance-rr
    bond-miimon 100
    bond-downdelay 200
    bond-updelay 200
    ```
5. Всего в сети с маской /29 8 адресов, из них 6 доступны для хостов. Из сети с маской /24 можно получить 32 подсети с маской /29.
Примеры подсетей /29 внутри сети 10.10.10.0/24 - 10.10.10.0/29, 10.10.10.8/29, 10.10.10.16/29.
6. Допустимо взять IP-адреса из подсети 100.64.0.0/26.
7. Чтобы проверить ARP-таблицу в Windows, нужно выполнить команду `arp -a`, в Linux - `ip neigh`.
Для очистки ARP-кеша в Windows используется команда `arp -d`, в Linux - `ip neigh flush all`. 
Чтобы удалить определённый IP-адрес, в Windows используется команда `arp -d <IP>`, в Linux - `ip neigh del <IP> dev <interface>`. 