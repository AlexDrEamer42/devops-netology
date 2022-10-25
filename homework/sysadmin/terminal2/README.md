# Домашнее задание к занятию "3.2. Работа в терминале. Лекция 2"

1. `cd` — это встроенная команда оболочки, так как работает "из коробки" и не порождает новых процессов. Можно проверить с помощью команды `type -t cd`, в ответе будет `builtin`.
2. Вместо `grep <some_string> <some_file> | wc -l` можно использовать `grep -c <some_string> <some_file>`.
3. При запуске команды `pstree -p` видно, что родителем всех процессов является `systemd(1)`.
4. Пример команды для перенаправления в сессию /dev/pts/1:
   ```bash
   ls 2>/dev/pts/1
   ```
5. Да, получится. Например, командой: 
    ```
    cat < file1.txt > file2.txt
    ```
6. Получится, если открыть эмулятор TTY (например, через Ctrl + Alt + F3) и залогиниться в нём. Чтобы увидеть выводимые данные, их надо перенаправить в нужный терминал. Например, `ls > /dev/tty3`.
7. `bash 5>&1` запускает оболочку с файловым дескриптором 5 и перенаправлением в stdout. Поэтому команда `echo netology > /proc/$$/fd/5` выведет `netology` в текущем окне терминала.   
8. Да, получится с помощью команды вида `command1 3>&2 2>&1 1>&3 | command2`.
9. Команда выводит переменные окружения для процесса оболочки. Значение переменных можно также получить с помощью `ps eww $$`.
10. `/proc/<PID>/cmdline` содержит полную команду, запустившую процесс, если этот процесс не зомби. `/proc/<PID>/exe` содержит символическую ссылку на команду, запустившую процесс.
11. Выполним команду `cat /proc/cpuinfo | grep sse`
    ```
    flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm invpcid_single pti fsgsbase avx2 invpcid md_clear flush_l1d
    flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm invpcid_single pti fsgsbase avx2 invpcid md_clear flush_l1d
    flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm invpcid_single pti fsgsbase avx2 invpcid md_clear flush_l1d
    flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm invpcid_single pti fsgsbase avx2 invpcid md_clear flush_l1d
    ```
    Самая старшая версия SSE — SSE4.2.
12. Для SSH по умолчанию не выделяется TTY. Чтобы изменить такое поведение, нужно использовать параметр -t, если существует локальный TTY, или параметр -tt, чтобы принудительно выделить TTY.
13. Перевёл процесс в другую сессию по инструкции из [документации reptyr](https://github.com/nelhage/reptyr#typical-usage-pattern). 
14. Команда `tee` читает со стандартного входа и записывает в стандартный выход и файлы. Так как команда `tee` запущена с правами суперпользователя, у неё есть доступ к директории **/root**. В команде `sudo echo string > /root/new_file` с правами суперпользователя запускается только `echo string`, поэтому доступа к директории **/root** нет.
