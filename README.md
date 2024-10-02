# Домашнее задание к занятию «Защита сети» - Жаринов Павел
------
### Подготовка к выполнению заданий

1. Подготовка защищаемой системы:

- установите **Suricata** - выполнено
- установите **Fail2Ban** - выполнено

2. Подготовка системы злоумышленника: установите **nmap** и **thc-hydra** либо скачайте и установите **Kali linux**- выполнено

Обе системы должны находится в одной подсети - выполнено

------

### Задание 1

Проведите разведку системы и определите, какие сетевые службы запущены на защищаемой системе:

**sudo nmap -sA < ip-адрес >**

**sudo nmap -sT < ip-адрес >**

**sudo nmap -sS < ip-адрес >**

**sudo nmap -sV < ip-адрес >**

По желанию можете поэкспериментировать с опциями: https://nmap.org/man/ru/man-briefoptions.html.


*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*
В логе suricata видим, что Suricata обнаружила сканирование, кроме TCP ACK (-sA), т.к. этот вид сканирования выполняется без установления соединения.
```
vb@vb-VirtualBox:~$ sudo cat /var/log/suricata/fast.log
10/02/2024-18:07:58.997934  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:39326 -> 192.168.10.12:3306
10/02/2024-18:07:59.020933  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:54824 -> 192.168.10.12:1521
10/02/2024-18:07:59.037981  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:53236 -> 192.168.10.12:5432
10/02/2024-18:07:59.039365  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:34214 -> 192.168.10.12:1433
10/02/2024-18:07:59.047396  [**] [1:2002911:6] ET SCAN Potential VNC Scan 5900-5920 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:36028 -> 192.168.10.12:5907
10/02/2024-18:07:59.071511  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:54896 -> 192.168.10.12:5815
10/02/2024-18:11:05.970378  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:35695 -> 192.168.10.12:3306
10/02/2024-18:11:05.995734  [**] [1:2002911:6] ET SCAN Potential VNC Scan 5900-5920 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:35695 -> 192.168.10.12:5911
10/02/2024-18:11:06.001542  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:35695 -> 192.168.10.12:1521
10/02/2024-18:11:06.007015  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:35695 -> 192.168.10.12:5801
10/02/2024-18:11:06.015538  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:35695 -> 192.168.10.12:1433
10/02/2024-18:11:06.020169  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:35695 -> 192.168.10.12:5432
10/02/2024-18:12:39.125064  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:65027 -> 192.168.10.12:3306
10/02/2024-18:12:39.135223  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:65027 -> 192.168.10.12:1433
10/02/2024-18:12:39.200851  [**] [1:2002911:6] ET SCAN Potential VNC Scan 5900-5920 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:65027 -> 192.168.10.12:5902
10/02/2024-18:12:39.236697  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:65027 -> 192.168.10.12:5432
10/02/2024-18:12:39.260833  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:65027 -> 192.168.10.12:5800
10/02/2024-18:12:39.272698  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:65027 -> 192.168.10.12:1521
```

Лог fail2ban не содержит записей о проведенном сканировании, т.к. при сканировании не было попыток аутентификации.
```
vb@vb-VirtualBox:~$ sudo cat /var/log/fail2ban.log
2024-10-02 17:42:59,092 fail2ban.server         [7743]: INFO    --------------------------------------------------
2024-10-02 17:42:59,092 fail2ban.server         [7743]: INFO    Starting Fail2ban v0.11.2
2024-10-02 17:42:59,093 fail2ban.observer       [7743]: INFO    Observer start...
2024-10-02 17:42:59,098 fail2ban.database       [7743]: INFO    Connected to fail2ban persistent database '/var/lib/fail2ban/fail2ban.sqlite3'
2024-10-02 17:42:59,099 fail2ban.database       [7743]: WARNING New database created. Version '4'
2024-10-02 17:42:59,099 fail2ban.jail           [7743]: INFO    Creating new jail 'sshd'
2024-10-02 17:42:59,111 fail2ban.jail           [7743]: INFO    Jail 'sshd' uses pyinotify {}
2024-10-02 17:42:59,115 fail2ban.jail           [7743]: INFO    Initiated 'pyinotify' backend
2024-10-02 17:42:59,116 fail2ban.filter         [7743]: INFO      maxLines: 1
2024-10-02 17:42:59,125 fail2ban.filter         [7743]: INFO      maxRetry: 5
2024-10-02 17:42:59,126 fail2ban.filter         [7743]: INFO      findtime: 600
2024-10-02 17:42:59,126 fail2ban.actions        [7743]: INFO      banTime: 600
2024-10-02 17:42:59,126 fail2ban.filter         [7743]: INFO      encoding: UTF-8
2024-10-02 17:42:59,127 fail2ban.filter         [7743]: INFO    Added logfile: '/var/log/auth.log' (pos = 0, hash = be8ff6fd3c3408f8abd351a99c6a0c8d1f208ea4)
2024-10-02 17:42:59,131 fail2ban.jail           [7743]: INFO    Jail 'sshd' started

```
------

