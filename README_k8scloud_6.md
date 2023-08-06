# Домашнее задание к занятию «Организация сети»

---
### Задание 1. Yandex Cloud 

**Что нужно сделать**

1. Создать пустую VPC. Выбрать зону.
2. Публичная подсеть.

 - Создать в VPC subnet с названием public, сетью 192.168.10.0/24.
 - Создать в этой подсети NAT-инстанс, присвоив ему адрес 192.168.10.254. В качестве image_id использовать fd80mrhj8fl2oe87o4e1.
 - Создать в этой публичной подсети виртуалку с публичным IP, подключиться к ней и убедиться, что есть доступ к интернету.
3. Приватная подсеть.
 - Создать в VPC subnet с названием private, сетью 192.168.20.0/24.
 - Создать route table. Добавить статический маршрут, направляющий весь исходящий трафик private сети в NAT-инстанс.
 - Создать в этой приватной подсети виртуалку с внутренним IP, подключиться к ней через виртуалку, созданную ранее, и убедиться, что есть доступ к интернету.

### Ответ

1. Сети и виртуальные машины созданы

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/bb732313-d49d-42ad-b59b-ab061a786e6e)

ссылка на тераформ https://github.com/LugovskoyPavel/terraform_yc_lug

2. Проверка на подключение к второй виртуалке из  подсети public

```
PS C:\Users\lugy1\terraform_yandex_dz> ssh 158.160.59.48 
The authenticity of host '158.160.59.48 (158.160.59.48)' can't be established.
ECDSA key fingerprint is SHA256:cGPivkKvQJ/kXADEbpAyQhj1DZiqTDSgT1xCY1DYH9s.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '158.160.59.48' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Aug  6 09:03:55 AM UTC 2023

  System load:  0.16259765625      Processes:             134
  Usage of /:   15.0% of 19.59GB   Users logged in:       0
  Memory usage: 5%                 IPv4 address for eth0: 192.168.10.21
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

lugy1@fhm36ckss3r658l16fsn:~$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=59 time=20.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=59 time=19.4 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=59 time=19.3 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=59 time=19.4 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 19.338/19.563/20.097/0.309 ms
```
3. Проверка доступа в интернет из private подсети

```
lugy1@fhmbdjqu4vcdbsrj63a2:~$ curl ifconfig.co
158.160.116.133
lugy1@fhmbdjqu4vcdbsrj63a2:~$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=58 time=21.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=58 time=20.9 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=58 time=21.0 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=58 time=21.0 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 20.914/21.166/21.748/0.337 ms
```
