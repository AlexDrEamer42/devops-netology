# Домашнее задание к занятию "3.5. Файловые системы"
1. Разрежённый файл — это файл, в котором последовательности нулевых байтов заменены на информацию об этих последовательностях (список дыр). Такие файлы используются для хранения контейнеров образов — дисков виртуальных машин, резервных копий дисков и разделов, и т.д. Использование разрежённых файлов экономит дисковое пространство, но требует дополнительных действий со стороны системного и прикладного ПО.
2. Жёсткие ссылки имеют те же права и владельца, что и объект, на который они ссылаются. Таким образом, у нескольких жёстких ссылок на один объект не могут быть разные права и владелец.
3. Создал ВМ с новой конфигурацией. Вывод команды `lsblk` содержит устройства `sdb` и `sdc` по 2.5 ГБ.
    ```
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    loop0                       7:0    0 61.9M  1 loop /snap/core20/1328
    loop1                       7:1    0 67.2M  1 loop /snap/lxd/21835
    loop2                       7:2    0 43.6M  1 loop /snap/snapd/14978
    sda                         8:0    0   64G  0 disk 
    ├─sda1                      8:1    0    1M  0 part 
    ├─sda2                      8:2    0  1.5G  0 part /boot
    └─sda3                      8:3    0 62.5G  0 part 
      └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm  /
    sdb                         8:16   0  2.5G  0 disk 
    sdc                         8:32   0  2.5G  0 disk
    ```
4. Выполним команду `sudo fdisk /dev/sdb`. Создадим таблицу разделов GPT и разделим первый диск на разделы.
    ```
    Device does not contain a recognized partition table.
    Created a new DOS disklabel with disk identifier 0x9d35b5be.
    
    Command (m for help): g
    Created a new GPT disklabel (GUID: 81C63C10-BB80-DB4E-99A6-A3A7CFE1AEA3).
    
    Command (m for help): n
    Partition number (1-128, default 1): 
    First sector (2048-5242846, default 2048): 
    Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242846, default 5242846): +2G
    
    Created a new partition 1 of type 'Linux filesystem' and of size 2 GiB.
    
    Command (m for help): n
    Partition number (2-128, default 2): 
    First sector (4196352-5242846, default 4196352): 
    Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242846, default 5242846): 
    
    Created a new partition 2 of type 'Linux filesystem' and of size 511 MiB.
    
    Command (m for help): w
    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.
    ```
    Проверяем:
    ```
    lsblk /dev/sdb
    ```
    ```
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sdb      8:16   0  2.5G  0 disk 
    ├─sdb1   8:17   0    2G  0 part 
    └─sdb2   8:18   0  511M  0 part
    ```
5. Выполним команду: 
    ```
    sudo sfdisk -d /dev/sdb | sudo sfdisk /dev/sdc
    ```
    ```
    Checking that no-one is using this disk right now ... OK

    Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
    Disk model: VBOX HARDDISK   
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Script header accepted.
    >>> Created a new GPT disklabel (GUID: 81C63C10-BB80-DB4E-99A6-A3A7CFE1AEA3).
    /dev/sdc1: Created a new partition 1 of type 'Linux filesystem' and of size 2 GiB.
    /dev/sdc2: Created a new partition 2 of type 'Linux filesystem' and of size 511 MiB.
    /dev/sdc3: Done.
    
    New situation:
    Disklabel type: gpt
    Disk identifier: 81C63C10-BB80-DB4E-99A6-A3A7CFE1AEA3
    
    Device       Start     End Sectors  Size Type
    /dev/sdc1     2048 4196351 4194304    2G Linux filesystem
    /dev/sdc2  4196352 5242846 1046495  511M Linux filesystem
    
    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.
    ```
    Проверяем:
    ```
    lsblk /dev/sdc
    ```
    ```
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sdc      8:32   0  2.5G  0 disk 
    ├─sdc1   8:33   0    2G  0 part 
    └─sdc2   8:34   0  511M  0 part 
    ```
6. Выполним команду:
    ```
    sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
    ```
    ```
    mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
    mdadm: size set to 2094080K
    Continue creating array? y
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md0 started.
    ```
    Проверяем:
    ```
    lsblk /dev/md0
    ```
    ```
    NAME MAJ:MIN RM SIZE RO TYPE  MOUNTPOINT
    md0    9:0    0   2G  0 raid1 
    ```
7. Выполним команду:
    ```
    sudo mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
    ```
    ```
    mdadm: chunk size defaults to 512K
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md1 started.
    ```
    Проверяем:
    ```
    lsblk /dev/md1
    ```
    ```
    NAME MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    md1    9:1    0 1017M  0 raid0 
    ```
