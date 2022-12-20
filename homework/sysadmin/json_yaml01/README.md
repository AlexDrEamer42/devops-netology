# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"

### Цель задания

В результате выполнения этого задания вы:

1. Познакомитесь с синтаксисами JSON и YAML.
2. Узнаете как преобразовать один формат в другой при помощи пары строк.

### Чеклист готовности к домашнему заданию

Установлена библиотека pyyaml для Python 3.

### Инструкция к заданию 

1. Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys-24/04-script-03-yaml/README.md).
2. Заполните недостающие части документа решением задач (заменяйте `???`, остальное в шаблоне не меняйте, чтобы не сломать форматирование текста, подсветку синтаксиса и прочее) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желанию.
3. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.


------

## Задание 1

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
```
    { "info": "Sample JSON output from our service\\t",
        "elements": [
            { "name": "first",
            "type": "server",
            "ip": 7175 
            },
            { "name": "second",
            "type": "proxy",
            "ip": "71.78.22.43"
            }
        ]
    }
```

---

## Задание 2

В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
import socket
import json
import yaml

with open('hosts.json', 'r') as js:
    hosts = json.load(js)
    
for host in hosts:
    host_ip = socket.gethostbyname(host)
    if host_ip == hosts[host]:
        print(host,hosts[host])
    else:
        print(F'[ERROR] {host} IP mismatch: {hosts[host]} {host_ip}')
        hosts[host] = host_ip
        
with open('hosts.json', 'w') as js:
    js.write(json.dumps(hosts))
    
with open('hosts.yml', 'w') as ym:
    ym.write(yaml.dump(hosts, explicit_start = True))
```

### Вывод скрипта при запуске при тестировании:
```
drive.google.com 64.233.163.194
[ERROR] mail.google.com IP mismatch: 64.233.161.18 64.233.165.19
google.com 74.125.131.138
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{"drive.google.com": "64.233.163.194", "mail.google.com": "64.233.165.19", "google.com": "74.125.131.138"}
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
---
drive.google.com: 64.233.163.194
google.com: 74.125.131.138
mail.google.com: 64.233.165.19
```

---

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
import json
import yaml
import re
from pathlib import Path
from sys import argv

script, input_file = argv

# Проверяем расширение файла
file_ext = Path(input_file).suffix

if file_ext not in ('.json','.yml'):
    print ('Bad file format')
    exit(1)

print(f'Extension is {file_ext}')

# Если в файле есть одинокие запятые, то это json. Если нет, то yaml
# Чтобы это понять, читаем файл как строку 
try:
    with open(input_file, 'r') as inp:
        input_data = inp.read()
except FileNotFoundError:
    print('File not found')
    exit(2)

commas = re.compile(r',(?=(?![\"]*[\s\w\?\.\"\!\-\_]*,))(?=(?![^\[]*\]))')
signs = commas.findall(input_data)

if len(signs) > 0:
    format = 'json'
else:
    format = 'yaml'

print(f'Format is {format}')

# Записываем исходный файл в словарь

if format == 'yaml':
    try:
        with open(input_file,'r') as inp:
            input_dict = yaml.safe_load(inp)
        print(input_dict)
    except yaml.scanner.ScannerError as exc:
        print(f'Bad format: {exc}')
        exit(3)
else:
    try:
        with open(input_file,'r') as inp:
            input_dict = json.load(inp)
        print(input_dict)
    except json.JSONDecodeError as exc:
        print(f'Bad format: {exc}')
        exit(3)


# Преобразуем файл в другой формат

if format == 'json':
    output_file = Path(input_file).stem + '.yml'
    with open(output_file, 'w') as of:
        yaml.dump(input_dict, of)
else:
    output_file = Path(input_file).stem + '.json'
    with open(output_file, 'w') as of:
        json.dump(input_dict, of)

print(f'Successfully exported to {output_file}')

```

### Примеры работы скрипта:
```
alex@example ~/tmp/json_yaml $ python3 json_yaml.py hosts.yml
Extension is .yml
Format is json
{'drive.google.com': '64.233.163.194', 'mail.google.com': '64.233.165.19', 'google.com': '74.125.131.138'}
Successfully exported to hosts.yml
```

```
alex@example ~/tmp/json_yaml $ python3 json_yaml.py hosts.json
Extension is .json
Format is json
Bad format: Invalid control character at: line 3 column 39 (char 82)
```
----

### Правила приема домашнего задания

В личном кабинете отправлена ссылка на .md файл в вашем репозитории.

-----

### Критерии оценки

Зачет - выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку - задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки. 
 
Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.
Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

