# Devops-Netology


## ДЗ 3.4

1. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
поместите его в автозагрузку,
предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.

```
>Unit systemd /etc/systemd/system/node_exporter.service:

[Unit]
Description=Node Exporter Service
After=network.target

[Service]
User=nodeusr
Group=nodeusr
Type=simple
ExecStart=/usr/local/bin/node_exporter $OPTIONS_FROM_CONFIG
Environment=/etc/node_exporter/*.conf
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
```
В файле *.conf будет:
OPTIONS_FROM_CONFIG=--web.max-requests=40 --log.level=info
```

Процес нормально стартует после ребута ВМ:
``` 
 node_exporter.service - Node Exporter Service
     Loaded: loaded (/etc/systemd/system/node_exporter.service; disabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-08-19 10:35:47 UTC; 5s ago
   Main PID: 14173 (node_exporter)
      Tasks: 5 (limit: 1066)
     Memory: 2.5M
     CGroup: /system.slice/node_exporter.service
             └─14173 /usr/local/bin/node_exporter

Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=thermal_zone
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=time
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=timex
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=udp_queues
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=uname
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=vmstat
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=xfs
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.223Z caller=node_exporter.go:115 level=info collector=zfs
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.224Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
Aug 19 10:35:47 vagrant node_exporter[14173]: ts=2022-08-19T10:35:47.224Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
```
2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
```
node_cpu_seconds_total{cpu="1",mode="system"} --- Среднее количество процессорного времени, затрачиваемого в системном режиме, в секунду
node_filesystem_avail_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} --- Пространство файловой системы, доступное в байтах.
node_network_receive_bytes_total{device="eth0"} --- Средний сетевой трафик, полученный в секунду
node_memory_MemFree_bytes  --- Свободно ОЗУ в байтах
```

3.Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata).

Поставил, изучил графики, удобное приложение. Буду пользоваться на ВМ.
```
● netdata.service - netdata - Real-time performance monitoring
     Loaded: loaded (/lib/systemd/system/netdata.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-08-22 06:41:26 UTC; 2h 14min ago
       Docs: man:netdata
             file:///usr/share/doc/netdata/html/index.html
             https://github.com/netdata/netdata
   Main PID: 637 (netdata)
      Tasks: 22 (limit: 1066)
     Memory: 136.0M
     CGroup: /system.slice/netdata.service
             ├─ 637 /usr/sbin/netdata -D
             ├─ 868 /usr/lib/netdata/plugins.d/apps.plugin 1
             ├─1029 /usr/lib/netdata/plugins.d/nfacct.plugin 1
             └─2007 bash /usr/lib/netdata/plugins.d/tc-qos-helper.sh 1

Aug 22 06:41:26 vagrant systemd[1]: Started netdata - Real-time performance monitoring.

```

4.Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
```
Да, можно:
[   12.654135] 10:46:30.298739 main     VBoxService 6.1.34 r150636 (verbosity: 0) linux.amd64 (Mar 23 2022 00:46:49) release log

Также systemd умеет опоредлеять виртализацию: systemd-detect-virt
```

5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
```
root@vagrant:~# /sbin/sysctl -n fs.nr_open --- Максимальное количество файловых дескрипторов, которые могут быть открыты.
1048576
root@vagrant:~# ulimit -n    -  мягкое значение устанавливается после входа пользователя в систему, но процессу пользователей разрешено увеличивать ограничение до жесткого ограничения
```

6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter.
```
root@vagrant:~# unshare --fork --pid --mount-proc sleep 1h
```
root@vagrant:~# ps aux | grep sleep
root        6493  0.0  0.0   5480   580 pts/0    T    12:42   0:00 unshare --fork --pid --mount-proc sleep 1h
root        6494  0.0  0.0   5476   584 pts/0    S    12:42   0:00 sleep 1h
root        6496  0.0  0.0   6432   720 pts/0    S+   12:43   0:00 grep --color=auto sleep
```
root@vagrant:~# nsenter -t 6494 -p -m 
root@vagrant:/# ps
    PID TTY          TIME CMD
      1 pts/0    00:00:00 sleep
     13 pts/0    00:00:00 bash
     24 pts/0    00:00:00 ps
root@vagrant:/# 
```

7. Найдите информацию о том, что такое :(){ :|:& };:
```
для понятности заменим : именем f и отформатируем код.

f() {
  f | f &
}
f

таким образом это функция, которая параллельно пускает два своих экземпляра. Каждый пускает ещё по два и т.д. 
При отсутствии лимита на число процессов машина быстро исчерпывает физическую память и уходит в своп.

При запуске стабилизирует процесс:
cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope

Cgroups - это способ ограничить ресурсы процессов внутри конкретной cgroup. 
Задача находились в cgroup и достигла своего предела pid / task.
```