### Задание 2

Проведите атаку на подбор пароля для службы SSH:

**hydra -L users.txt -P pass.txt < ip-адрес > ssh**

1. Настройка **hydra**: 
 
 - создайте два файла: **users.txt** и **pass.txt**;
 - в каждой строчке первого файла должны быть имена пользователей, второго — пароли. В нашем случае это могут быть случайные строки, но ради эксперимента можете добавить имя и пароль существующего пользователя.

Дополнительная информация по **hydra**: https://kali.tools/?p=1847.

2. Включение защиты SSH для Fail2Ban:

-  открыть файл /etc/fail2ban/jail.conf,
-  найти секцию **ssh**,
-  установить **enabled**  в **true**.

Дополнительная информация по **Fail2Ban**:https://putty.org.ru/articles/fail2ban-ssh.html.



*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*
```
└─$ sudo hydra -L user.txt -P pass.txt 192.168.10.12 ssh
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-02 12:05:20
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 80 login tries (l:8/p:10), ~5 tries per task
[DATA] attacking ssh://192.168.10.12:22/
[ERROR] could not connect to ssh://192.168.10.12:22 - Connection refused
```
suricata сохранила в логах попытку утечки информации с атакующего хоста. 
```
vb@vb-VirtualBox:/etc/fail2ban$ sudo tail -f /var/log/suricata/fast.log
10/02/2024-18:18:04.774998  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:3306
10/02/2024-18:18:04.806675  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:1521
10/02/2024-18:18:04.820322  [**] [1:2002911:6] ET SCAN Potential VNC Scan 5900-5920 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:5907
10/02/2024-18:18:04.951958  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:1433
10/02/2024-18:18:04.965092  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:5801
10/02/2024-18:18:04.965159  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:5432
10/02/2024-20:36:02.118298  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.10.10:8 -> 192.168.10.12:0
10/02/2024-20:36:03.147281  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.10.10:8 -> 192.168.10.12:0
10/02/2024-20:42:26.162954  [**] [1:2001219:20] ET SCAN Potential SSH Scan [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:42520 -> 192.168.10.12:22
10/02/2024-20:42:26.162954  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:42520 -> 192.168.10.12:22
```
fail2ban не зафиксировал подбора пароля, могу предположить это потому, что делал много попыток и ip попал в блок-лист
```
vb@vb-VirtualBox:~$ sudo tail -f /var/log/auth.log
Oct  2 21:11:43 vb-VirtualBox sudo:       vb : TTY=pts/0 ; PWD=/home/vb ; USER=root ; COMMAND=/usr/bin/cat /var/log/fail2ban.log
Oct  2 21:11:43 vb-VirtualBox sudo: pam_unix(sudo:session): session opened for user root(uid=0) by (uid=1000)
Oct  2 21:11:43 vb-VirtualBox sudo: pam_unix(sudo:session): session closed for user root
Oct  2 21:17:01 vb-VirtualBox CRON[4722]: pam_unix(cron:session): session opened for user root(uid=0) by (uid=0)
Oct  2 21:17:01 vb-VirtualBox CRON[4722]: pam_unix(cron:session): session closed for user root
Oct  2 21:17:19 vb-VirtualBox sudo:       vb : TTY=pts/0 ; PWD=/home/vb ; USER=root ; COMMAND=/usr/bin/cat /var/log/auth.log
Oct  2 21:17:19 vb-VirtualBox sudo: pam_unix(sudo:session): session opened for user root(uid=0) by (uid=1000)
Oct  2 21:17:19 vb-VirtualBox sudo: pam_unix(sudo:session): session closed for user root
Oct  2 21:17:32 vb-VirtualBox sudo:       vb : TTY=pts/0 ; PWD=/home/vb ; USER=root ; COMMAND=/usr/bin/tail -f /var/log/auth.log
Oct  2 21:17:32 vb-VirtualBox sudo: pam_unix(sudo:session): session opened for user root(uid=0) by (uid=1000)
---
vb@vb-VirtualBox:~$ sudo tail -f /var/log/fail2ban.log
2024-10-02 21:08:50,279 fail2ban.jail           [4527]: INFO    Creating new jail 'sshd'
2024-10-02 21:08:50,305 fail2ban.jail           [4527]: INFO    Jail 'sshd' uses pyinotify {}
2024-10-02 21:08:50,310 fail2ban.jail           [4527]: INFO    Initiated 'pyinotify' backend
2024-10-02 21:08:50,310 fail2ban.filter         [4527]: INFO      maxLines: 1
2024-10-02 21:08:50,320 fail2ban.filter         [4527]: INFO      maxRetry: 5
2024-10-02 21:08:50,320 fail2ban.filter         [4527]: INFO      findtime: 600
2024-10-02 21:08:50,320 fail2ban.actions        [4527]: INFO      banTime: 600
2024-10-02 21:08:50,320 fail2ban.filter         [4527]: INFO      encoding: UTF-8
2024-10-02 21:08:50,322 fail2ban.filter         [4527]: INFO    Added logfile: '/var/log/auth.log' (pos = 55859, hash = be8ff6fd3c3408f8abd351a99c6a0c8d1f208ea4)
2024-10-02 21:08:50,328 fail2ban.jail           [4527]: INFO    Jail 'sshd' started
---
vb@vb-VirtualBox:~$ sudo systemctl status fail2ban
● fail2ban.service - Fail2Ban Service
     Loaded: loaded (/lib/systemd/system/fail2ban.service; disabled; vendor preset: enabled)
     Active: active (running) since Wed 2024-10-02 21:08:50 MSK; 10min ago
       Docs: man:fail2ban(1)
   Main PID: 4527 (fail2ban-server)
      Tasks: 5 (limit: 4557)
     Memory: 14.1M
        CPU: 410ms
     CGroup: /system.slice/fail2ban.service
             └─4527 /usr/bin/python3 /usr/bin/fail2ban-server -xf start

окт 02 21:08:50 vb-VirtualBox systemd[1]: Started Fail2Ban Service.
окт 02 21:08:50 vb-VirtualBox fail2ban-server[4527]: Server ready
---
vb@vb-VirtualBox:~$ sudo tail -f /var/log/suricata/fast.log
10/02/2024-18:18:04.965159  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 192.168.10.10:61349 -> 192.168.10.12:5432
10/02/2024-20:36:02.118298  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.10.10:8 -> 192.168.10.12:0
10/02/2024-20:36:03.147281  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.10.10:8 -> 192.168.10.12:0
10/02/2024-20:42:26.162954  [**] [1:2001219:20] ET SCAN Potential SSH Scan [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:42520 -> 192.168.10.12:22
10/02/2024-20:42:26.162954  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 192.168.10.10:42520 -> 192.168.10.12:22
10/02/2024-21:15:47.594636  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.10.10:8 -> 192.168.10.12:0
10/02/2024-21:15:48.596387  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.10.10:8 -> 192.168.10.12:0
10/02/2024-21:15:49.602507  [**] [1:2100366:8] GPL ICMP_INFO PING *NIX [**] [Class

```
