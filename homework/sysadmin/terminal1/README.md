5. Для ВМ выделено 1024 МБ и 2 процессора.
6. Чтобы добавить ресурсы для ВМ, нужно изменить файл Vagrantfile:
    ```vagrant
    Vagrant.configure("2") do |config|
            config.vm.box = "bento/ubuntu-20.04"
            config.vm.provider "virtualbox" do |v|
              v.memory = 2048
              v.cpus = 4
            end
     end
    ```

    и перезапустить ВМ командой `vagrant reload`.


8. Длина журнала `history` задаётся в переменной `HISTFILESIZE` (строка 1155). Директива `ignoreboth` используется в переменной `HISTCONTROL`, чтобы не сохранять в истории повторяющиеся команды и команды, которые начинаются с пробела.
9. Фигурные скобки используются для генерации множества строк (строка 806).
10. Чтобы создать 100000 файлов, нужно выполнить команду `touch file{1..100000}`. Создать 300000 файлов не позволяет размер стека. Его можно увеличить командой `ulimit -s 65536`.
11. Конструкция `[[ -d /tmp ]]` возвращает `true`, если существует директория **/tmp**.
12. Последовательность команд:
    ```bash
    mkdir /tmp/new_path_directory
    cp /bin/bash /tmp/new_path_directory/
    export PATH=/tmp/new_path_directory:$PATH
    ```
13. `at` выполняет команду строго в назначенное время, `batch` ждёт, когда это позволяет уровень системной нагрузки.