8. Выполним команды:
    ```
    sudo pvcreate /dev/md0
    sudo pvcreate /dev/md1
    ```
    ```
      Physical volume "/dev/md0" successfully created.
      Physical volume "/dev/md1" successfully created.
    ```
    Проверяем:
    ```
    sudo pvdisplay
    ```
    ```
         --- Physical volume ---
         PV Name               /dev/sda3
         VG Name               ubuntu-vg
         PV Size               <62.50 GiB / not usable 0   
         Allocatable           yes 
         PE Size               4.00 MiB
         Total PE              15999
         Free PE               8000
         Allocated PE          7999
         PV UUID               x7S6t2-at3n-E9kU-cz28-gAH3-QU9H-vyVuNf
       
         "/dev/md0" is a new physical volume of "<2.00 GiB"
         --- NEW Physical volume ---
         PV Name               /dev/md0
         VG Name               
         PV Size               <2.00 GiB
         Allocatable           NO
         PE Size               0   
         Total PE              0
         Free PE               0
         Allocated PE          0
         PV UUID               rT93af-pNUp-TtHq-aOoM-pDEM-M1zf-NPqGV8
       
         "/dev/md1" is a new physical volume of "1017.00 MiB"
         --- NEW Physical volume ---
         PV Name               /dev/md1
         VG Name               
         PV Size               1017.00 MiB
         Allocatable           NO
         PE Size               0   
         Total PE              0
         Free PE               0
         Allocated PE          0
         PV UUID               oKzrGC-vV1R-XsbB-swdF-b96q-0Jjd-0LDhVi
    ```   
9. Выполним команду:
    ```
    sudo vgcreate vg01 /dev/md0 /dev/md1
    ```
    ```
      Volume group "vg01" successfully created
    ```
    Проверяем:
    ```
    sudo vgdisplay
    ```
    ```
      --- Volume group ---
      VG Name               ubuntu-vg
      System ID             
      Format                lvm2
      Metadata Areas        1
      Metadata Sequence No  2
      VG Access             read/write
      VG Status             resizable
      MAX LV                0
      Cur LV                1
      Open LV               1
      Max PV                0
      Cur PV                1
      Act PV                1
      VG Size               <62.50 GiB
      PE Size               4.00 MiB
      Total PE              15999
      Alloc PE / Size       7999 / <31.25 GiB
      Free  PE / Size       8000 / 31.25 GiB
      VG UUID               4HbbNB-kISH-fXeQ-qzbV-XeNd-At34-cCUUuJ
       
      --- Volume group ---
      VG Name               vg01
      System ID             
      Format                lvm2
      Metadata Areas        2
      Metadata Sequence No  1
      VG Access             read/write
      VG Status             resizable
      MAX LV                0
      Cur LV                0
      Open LV               0
      Max PV                0
      Cur PV                2
      Act PV                2
      VG Size               <2.99 GiB
      PE Size               4.00 MiB
      Total PE              765
      Alloc PE / Size       0 / 0   
      Free  PE / Size       765 / <2.99 GiB
      VG UUID               MV270P-rGph-Y1p6-Xgsx-An6t-Z8Xa-eaLnVX
    ```
10. Выполним команду:
    ```
    sudo lvcreate -L 100M vg01 /dev/md1
    ```
    ```
      Logical volume "lvol0" created
    ```
    Проверяем:
    ```
    sudo lvdisplay
    ```
    ```
    --- Logical volume ---
    LV Path                /dev/ubuntu-vg/ubuntu-lv
    LV Name                ubuntu-lv
    VG Name                ubuntu-vg
    LV UUID                mJ8K7e-F4uw-o8Sx-iwt0-JfLQ-Dpoh-E7lSU1
    LV Write Access        read/write
    LV Creation host, time ubuntu-server, 2022-06-07 11:41:15 +0000
    LV Status              available
    # open                 1
    LV Size                <31.25 GiB
    Current LE             7999
    Segments               1
    Allocation             inherit
    Read ahead sectors     auto
    - currently set to     256
    Block device           253:0
     --- Logical volume ---
    LV Path                /dev/vg01/lvol0
    LV Name                lvol0
    VG Name                vg01
    LV UUID                3e5JGH-0wQY-GUZi-Cp2v-rrDW-rema-3V9NXp
    LV Write Access        read/write
    LV Creation host, time vagrant, 2022-10-29 12:34:58 +0000
    LV Status              available
    # open                 0
    LV Size                100.00 MiB
    Current LE             25
    Segments               1
    Allocation             inherit
    Read ahead sectors     auto
    - currently set to     4096
    Block device           253:1
    ```
11. Выполним команду:
    ```
    sudo mkfs.ext4 /dev/vg01/lvol0
    ```
    ```
    mke2fs 1.45.5 (07-Jan-2020)
    Creating filesystem with 25600 4k blocks and 25600 inodes
    
    Allocating group tables: done                            
    Writing inode tables: done                            
    Creating journal (1024 blocks): done
    Writing superblocks and filesystem accounting information: done
    ```
12. Выполним команды:
    ```
    mkdir /tmp/new
    ```
    ```
    sudo mount /dev/vg01/lvol0 /tmp/new/
    ```
