ДЗ3.8

1. Работа c HTTP через телнет.
```
Trying 151.101.129.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.1 301 Moved Permanently
Server: Varnish
Retry-After: 0
Location: https://stackoverflow.com/questions
Content-Length: 0
Accept-Ranges: bytes
Date: Fri, 09 Sep 2022 07:25:01 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-bma1635-BMA
X-Cache: HIT
X-Cache-Hits: 0
X-Timer: S1662708301.382177,VS0,VE0
Strict-Transport-Security: max-age=300
X-DNS-Prefetch-Control: off

Код состояния HTTP 301 или Moved Permanently (с англ. — «Перемещено навсегда») — стандартный код ответа HTTP, получаемый в ответ от сервера в ситуации, когда запрошенный ресурс был на постоянной основе перемещён в новое месторасположение, и указывающий на то, что текущие ссылки, использующие данный URL, должны быть обновлены. Адрес нового месторасположения ресурса указывается в поле Location получаемого в ответ заголовка пакета протокола HTTP. 
В нашем случае: https://stackoverflow.com/questions
```
2. Повторите задание 1 в браузере, используя консоль разработчика F12.

![dev-console](https://github.com/yk-x129/devops-netology/HW/images/dev-console.png)

307 Internal Redirect

3.Какой IP адрес у вас в интернете?
```
curl https://ipinfo.io/ip
83.220.236.52
```

4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois
```
inetnum:        83.220.236.0 - 83.220.239.255
netname:        BEE-MSK-GPRS-FW
descr:          Beeline-Moscow GPRS Firewall
country:        RU
admin-c:        VLAC1-RIPE
tech-c:         VLTC1-RIPE
status:         ASSIGNED PA
remarks:        ------------ A T T E N T I O N !!! ------------
remarks:        Please use
remarks:
remarks:        internet.abuse@beeline.ru
remarks:        fraud@beeline.ru
remarks:        info@beeline.ru
remarks:
remarks:        e-mail addresses for spam and abuse complaints.
remarks:        Messages to other addresses will be ignored!
remarks:        -----------------------------------------------
mnt-by:         BEE-MNT
created:        2008-11-13T10:57:01Z
last-modified:  2009-02-10T15:04:00Z
source:         RIPE # Filtered

% Information related to '83.220.236.0/23AS16345'

route:          83.220.236.0/23
descr:          OJSC "VimpelCom"
origin:         AS16345
mnt-by:         BEE-MNT
created:        2013-09-16T16:12:58Z
last-modified:  2013-09-16T16:12:58Z
source:         RIPE # Filtered

AS - AS16345
```

5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute
```
traceroute -An 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.10.100.22 [*]  2.432 ms  2.382 ms  2.364 ms
 2  10.22.22.22 [*]  2.349 ms  2.333 ms  2.319 ms
 3  * * *
 4  * * *
 5  10.10.140.3 [*]  23.033 ms 10.10.140.14 [*]  23.020 ms 10.10.140.15 [*]  23.007 ms
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  62.105.150.248 [AS8350/AS3216]  38.775 ms  38.756 ms  38.737 ms
11  81.211.29.103 [AS3216]  38.720 ms  30.684 ms  33.692 ms
12  * 108.170.250.146 [AS15169]  63.163 ms 108.170.250.66 [AS15169]  40.382 ms
13  142.251.49.24 [AS15169]  54.956 ms  51.622 ms 142.250.239.64 [AS15169]  51.602 ms
14  142.251.238.72 [AS15169]  63.069 ms 72.14.235.69 [AS15169]  51.564 ms 172.253.65.82 [AS15169]  51.546 ms
15  72.14.237.201 [AS15169]  55.888 ms 216.239.47.201 [AS15169]  49.789 ms 172.253.51.223 [AS15169]  61.089 ms
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  8.8.8.8 [AS15169]  40.636 ms  40.007 ms *
```

6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?
```
mtr -zn 8.8.8.8
![mtr](https://github.com/yk-x129/devops-netology/HW/images/mtr.png)

```

7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig
```
dig dns.google.com
; <<>> DiG 9.18.1-1ubuntu1.1-Ubuntu <<>> dns.google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17925
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google.com.			IN	A

;; ANSWER SECTION:
dns.google.com.		516	IN	A	8.8.8.8
dns.google.com.		516	IN	A	8.8.4.4

;; Query time: 47 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Fri Sep 09 10:34:58 MSK 2022
;; MSG SIZE  rcvd: 75
```

8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig
```
dig -x 8.8.8.8

; <<>> DiG 9.18.1-1ubuntu1.1-Ubuntu <<>> -x 8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35268
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;8.8.8.8.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.	19726	IN	PTR	dns.google.

;; Query time: 47 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Fri Sep 09 10:36:03 MSK 2022
;; MSG SIZE  rcvd: 73

dig -x 8.8.4.4

; <<>> DiG 9.18.1-1ubuntu1.1-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5134
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.	6736	IN	PTR	dns.google.

;; Query time: 51 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Fri Sep 09 10:36:07 MSK 2022
;; MSG SIZE  rcvd: 73
```
