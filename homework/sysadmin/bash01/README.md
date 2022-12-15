# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"

### Цель задания

В результате выполнения этого задания вы:

1. Познакомитесь с командной оболочкой Bash.
2. Используете синтаксис bash-скриптов.
3. Узнаете, как написать скрипт в файл так, чтобы он мог выполниться с параметрами и без.


### Чеклист готовности к домашнему заданию

1. У вас настроена виртуальная машина/контейнер/установлена гостевая ОС семейств Linux, Unix, MacOS.
2. Установлен Bash.


### Инструкция к заданию

1. Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-01-bash/README.md).
2. Заполните недостающие части документа решением задач (заменяйте `???`, остальное в шаблоне не меняйте, чтобы не сломать форматирование текста, подсветку синтаксиса). Вместо логов можно вставить скриншоты по желанию.
3. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
4. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.

------

## Задание 1

Есть скрипт:
```bash
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная | Значение | Обоснование                                                               |
|------------|----------|---------------------------------------------------------------------------|
| `c`        | `a+b`    | a и b интерпретируются как строки, а не значения переменных               |
| `d`        | `1+2`    | переменные $a и $b по умолчанию интерпретируются как строковые            |
| `e`        | `3`      | конструкция `$(( ))` явно указывает на выполнение арифметической операции |

----

## Задание 2

На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
```bash
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```

### Ваш скрипт:
```bash
#!/usr/bin/env bash
i=1
while ((i==1))
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	else
		echo 'Service is working!' >> curl.log
		i=0
	fi
done
```

---

## Задание 3

Необходимо написать скрипт, который проверяет доступность трёх IP: `192.168.0.1`, `173.194.222.113`, `87.250.250.242` по `80` порту и записывает результат в файл `log`. Проверять доступность необходимо пять раз для каждого узла.

### Ваш скрипт:
```bash
#!/usr/bin/env bash
ip=("5.255.255.88" "77.88.55.50" "127.0.0.1")
for i in ${ip[@]}
do
        for j in {1..5} 
        do
                curl http://{$i}:80
                if (($? == 0))
                then
                        echo $i ' доступен' >> log
                else
                        echo $i ' недоступен' >> log
                fi
        done
done
```

Содержимое лог-файла:
```
5.255.255.88  доступен
5.255.255.88  доступен
5.255.255.88  доступен
5.255.255.88  доступен
5.255.255.88  доступен
77.88.55.50  доступен
77.88.55.50  доступен
77.88.55.50  доступен
77.88.55.50  доступен
77.88.55.50  доступен
127.0.0.1  недоступен
127.0.0.1  недоступен
127.0.0.1  недоступен
127.0.0.1  недоступен
127.0.0.1  недоступен
```
---
## Задание 4

Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

### Ваш скрипт:
```bash
#!/usr/bin/env bash
ip=("5.255.255.88" "127.0.0.1" "77.88.55.50")
err=0
for i in ${ip[@]}
do
        for j in {1..5} 
        do
                curl http://{$i}:80
                if (($? == 0))
                then
                        echo $i ' доступен' >> log
                else
                        echo $i ' недоступен' >> error
                        err=1
                        break
                fi
        done
        if (($err == 1))
        then
                break
        fi
done
```
Содержимое файла `log`:
```
5.255.255.88  доступен
5.255.255.88  доступен
5.255.255.88  доступен
5.255.255.88  доступен
5.255.255.88  доступен
```
Содержимое файла `error`:
```
127.0.0.1  недоступен
```
---

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: \[04-script-01-bash\] сломал хук.

### Ваш скрипт:
```bash
#!/usr/bin/env bash
commit_size=$(expr length $1)

if (( $commit_size <= 30 )) && [[ $1 =~ ^\[.*\] ]]
then
	echo 'Коммит красивый'
	exit 0
else
	echo 'Коммит некрасивый'
	exit 1
fi
```

----

### Правила приема домашнего задания
В личном кабинете отправлена ссылка на .md файл в вашем репозитории.


### Критерии оценки

Зачет - выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку - задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки. 
 
Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.
Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.