# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | значение не будет присвоено, разные типы данных  |
| Как получить для переменной `c` значение 12?  | c = str(a) + b  |
| Как получить для переменной `c` значение 3?  | c = a + int(b)  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:

```python
#!/usr/bin/env python3

import os

path = os.getcwd()   #Переменная для указания пути до git
bash_command = ["cd " + path, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(os.path.abspath(prepare_result))
#        break
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~/devops-netology$ python3 hw4.2.py
/home/vagrant/devops-netology/HW/hw4.1.md
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import sys

path = sys.argv[1] # Путь до репо Git берём из аргумента скрипта
os.chdir(path)
bash_command = ["git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(path+prepare_result)
#        break
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~/devops-netology$ python3 hw4.2.2.py /home/vagrant/devops-netology
/home/vagrant/devops-netologyHW/hw4.1.md
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import socket
dns_list_name=["drive.google.com", "mail.google.com", "google.com"]
dns_list_ipaddress=["0", "0", "0"]
for i in range(0, 3):
        dns_list_ipaddress[i]=socket.gethostbyname(dns_list_name[i])
        print(dns_list_name[i],dns_list_ipaddress[i])
check = 0
while check < 10:
        for i in range(0, 3):
                if dns_list_ipaddress[i] != socket.gethostbyname(dns_list_name[i]):
                        print("EROR", dns_list_name[i],dns_list_ipaddress[i],socket.gethostbyname(dns_list_name[i]))
                else:
                        print(dns_list_name[i], dns_list_ipaddress[i])
        check = check + 1
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~/devops-netology$ python3 hw4.2.3.py
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
drive.google.com 108.177.14.194
mail.google.com 173.194.73.19
google.com 173.194.73.138
vagrant@ubuntu-bionic:~/devops-netology$
```