13. Выполним команду:
    ```
    sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
    ```
    ```
    --2022-10-29 12:42:17--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
    Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
    Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 22425533 (21M) [application/octet-stream]
    Saving to: ‘/tmp/new/test.gz’
    
    /tmp/new/test.gz  100%[===============================================================================================================>]  21.39M  2.63MB/s    in 8.5s    
    
    2022-10-29 12:42:26 (2.51 MB/s) - ‘/tmp/new/test.gz’ saved [22425533/22425533]
    ```
14. Вывод `lsblk`:
    ```
    NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    loop0                       7:0    0 61.9M  1 loop  /snap/core20/1328
    loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
    loop2                       7:2    0 43.6M  1 loop  /snap/snapd/14978
    loop3                       7:3    0 63.2M  1 loop  /snap/core20/1623
    loop4                       7:4    0   48M  1 loop  /snap/snapd/17336
    loop5                       7:5    0 67.8M  1 loop  /snap/lxd/22753
    sda                         8:0    0   64G  0 disk  
    ├─sda1                      8:1    0    1M  0 part  
    ├─sda2                      8:2    0  1.5G  0 part  /boot
    └─sda3                      8:3    0 62.5G  0 part  
      └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
    sdb                         8:16   0  2.5G  0 disk  
    ├─sdb1                      8:17   0    2G  0 part  
    │ └─md0                     9:0    0    2G  0 raid1 
    └─sdb2                      8:18   0  511M  0 part  
      └─md1                     9:1    0 1017M  0 raid0 
        └─vg01-lvol0          253:1    0  100M  0 lvm   /tmp/new
    sdc                         8:32   0  2.5G  0 disk  
    ├─sdc1                      8:33   0    2G  0 part  
    │ └─md0                     9:0    0    2G  0 raid1 
    └─sdc2                      8:34   0  511M  0 part  
      └─md1                     9:1    0 1017M  0 raid0 
        └─vg01-lvol0          253:1    0  100M  0 lvm   /tmp/new
    ```
15. Проверяем целостность файла:
    ```
    gzip -t /tmp/new/test.gz
    echo $?
    ```
    ```
    0
    ```
16. Выполним команду:
    ```
    sudo pvmove /dev/md1 /dev/md0
    ```
    Проверяем:
    ```
    lsblk
    ```
    ```
    NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    loop0                       7:0    0 61.9M  1 loop  /snap/core20/1328
    loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
    loop2                       7:2    0 43.6M  1 loop  /snap/snapd/14978
    loop3                       7:3    0 63.2M  1 loop  /snap/core20/1623
    loop4                       7:4    0   48M  1 loop  /snap/snapd/17336
    loop5                       7:5    0 67.8M  1 loop  /snap/lxd/22753
    sda                         8:0    0   64G  0 disk  
    ├─sda1                      8:1    0    1M  0 part  
    ├─sda2                      8:2    0  1.5G  0 part  /boot
    └─sda3                      8:3    0 62.5G  0 part  
      └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
    sdb                         8:16   0  2.5G  0 disk  
    ├─sdb1                      8:17   0    2G  0 part  
    │ └─md0                     9:0    0    2G  0 raid1 
    │   └─vg01-lvol0          253:1    0  100M  0 lvm   /tmp/new
    └─sdb2                      8:18   0  511M  0 part  
      └─md1                     9:1    0 1017M  0 raid0 
    sdc                         8:32   0  2.5G  0 disk  
    ├─sdc1                      8:33   0    2G  0 part  
    │ └─md0                     9:0    0    2G  0 raid1 
    │   └─vg01-lvol0          253:1    0  100M  0 lvm   /tmp/new
    └─sdc2                      8:34   0  511M  0 part  
      └─md1                     9:1    0 1017M  0 raid0
    ``` 
17. Выполним команду:
    ```
    sudo mdadm --fail /dev/md0 /dev/sdb
    ```
    ```
    mdadm: set /dev/sdb1 faulty in /dev/md0
    ```
    Проверяем:
    ```
    cat /proc/mdstat
    ```
    ```
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
    md1 : active raid0 sdc2[1] sdb2[0]
          1041408 blocks super 1.2 512k chunks
          
    md0 : active raid1 sdc1[1] sdb1[0](F)
          2094080 blocks super 1.2 [2/1] [_U]
          
    unused devices: <none>
    ```
18. Подтверждаем с помощью `dmesg`:
    ```
    dmesg | grep raid1
    ```
    ```
    [ 3814.847705] md/raid1:md0: Disk failure on sdb1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
    ```
19. Проверяем целостность файла:
    ```
    gzip -t /tmp/new/test.gz
    echo $?
    ```
    ```
    0
    ```
20. Выполняем команду `vagrant destroy`:
    ```
    ==> vagrant: A new version of Vagrant is available: 2.3.2 (installed version: 2.3.1)!
    ==> vagrant: To upgrade visit: https://www.vagrantup.com/downloads.html
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
    ==> default: Forcing shutdown of VM...
    ==> default: Destroying VM and associated drives...
    ```