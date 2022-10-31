=== ДЗ3.9

1. Установите Bitwarden плагин для браузера. Зарегистрируйтесь и сохраните несколько паролей.

![Иллюстрация к 1 заданию](HW/images/hw39_scr1.png)


2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden аккаунт через Google authenticator OTP.

![Иллюстрация к 2 заданию](HW/images/hw39_scr2.png)


3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
```
vagrant@ubuntu-bionic:~$ sudo a2enmod ssl
Considering dependency setenvif for ssl:
Module setenvif already enabled
Considering dependency mime for ssl:
Module mime already enabled
Considering dependency socache_shmcb for ssl:
Enabling module socache_shmcb.
Enabling module ssl.
See /usr/share/doc/apache2/README.Debian.gz on how to configure SSL and create self-signed certificates.
To activate the new configuration, you need to run:
  systemctl restart apache2
vagrant@ubuntu-bionic:~$ sudo systemctl restart apache2
vagrant@ubuntu-bionic:~$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
> -keyout /etc/ssl/private/apache-selfsigned.key \
> -out /etc/ssl/certs/apache-selfsigned.crt \
> -subj "/C=RU/ST=Moscow/L=Moscow/O=None/OU=None/CN=www.netology.com"
Can't load /home/vagrant/.rnd into RNG
139848585339328:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/vagrant/.rnd
Generating a RSA private key
.............................................................................................................................................+++++
...........................+++++
writing new private key to '/etc/ssl/private/apache-selfsigned.key'
vagrant@ubuntu-bionic:~$ cat /etc/apache2/sites-available/netology.conf
<VirtualHost *:443>
  ServerName netology_homework
  DocumentRoot /var/www/netology

  SSLEngine on
  SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
  SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>
vagrant@ubuntu-bionic:~$ sudo mkdir /var/www/netology
vagrant@ubuntu-bionic:~$ cat /var/www/netology/index.html
<H1>Test HTTPS</H1>
vagrant@ubuntu-bionic:~$ sudo a2ensite netology.conf
Enabling site netology.
To activate the new configuration, you need to run:
  systemctl reload apache2
vagrant@ubuntu-bionic:~$ sudo systemctl reload apache2
vagrant@ubuntu-bionic:~$ openssl s_client -connect localhost:443 -servername netology.com
CONNECTED(00000005)
depth=0 C = RU, ST = Moscow, L = Moscow, O = None, OU = None, CN = www.netology.com
verify error:num=18:self signed certificate
verify return:1
depth=0 C = RU, ST = Moscow, L = Moscow, O = None, OU = None, CN = www.netology.com
verify return:1
```
4. Проверьте на TLS уязвимости произвольный сайт в интернете.
```
agrant@ubuntu-bionic:~$ sudo apt-get install -y testssl.sh


vagrant@ubuntu-bionic:~$ testssl -U --sneaky https://localhost

No engine or GOST support via engine with your /usr/bin/openssl

###########################################################
    testssl       2.9.5 from https://testssl.sh/

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.1.1  11 Sep 2018" [~162 ciphers]
 on ubuntu-bionic:/usr/bin/openssl
 (built: "Jul  4 11:25:51 2022", platform: "debian-amd64")


 Start 2022-10-31 13:29:42        -->> 127.0.0.1:443 (localhost) <<--

 further IP addresses:   ::1
 A record via            /etc/hosts
 rDNS (127.0.0.1):       localhost.
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), no session tickets
 Secure Renegotiation (CVE-2009-3555)      VULNERABLE (NOT ok)
 Secure Client-Initiated Renegotiation     VULNERABLE (NOT ok), DoS threat
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    no HTTP compression (OK)  - only supplied "/" tested
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    not vulnerable (OK)
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services
                                           https://censys.io/ipv4?q=B358E46D0CC61C04A5F69252BE532A9326D14BB010C4A324C0FDD0D4FFC7CCAA could help you to find out
 LOGJAM (CVE-2015-4000), experimental      Common prime with 2048 bits detected: RFC3526/Oakley Group 14,
                                           but no DH EXPORT ciphers
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES256-SHA DHE-RSA-AES256-SHA DHE-RSA-CAMELLIA256-SHA AES256-SHA CAMELLIA256-SHA ECDHE-RSA-AES128-SHA DHE-RSA-AES128-SHA DHE-RSA-CAMELLIA128-SHA
                                                 AES128-SHA CAMELLIA128-SHA
                                           VULNERABLE -- but also supports higher protocols (possible mitigation): TLSv1.1 TLSv1.2
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2022-10-31 13:29:52 [  11s] -->> 127.0.0.1:443 (localhost) <<--


vagrant@ubuntu-bionic:~$
```
5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.

```
vagrant@vagrant:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:AOyV70Bb42FGGPnWZ0TwkyGa2JuQi2QNCZIjx7eDUIc vagrant@vagrant
The key's randomart image is:
+---[RSA 3072]----+
|.+o++..*. ooo    |
|=.E.++*+*o o.o   |
|.+ +o+*O++ .+    |
|  .o+.o+=o. o.   |
|    ...+S  o     |
|        .        |
|                 |
|                 |
|                 |
+----[SHA256]-----+
vagrant@vagrant:~$ ssh-copy-id vagrant@192.168.1.34
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host '192.168.1.74 (192.168.1.34)' can't be established.
ECDSA key fingerprint is SHA256:wSHl+h4vAtTT7mbkj2lbGyxWXWTUf6VUliwpncjwLPM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.1.74's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.1.74'"
and check to make sure that only the key(s) you wanted were added.

vagrant@vagrant:~$ ssh vagrant@192.168.1.34
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat 08 Jan 2022 08:57:43 AM UTC

  System load:  0.01              Processes:             114
  Usage of /:   2.3% of 61.31GB   Users logged in:       1
  Memory usage: 16%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.1.34


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Sat Jan  8 08:53:28 2022 from 10.0.2.2
vagrant@vagrant:~$
```

6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
```
vagrant@vagrant:~$ mv id_rsa id_rsa.test
vagrant@vagrant:~$ mv id_rsa.pub id_rsa.pub.test
vagrant@vagrant:~$ touch ./.ssh/config
vagrant@vagrant:~$ vim ./.ssh/config

Host vagrant_sshd
HostName 192.168.1.34
Port 22
IdentityFile ~vagrant/.ssh/id_rsa.test

vagrant@vagrant:~$ ssh vagrant_sshd
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat 08 Jan 2022 09:14:05 AM UTC

  System load:  0.01              Processes:             111
  Usage of /:   2.4% of 61.31GB   Users logged in:       1
  Memory usage: 16%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.1.34


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Sat Jan  8 08:57:44 2022 from 192.168.1.117
vagrant@vagrant:~$
```

7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
```
vagrant@ubuntu-bionic:~$ sudo tcpdump -c 100 -i enp0s8 -w enp0s8.pcap
PS C:\Users\ykarp\.ssh> scp vagrant@192.168.1.34:/home/vagrant/enp0s8.pcap ..\vagrant\

![Иллюстрация к 7 заданию](HW/images/hw39_scr3.png)