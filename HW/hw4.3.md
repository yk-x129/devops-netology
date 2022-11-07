# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

### Ваш скрипт:
```json
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            },
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import socket
import yaml
import json
dns_list_name=["drive.google.com", "mail.google.com", "google.com"]
dns_list_ipaddress=["0", "0", "0"]
for i in range(0, 3):
                dns_list_ipaddress[i]=socket.gethostbyname(dns_list_name[i])
                print(dns_list_name[i],dns_list_ipaddress[i])
check = 0
dict_yaml = [{'Service': dns_list_name[0], 'ip_adress': dns_list_ipaddress[0]},
                                                {'Service': dns_list_name[1], 'ip_adress': dns_list_ipaddress[1]},
                                                {'Service': dns_list_name[2], 'ip_adress': dns_list_ipaddress[2]}]
with open(r'/home/vagrant/devops-netology/dns-list.yaml', 'w') as file:
                        documents = yaml.dump(dict_yaml, file)
jsonString = json.dumps(dict_yaml)
jsonFile = open(r'/home/vagrant/devops-netology/dns-list.json', "w")
jsonFile.write(jsonString)
jsonFile.close()
t = 0
while check < 200:
                for i in range(0, 3):
                                if dns_list_ipaddress[i] != socket.gethostbyname(dns_list_name[i]):
                                                t = t + 1
                                                print("EROR", dns_list_name[i],dns_list_ipaddress[i],socket.gethostbyname(dns_list_name[i]))
                                else:
                                                print(dns_list_name[i], dns_list_ipaddress[i])
                if t != 0:
                                t = 0
                                dict_yaml = [{'Service': dns_list_name[0], 'ip_adress': dns_list_ipaddress[0]},
                                                                                 {'Service': dns_list_name[1], 'ip_adress': dns_list_ipaddress[1]},
                                                                                 {'Service': dns_list_name[2], 'ip_adress': dns_list_ipaddress[2]}]

                                with open(r'/home/vagrant/devops-netology/dns-list.yaml', 'w') as file:
                                                documents = yaml.dump(dict_yaml, file)
                                jsonString = json.dumps(dict_yaml)
                                jsonFile = open(r'yml_file.json', "w")
                                jsonFile.write(jsonString)
                                jsonFile.close()
                check = check + 1
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~/devops-netology$ python3 hw4.3.py
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
drive.google.com 64.233.164.194
mail.google.com 173.194.73.18
google.com 173.194.220.100
vagrant@ubuntu-bionic:~/devops-netology$
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
vagrant@ubuntu-bionic:~/devops-netology$ cat dns-list.json
[{"Service": "drive.google.com", "ip_adress": "64.233.164.194"}, {"Service": "mail.google.com", "ip_adress": "173.194.73.18"}, {"Service": "google.com", "ip_adress": "173.194.220.100"}]
vagrant@ubuntu-bionic:~/
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
devops-netology$ cat dns-list.yaml
- {Service: drive.google.com, ip_adress: 64.233.164.194}
- {Service: mail.google.com, ip_adress: 173.194.73.18}
- {Service: google.com, ip_adress: 173.194.220.100}
vagrant@ubuntu-bionic:~/devops-netology$
```
