=== ДЗ3.5

2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
```
Нет, жесткая ссылка (hard link) – это как дополнительное имя на существующий файл, inodes у файла и у жесткой ссылки совпадает. Т.е жесткая ссылка указывает на то же место, где находиться основной файл, в то же самое место на жестком диске. Все права, владелец и даты изменений сохраняются. 
```

4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
```
root@vagrant:~# fdisk -l /dev/sdb
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x6aa4edbf

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux
```

5.Используя sfdisk, перенесите данную таблицу разделов на второй диск.
```
sfdisk -d /dev/sdb > sdb-part.txt
sfdisk /dev/sdc < sdb-part.txt 

root@vagrant:~# fdisk -l /dev/sdc
Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x6aa4edbf

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux
```
6. Соберите mdadm RAID1 на паре разделов 2 Гб.
```
mdadm --create --verbose /dev/md0 --level=1  --raid-devices=2 /dev/sdb1 /dev/sdc1

root@vagrant:~# mdadm --detail /dev/md0 
/dev/md0:
           Version : 1.2
     Creation Time : Tue Aug 23 07:09:55 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Tue Aug 23 07:10:06 2022
             State : clean 
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : vagrant:0  (local to host vagrant)
              UUID : cbbc70a5:c2750a3d:2effb6b6:5a803576
            Events : 17

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1

```
7. Соберите mdadm RAID0 на второй паре маленьких разделов.
```
mdadm --create --verbose /dev/md0 --level=0  --raid-devices=2 /dev/sdb2 /dev/sdc2

root@vagrant:~# mdadm --detail /dev/md1 
/dev/md1:
           Version : 1.2
     Creation Time : Tue Aug 23 07:10:30 2022
        Raid Level : raid0
        Array Size : 1042432 (1018.00 MiB 1067.45 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Tue Aug 23 07:10:30 2022
             State : clean 
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

            Layout : -unknown-
        Chunk Size : 512K

Consistency Policy : none

              Name : vagrant:1  (local to host vagrant)
              UUID : 588fb2ce:37904d94:c9a70fb2:92eefc1a
            Events : 0

    Number   Major   Minor   RaidDevice State
       0       8       18        0      active sync   /dev/sdb2
       1       8       34        1      active sync   /dev/sdc2
```
8. Создайте 2 независимых PV на получившихся md-устройствах.

```
pvcreate /dev/md0
pvcreate /dev/md1

  --- Physical volume ---
  PV Name               /dev/md0
  VG Name               vg-raid
  PV Size               <2.00 GiB / not usable 0   
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              511
  Free PE               511
  Allocated PE          0
  PV UUID               UYJU7c-dy57-2Rq7-mAq4-ptdo-AkLE-2n7HFj
   
  --- Physical volume ---
  PV Name               /dev/md1
  VG Name               vg-raid
  PV Size               1018.00 MiB / not usable 2.00 MiB
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              254
  Free PE               254
  Allocated PE          0
  PV UUID               7uMyIu-hFIW-jl21-uAFB-11Rv-iChi-Hcvpf0
```
9. Создайте общую volume-group на этих двух PV.

```
vgcreate vg-raid /dev/md0 /dev/md1

--- Volume group ---
  VG Name               vg-raid
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
  VG UUID               svTqhs-8iko-Ni04-Reer-kWmB-9acX-Mt8Od4
```
10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
```
lvcreate -L 100M vg-raid /dev/md1

--- Logical volume ---
  LV Path                /dev/vg-raid/lvol0
  LV Name                lvol0
  VG Name                vg-raid
  LV UUID                DR2nB3-eTf4-ivmH-tMf0-sj2t-tkng-q3lRYf
  LV Write Access        read/write
  LV Creation host, time vagrant, 2022-08-23 07:48:09 +0000
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
11. Создайте mkfs.ext4 ФС на получившемся LV.
```
root@vagrant:~# mkfs.ext4 /dev/vg-raid/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```

12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.
```
mount /dev/vg-raid/lvol0 /tmp/new/

sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─vg--raid-lvol0      253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─vg--raid-lvol0      253:1    0  100M  0 lvm   /tmp/new
```

13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.
```

```
14. Прикрепите вывод lsblk.
```
root@vagrant:~# lsblk 
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 61.9M  1 loop  /snap/core20/1328
loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 43.6M  1 loop  /snap/snapd/14978
loop3                       7:3    0   47M  1 loop  /snap/snapd/16292
loop4                       7:4    0   62M  1 loop  /snap/core20/1611
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
  └─md1                     9:1    0 1018M  0 raid0 
    └─vg--raid-lvol0      253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─vg--raid-lvol0      253:1    0  100M  0 lvm   /tmp/new
```
15. Протестируйте целостность файла:
```
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
```
root@vagrant:~# pvmove /dev/md1 /dev/md0
  /dev/md1: Moved: 12.00%
  /dev/md1: Moved: 100.00%
root@vagrant:~# lsblk 
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 61.9M  1 loop  /snap/core20/1328
loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 43.6M  1 loop  /snap/snapd/14978
loop3                       7:3    0   47M  1 loop  /snap/snapd/16292
loop4                       7:4    0   62M  1 loop  /snap/core20/1611
loop5                       7:5    0 67.8M  1 loop  /snap/lxd/22753
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0  1.5G  0 part  /boot
└─sda3                      8:3    0 62.5G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
│   └─vg--raid-lvol0      253:1    0  100M  0 lvm   /tmp/new
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
│   └─vg--raid-lvol0      253:1    0  100M  0 lvm   /tmp/new
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
root@vagrant:~# 
```
17. Сделайте --fail на устройство в вашем RAID1 md.
```
root@vagrant:~# mdadm --detail /dev/md0 
/dev/md0:
           Version : 1.2
     Creation Time : Tue Aug 23 07:09:55 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Tue Aug 23 08:09:20 2022
             State : clean 
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : vagrant:0  (local to host vagrant)
              UUID : cbbc70a5:c2750a3d:2effb6b6:5a803576
            Events : 17

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1
```

```
root@vagrant:~# mdadm /dev/md0 --fail /dev/sdc1
```

```
mdadm: set /dev/sdc1 faulty in /dev/md0
root@vagrant:~# mdadm --detail /dev/md0 
/dev/md0:
           Version : 1.2
     Creation Time : Tue Aug 23 07:09:55 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Tue Aug 23 08:11:43 2022
             State : clean, degraded 
    Active Devices : 1
   Working Devices : 1
    Failed Devices : 1
     Spare Devices : 0

Consistency Policy : resync

              Name : vagrant:0  (local to host vagrant)
              UUID : cbbc70a5:c2750a3d:2effb6b6:5a803576
            Events : 19

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       -       0        0        1      removed

       1       8       33        -      faulty   /dev/sdc1
```

18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
```
[ 5151.694369] md/raid1:md0: Disk failure on sdc1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
```

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:
```
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
