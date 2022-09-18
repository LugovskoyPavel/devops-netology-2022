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
| Какое значение будет присвоено переменной `c`?  | Ошибка, так как a и b переменные с разным типом данных |
| Как получить для переменной `c` значение 12?  | Необходимо a - превести к типу строка, далее переменную с перевезти в тип int  |
| Как получить для переменной `c` значение 3?  | Необходимо b задать без кавычек, то есть как тип int  |

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
#!/usr/bin/env python3

import os

bash_command = ["cd ~/devops-netology-2022", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
print(result_os)
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@vagrant:~$ ./script1.sh
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean

Добавил print, так как был нулевой ответ
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
#!/usr/bin/env python3

import os
import sys

param  = sys.argv[1]
bash_command = ['cd '+param, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()

for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(f'{param}/{prepare_result}')

```

### Вывод скрипта при запуске при тестировании:
```

vagrant@vagrant:~$ ./script1.sh sysadm-homeworks
vagrant@vagrant:~$

Результат - пустой
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
#!/usr/bin/env python3

import socket

i = 1
servers = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0


while 1==1 :
  for host in servers:
    ip = socket.gethostbyname(host)
    if ip != servers[host]:
      if i==1 and init !=1:
        print('ERROR ' + str(host) +' IP mistmatch: '+servers[host]+' '+ip)
      servers[host]=ip
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@vagrant:~$ nano script1.sh
vagrant@vagrant:~$ ./script1.sh
2022-09-18 08:57:00 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 173.194.222.194
2022-09-18 08:57:00 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 74.125.131.83
2022-09-18 08:57:00 [ERROR] google.com IP mistmatch: 0.0.0.0 64.233.164.139
2022-09-18 08:57:00 [ERROR] google.com IP mistmatch: 64.233.164.139 64.233.164.101
2022-09-18 08:57:10 [ERROR] mail.google.com IP mistmatch: 74.125.131.83 173.194.222.19
2022-09-18 08:57:10 [ERROR] mail.google.com IP mistmatch: 173.194.222.19 173.194.222.17
2022-09-18 08:57:14 [ERROR] drive.google.com IP mistmatch: 173.194.222.194 64.233.164.194
```
