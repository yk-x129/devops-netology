# Devops-Netology


## ДЗ 3.3

1\. Какой системный вызов делает команда cd?

```

chdir("/tmp")

```

2\. Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.

```

/usr/share/misc/magic.mgc

/usr/share/misc/magic.mgc: symbolic link to ../../lib/file/magic.mgc

```

3\. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

```

Ищем файловый дискриптор процеса и обнуляем его:

cat /dev/null > /proc/$PID/fd/3

```

4.Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

```

Зомби не занимают памяти, но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом:

SER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND

vagrant 2786 0.0 0.0 0 0 pts/0 Z 13:05 0:00 \[ls\] &lt;defunct&gt;

```

5.В iovisor BCC есть утилита opensnoop. На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты?

```

vagrant@vagrant:~$ sudo /usr/sbin/opensnoop-bpfcc

PID COMM FD ERR PATH

640 irqbalance 6 0 /proc/interrupts

640 irqbalance 6 0 /proc/stat

640 irqbalance 6 0 /proc/irq/20/smp_affinity

640 irqbalance 6 0 /proc/irq/0/smp_affinity

640 irqbalance 6 0 /proc/irq/1/smp_affinity

640 irqbalance 6 0 /proc/irq/8/smp_affinity

640 irqbalance 6 0 /proc/irq/12/smp_affinity

640 irqbalance 6 0 /proc/irq/14/smp_affinity

640 irqbalance 6 0 /proc/irq/15/smp_affinity

1 systemd 12 0 /proc/619/cgroup

1 systemd 12 0 /proc/650/cgroup

1 systemd 12 0 /proc/386/cgroup

845 vminfo 6 0 /var/run/utmp

634 dbus-daemon -1 2 /usr/local/share/dbus-1/system-services

634 dbus-daemon 21 0 /usr/share/dbus-1/system-services

634 dbus-daemon -1 2 /lib/dbus-1/system-services

634 dbus-daemon 21 0 /var/lib/snapd/dbus-1/system-services/

```

6\. Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.

```

struct utsname {

char sysname\[\]; /* название операционной системы

(например, «Linux») */

char nodename\[\]; /* имя в сети, зависящее от реализации */

char release\[\]; /* идентификатор выпуска ОС (например, «2.6.28») */

char version\[\]; /* версия ОС */

char machine\[\]; /* идентификатор аппаратного обеспечения */

#ifdef \_GNU\_SOURCE

char domainname\[\]; /* доменное имя NIS или YP */

#endif

};

/proc/version --- This string identifies the kernel version that is currently running. It includes the contents of /proc/sys/kernel/ostype, /proc/sys/kernel/osrelease and /proc/sys/kernel/version.

```

7\. Чем отличается последовательность команд через ; и через && в bash?

```

Если команды разделены ';' то они выполняются последовательно, не зависимо от кода завершения. В случае разделения команд '&&', следующая команда будет выполнена только если предыдущая вернёт код 0 (успешное завершение).

set -e Exit immediately if a command exits with a non-zero status. Поведение будет аналогично &&.

```

8\. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?

```

-e Exit immediately if a command exits with a non-zero status.

-u Treat unset variables as an error when substituting.

-x Print commands and their arguments as they are executed.

-o Set the variable corresponding to option-name:

pipefail the return value of a pipeline is the status of

the last command to exit with a non-zero status,

or zero if no command exited with a non-zero status

Данный режим полезно использовать при отладке сценариев.

```

9\. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе.

```

agrant@vagrant:~$ ps -o stat

STAT

Ss

R+

D uninterruptible sleep (usually IO)

I Idle kernel thread

R running or runnable (on run queue)

S interruptible sleep (waiting for an event to complete)

T stopped by job control signal

t stopped by debugger during the tracing

W paging (not valid since the 2.6.xx kernel)

X dead (should never be seen)

Z defunct ("zombie") process, terminated but not reaped by its parent

< high-priority (not nice to other users)

N low-priority (nice to other users)

L has pages locked into memory (for real-time and custom IO)

s is a session leader

l is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)

\+ is in the foreground process group

Ss -- Спящий (ожидающий) процесс являющийся лидером сессии

R+ -- запущенный в фоне процесс.

```
