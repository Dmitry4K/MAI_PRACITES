# МАИ - Вычислительная практика - 2ой курс
Здесь будет выкладываться всякий треш для практики
## MiniOS
### Файлы репозитория
_readme.md_ - этот текст

_source.zip_ - исходники MiniOS

_MiniOS doc_ - документация MiniOS

_compile.sh_ - автор не поленился и написал мини скрипт, который автоматизирует вашу собрку этой "прекрасной" ОС :3

_makefile_ - исправленный makefile (желательно не использовать его вручную, потмоу что сборка MiniOS имеет свойство ломаться)

### Как собрать Это? ###

В исходном makefile, который находится в source.zip есть несколько ошибок, из-за которых зачастую эта ось не собирается. Для того, чтобы скомпилировать и использовать эту операционную систему необходимо сделать следующее:

0. **Установить Ubuntu 16.04 и обновить пакеты** (```sudo apt-get upgrade```)
1. **Распаковать source.zip** и перейти к исходникам где находится makefile.
2. **Заменить makefile, на новый makefile**, который находится в этом репозитории.
3. К исходникам **добавить скрипт compile.sh** (так же находится в этом репозитории).
4. **Выполнить ряд команд** находясь в папке с исходниками:
```bash
sudo apt install gcc make nasm qemu cgdb
сhmod ugo+x run_qemu.sh
chmod ugo+x compile.sh
sudo ./compile.sh
sudo ./run_qemu.sh
```
Скрипт compile.sh скомпилирует ядро ос, образ и добавит в образ ядро.

Скрипт run_qemu.sh запустит ос в qemu
