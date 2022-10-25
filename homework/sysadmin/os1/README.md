# Домашнее задание к занятию "3.3. Операционные системы. Лекция 1"

1. Выполним команду:
    ```
   strace /bin/bash -c 'cd /tmp' 2>&1 | grep execve
    ```
   
    Получаем ответ: 
    ```
    execve("/bin/bash", ["/bin/bash", "-c", "cd /tmp"], 0x7ffc8f02c320 /* 23 vars */) = 0 
    ```
2. База данных file находится в файле **/usr/share/misc/magic.mgc**. Запускаем команду:
    ```
   strace file 2>&1 | grep openat
    ```
   Получаем ответ:
    ```
    openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libmagic.so.1", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/liblzma.so.5", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libbz2.so.1.0", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libz.so.1", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
    openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)
    openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3
    openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3
    ```
3. Предлагаемый способ обнуления:

    a. Запускаем процесс и отправляем его в фон:  
    ```
    ping 8.8.8.8 > ping.log &
    ```

    Получаем PID:
    ```
    [2] 3286
    ```
    b. Удаляем лог-файл:
    ```
    rm ping.log
    ```
    c. Определяем файловый дескриптор:
    ```
    sudo lsof -p 3286 | grep ping.log
    ```
    ```
    ping    3286 vagrant    1w   REG  253,0     7553 1321148 /home/vagrant/ping.log (deleted)
    ```
    d. Обнуляем содержимое файла:
    
    ```
    : | sudo tee /proc/3286/fd/1
    ```
4. Зомби-прооцессы не используют системные ресурсы, но занимают место в таблице процессов системы. Так как размер таблицы ограничен, то большое количество зомби могут помешать созданию новых процессов.
5. Запустим утилиту с параметром `-T`:
   ```
   sudo opensnoop-bpfcc -T
   ```
   Результат работы команды за первую секунду:
   ```
   TIME(s)       PID    COMM               FD ERR PATH
   0.000000000   893    vminfo              4   0 /var/run/utmp
   0.000630000   653    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
   0.000730000   653    dbus-daemon        22   0 /usr/share/dbus-1/system-services
   0.001096000   653    dbus-daemon        -1   2 /lib/dbus-1/system-services
   0.001166000   653    dbus-daemon        22   0 /var/lib/snapd/dbus-1/system-services/
   1.664936000   658    irqbalance          6   0 /proc/interrupts
   ```
6. Запустим команду:
   ```
   strace uname -a 2>&1 | grep execve
   ```
   Получим ответ
   ```
   execve("/usr/bin/uname", ["uname", "-a"], 0x7ffc636f3d78 /* 23 vars */) = 0
   ```
   Цитата из man: `Part of the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.`
7. Команды с разделителем `;` выполняются последовательно. Если команды разделены с помощью `&&`, то последующая команда выполнится, только если предыдущая завершилась успешно (с кодом 0).
`&&` в режиме `set -e` использовать бессмысленно, так как с этой настройкой, если одна из команд завершится с ошибкой, выполнение последовательности команд прервётся в любом случае.
8. Опции режима `set -euxo pipefail`:
    - параметр `e` принудительно завершает сценарий, если одна из команд завершилась с ошибкой;
    - параметр `x` выводит отладочную информацию о командах и их аргументах;
    - параметр `u` прерывает работу сценария, если значение используемой переменной не определено;
    - если одна или несколько команд пайплайна завершились с ошибкой, то с параметром `o pipefail` код завершения пайплайна будет равен коду завершения последней команды, отличного от нуля. Без этого параметра кодом завершения пайплайна будет код завершения последней команды.
    
   Этот режим хорошо использовать в сценариях, так как мы получаем более точную информацию о работе сценария и сразу прерываем его выполнение, если что-то пошло не так.    
9. Выполним команду:
    ```
    ps -o stat
    ```
    Получим ответ:
    ```
    STAT
    Ss
    R+   
    ```
    Больше всего в системе спящих процессов, которые могут быть прерваны. Среди процессов, запущенных в фоне, больше всего исполняющихся и ожидающих исполнения процессов.
