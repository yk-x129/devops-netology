# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"
## Обязательная задача 1:
Есть скрипт:
```shell
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```
Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная | Значение | Обоснование |
|------------|----------|-------------|
| c          | ???      | ???         |
| d          | ???	    | ???         |
| e          | ???      | ???         |

### Решение:
| Переменная | Значение | Обоснование                                                                        |
|------------|----------|------------------------------------------------------------------------------------|
| c          | a+b      | текстовое значение                                           						 |
| d          | 1+2	    | текстовая значение                                                                 |
| e          | 3        | за счет конструкции `$(())` производится математическая операция между переменными |

## Обязательная задача 2
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, 
записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). 
В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно 
уменьшается. Что необходимо сделать, чтобы его исправить:
```shell
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```
### Решение:
```shell
while ((1==1)) # добавлена закрывающая скобка.
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
		sleep 5   #добавлено для того чтобы проверка проходила с интервалом в 5 секунд чтобы лог не захламлялся.
	else break  #добавлено условие выхода из цикла
	fi
done
```
## Обязательная задача 3
Необходимо написать скрипт, который проверяет доступность трёх IP: 192.168.0.1, 173.194.222.113, 87.250.250.242 
по 80 порту и записывает результат в файл log. Проверять доступность необходимо пять раз для каждого узла.
### Решение:
```shell
#!/usr/bin/env bash
HOSTS=("192.168.0.1" "173.194.222.113" "87.250.250.242")
PORT=80
LOGFILE=./check.log
touch $LOGFILE
for i in {1..5}
do
  for h in ${HOSTS[@]}
  do
    curl $h:$PORT --connect-timeout 5
    RET=$?
    echo $(date):" "$h" status is "$RET >> $LOGFILE
  done
done
```
```shell
vagrant@ubuntu-bionic:~$ cat check.log
Mon Oct 31 14:57:30 UTC 2022: 192.168.0.1 status is 28
Mon Oct 31 14:57:30 UTC 2022: 173.194.222.113 status is 0
Mon Oct 31 14:57:30 UTC 2022: 87.250.250.242 status is 0
Mon Oct 31 14:57:35 UTC 2022: 192.168.0.1 status is 28
Mon Oct 31 14:57:35 UTC 2022: 173.194.222.113 status is 0
Mon Oct 31 14:57:35 UTC 2022: 87.250.250.242 status is 0
```
## Обязательная задача 4
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется 
недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.
### Решение:
```shell
#!/usr/bin/env bash
HOSTS=("173.194.222.113" "192.168.0.1" "87.250.250.242") # Поменял последовательность хостов для примера
PORT=80
LOGFILE=./check.log
touch $LOGFILE
while ((1==1))
do
  for h in ${HOSTS[@]}
  do
    curl $h:$PORT --connect-timeout 5
    if [ "$?" != 0 ]
    then
      echo $(date):" "$h" status is DOWN!" >> $LOGFILE
      exit 0
    fi
  done
done
```
```shell
vagrant@ubuntu-bionic:~$ cat check.log
Mon Oct 31 14:59:08 UTC 2022: 192.168.0.1 status is DOWN!
```
