# МАИ - Вычислительная практика - 2ой курс
Здесь будет выкладываться всякий треш для практики
# MiniOS
## Файлы репозитория
_readme.md_ - этот текст

_source.zip_ - исходники MiniOS

_MiniOS doc_ - документация MiniOS

_compile.sh_ - автор не поленился и написал мини скрипт, который автоматизирует вашу собрку этой "прекрасной" ОС :3

_makefile_ - исправленный makefile (желательно не использовать его вручную, потому что процесс сборки MiniOS имеет свойство ломаться)

_vfs.c_ - реализация функций для работы с файловой таблицей дескрипторов.

_vfs.h_ - объявление таблицы файловых дескрипторов, прототипы функций

## Как собрать Это?

В исходном makefile, который находится в source.zip есть несколько ошибок, из-за которых зачастую эта ось не собирается. Для того, чтобы скомпилировать и использовать эту операционную систему необходимо сделать следующее:

0. **Установить Ubuntu 16.04 и обновить пакеты** (```sudo apt-get update && sudo apt-get upgrade```)
1. **Распаковать source.zip** и перейти к исходникам где находится makefile.
2. **Заменить makefile, на новый makefile**, который находится в этом репозитории.
3. К исходникам **добавить скрипт compile.sh** (также находится в этом репозитории).
4. В папке проекта **необходимо создать файл .gdbinit с содержанием “ set auto-load safe-path /”**
5. **Выполнить ряд команд** находясь в папке с исходниками:
```bash
sudo apt install gcc make nasm qemu cgdb grub
сhmod ugo+x run_qemu.sh
chmod ugo+x compile.sh
sudo ./compile.sh
sudo ./run_qemu.sh
```
Если у вас по каким-то причинам **не работает chmod** и его не получается установить, то делаете это:
```bash
sudo apt install gcc make nasm qemu cgdb grub
sudo bash ./compile.sh
sudo bash ./run_qemu.sh
```

Скрипт compile.sh скомпилирует ядро ос, образ и добавит в образ ядро.

Скрипт run_qemu.sh запустит ос в qemu

В итоге всех махинаций получается образ MiniOS - hdd.img. Как запустить его с флешки - я хз) [Уже не хз]

**Если у вас что пошло не так по этому гайду, возможно получиться по другому - https://github.com/DeadBlasoul/minios**

_Коментарий автора:_

К слову, если вы отчаянный человек и решите хоть насколько то уйти от предложенного гайда, скорее всего у вас все сломается))) (речь идет не просто о соблюдении порядка команд, но и версии ос на которой можно собрать данную ос)

## Как запуститься с флешки?

Нужно каким-то образом полученный образ hdd.img установить на флешку, это можно сделать следующими способами

### Для Винды

0. **Выгрузить из виртуальной машины образ hdd.img** (настроить общие папки если используете Virtual Box - https://lumpics.ru/set-up-virtualbox-shared-folders-in-linux/)
1. **Вставить флешку**
2. Используя программу Rufus (https://rufus.ie/ru_RU.html) или Win32DiskImager (https://informatiktv.ru/index.php/rabota-s-nositelyami/92-zapis-obraza-img-na-fleshku) **загрузить образ на флешку**
3. **Перезагрузить компьютер**
4. Во время включения системы **открыть grub или bios** (зажать shift или esc, можно обе)
5. **Выбрать свою флешку в меню boot** (этот шаг у всех разный, так как все компы - разные)

### Для Линукса

1. **Вставить флешку**
2. **Выполнить скрипт - to_flash.sh** (```sudo bash ./to_flash.sh```)
3. **Перезагрузить компьютер**
4. Во время включения системы **открыть grub или bios** (зажать shift или esc, можно обе)
5. **Выбрать свою флешку в меню boot** (этот шаг у всех разный, так как все компы - разные)

Также может потребоваться убрать параметр secure boot, что бы операционная система запустилась. По сути - это смая сложная часть в данном шаге - заставить вашу машину запустить minios - придется поковыряться в настройках bios или uefi и снять там все ограничения.

_Коментарий автора:_

За гайд по Линукс не отвечаю, у меня главная ось - Windows

## \[Группа 208, Команда 5\] Объяснение работы модуля initrd и виртуальной файловой системы

**Виртуальная файловая система** – (**V**irtual **F**ile **S**ystem) предназначена для того, чтобы абстрагироваться от деталей файловой системы и места, где файлы хранятся, и чтобы получать доступ к файлам в единообразной форме. Все это, как правило, реализуется в виде графа нодов; каждый нод представляет собой файл, директорий, символьную ссылку, устройство, сокет или конвейер. Каждый нод должен знать, к какой файловой системе он принадлежит, и у него должно быть достаточно информации с тем, чтобы он можно было найти и выполнить соответствующие функции открытия, закрытия и т. п. Распространенным способом достижения этой цели является наличие указателей функций, которые можно вызвать из ядра.

### Подключение модуля initrd.img

initrd.img - отдельный модуль операционной системы. По своей сути - диск, носитель информации. В этом модуле описана основная виртуальная файловая система с функциями открытия и чтения файлов из этого диска. Чтобы по лучше узнать как работает этот модуль нужно посмотреть документацию - http://www.jamesmolloy.co.uk/tutorial_html/8.-The%20VFS%20and%20the%20initrd.html. 

Для его подклчения нужно добавить initrd.img к модулю hdd.img, добавив несколько строк в makefile, затем в файлу menu.lst указать этот модуль.

### О модуле initrd.img

**Структура** – в самом начале модуля записано кол-во файлов (n) на диске, далее идет n файловых заголовков (initrd_file_header_t) описывающих все файлы в системе. Далее идет содержание самих файлов. 

Далее приведено описание initrd_file_header_t

```C
typedef struct
{
    u8int magic;     // Magic number, for error checking.
    s8int name[64];  // Filename.
    u32int offset;   // Offset in the initrd that the file starts.
    u32int length;   // Length of the file.
} initrd_file_header_t;
```

Моудль initrd.img предоставляет свои функции работы с файлами и каталогами, они приведены ниже:

```C
static u32int initrd_read(fs_node_t *node, u32int offset, u32int size, u8int *buffer);
static struct dirent *initrd_readdir(fs_node_t *node, u32int index);
static fs_node_t *initrd_finddir(fs_node_t *node, char *name);
fs_node_t *initialise_initrd(u32int location);
```

Только эти функции работают напрямую с initrd. **Таким образом виртуальная файловая система лишь использует уже имеющиеся функции**.

### О виртуальной файловой системе

Виртуальная файловая система реализует ряд новых типов данных, которое помогают работать с файловой системой. Файлы fs.h и fs.c - та самая виртуальная файловаая система. Она предоставляет следующие типы данных:

```C
typedef struct fs_node
{
    char name[128];     // The filename.
    u32int mask;        // The permissions mask.
    u32int uid;         // The owning user.
    u32int gid;         // The owning group.
    u32int flags;       // Includes the node type. See #defines above.
    u32int inode;       // This is device-specific - provides a way for a filesystem to identify files.
    u32int length;      // Size of the file, in bytes.
    u32int impl;        // An implementation-defined number.
    read_type_t read;
    write_type_t write;
    open_type_t open;
    close_type_t close;
    readdir_type_t readdir;
    finddir_type_t finddir;
    struct fs_node *ptr; // Used by mountpoints and symlinks.
} fs_node_t;

struct dirent
{
    char name[128]; // Filename.
    u32int ino;     // Inode number. Required by POSIX.
};
```
read_type_t, write_type_t, open_type_t и т.д. ничто иное как указатели на функции открытия, чтенияЮ закртия файлов. При иницилизации файловой системы в эти поля должны быть помещены указатели на соотвествующие функции, которые будут вызываться файловой системой. Это происходит здесь (отмечено <----):

```C
fs_node_t *initialise_initrd(u32int location)
{
    // Initialise the main and file header pointers and populate the root directory.
    initrd_header = (initrd_header_t *)location;
    file_headers = (initrd_file_header_t *) (location+sizeof(initrd_header_t));

    // Initialise the root directory.
    initrd_root = (fs_node_t*)kmalloc(sizeof(fs_node_t));
    strcpy(initrd_root->name, "initrd");
    initrd_root->mask = initrd_root->uid = initrd_root->gid = initrd_root->inode = initrd_root->length = 0;
    initrd_root->flags = FS_DIRECTORY;
    initrd_root->read = 0;
    initrd_root->write = 0;
    initrd_root->open = 0;
    initrd_root->close = 0;
    initrd_root->readdir = &initrd_readdir;			//<------------
    initrd_root->finddir = &initrd_finddir;			//<------------
    initrd_root->ptr = 0;
    initrd_root->impl = 0;

    // Initialise the /dev directory (required!)
    initrd_dev = (fs_node_t*)kmalloc(sizeof(fs_node_t));
    strcpy(initrd_dev->name, "dev");
    initrd_dev->mask = initrd_dev->uid = initrd_dev->gid = initrd_dev->inode = initrd_dev->length = 0;
    initrd_dev->flags = FS_DIRECTORY;
    initrd_dev->read = 0;
    initrd_dev->write = 0;
    initrd_dev->open = 0;
    initrd_dev->close = 0;
    initrd_dev->readdir = &initrd_readdir;			//<-----------
    initrd_dev->finddir = &initrd_finddir;			//<-----------
    initrd_dev->ptr = 0;
    initrd_dev->impl = 0;

    root_nodes = (fs_node_t*)kmalloc(sizeof(fs_node_t) * initrd_header->nfiles);
    nroot_nodes = initrd_header->nfiles;

    // For every file...
    int i;
    for (i = 0; i < initrd_header->nfiles; i++)
    {
        // Edit the file's header - currently it holds the file offset
        // relative to the start of the ramdisk. We want it relative to the start
        // of memory.
        file_headers[i].offset += location;
        // Create a new file node.
        strcpy(root_nodes[i].name, &file_headers[i].name);
        root_nodes[i].mask = root_nodes[i].uid = root_nodes[i].gid = 0;
        root_nodes[i].length = file_headers[i].length;
        root_nodes[i].inode = i;
        root_nodes[i].flags = FS_FILE;
        root_nodes[i].read = &initrd_read;			//<----------
        root_nodes[i].write = 0;
        root_nodes[i].readdir = 0;
        root_nodes[i].finddir = 0;
        root_nodes[i].open = 0;
        root_nodes[i].close = 0;
        root_nodes[i].impl = 0;
    }
    return initrd_root;
}
```

При просмотре кода выше должно быть понятно, что загружается в виртуальную файловую систему корневая директория, dev-директория и информация о всех файлах на диске.

Виртуальная файловая система предоставляет слудющие функции:

```C
u32int read_fs(fs_node_t *node, u32int offset, u32int size, u8int *buffer);
u32int write_fs(fs_node_t *node, u32int offset, u32int size, u8int *buffer);
void open_fs(fs_node_t *node, u8int read, u8int write);
void close_fs(fs_node_t *node);
struct dirent *readdir_fs(fs_node_t *node, u32int index);
fs_node_t *finddir_fs(fs_node_t *node, char *name);
```
Реализация всех этих функций крайне проста, вот пример одной из них:
```C
u32int write_fs(fs_node_t *node, u32int offset, u32int size, u8int *buffer)
{
    // Has the node got a write callback?
    if (node->write != 0)
        return node->write(node, offset, size, buffer);
    else
        return 0;
}
```
Тут происходит следующее: файловая система от переданного в функцию узла пытается выполнить команду write, если указатель на команду не пустой (если команда предусмотрена файловой системой), то выполняем команду.


### Posix стандарты и функции

**В стандартах POSIX при открытии файла используется специальная таблица файловых дескрипторов**, которая обеспечивает более удобное взаимодействие с файлами по средством обычного целочисленного числа. 

В модуле initrd была реализована свой виртуальная файловая система, которая поддерживает функции чтения и поиска директорий и файлов. Благодаря этому можно с легкостью усовершенствовать текущую систему добавлением собственной таблицей дескрипторов. Вхождение одного элемента состоит из переменной int – флага октрытия файла и fs_node_t* указателя на файл (этот тип данных описан в документации и на сайте http://www.jamesmolloy.co.uk/tutorial_html/8.-The%20VFS%20and%20the%20initrd.html).

Далее представлены функции в стандарте POSIX, которые были реализованы (код реализаций находится в файле _vfs.c_).

```C
int open(char* pathname, int flags);
int read(int nFd, char* buf, int byte, int offset);
int close(int fd);
int write(int fd, char* buf, int size, int offset);
```

**Все эти функции – улучшение уже существующей файловой системы, которая описывается в файлах fs.h/.c initrd.h/.c**

Также используется ряд дополнительных функциий для работы с файловыми дескрипторами и таблицей

```C
int get_unused_fd();                        //ищет в таблице неиспользованный дескриптор
int reserved(int i);                        //проверяет зарезервирован ли дескриптор
void init_vfs(struct multiboot* mboot_ptr); //иницилизирует виртуальную файловую систему и таблицу файловых дескрипторов
void fd_install(int fd, fs_node_t* file);   //связывает дескриптор с указателем на файл или нод
```

**Таблица дескрипторов**

```C
struct fdt_entry_struct {
	fs_node_t* file;
	int flags;
}fdt[OPEN_MAX];
```

### Тестирование VFS

Тестирование происходит на следующих двух тестах

```C
static void process_one(registers_t reg){           //просто открываем файл и читаем из него
	int flags = 1;
	int fd;
	char buf[256];
	monitor_write("Opening Test.txt\n");
	if((fd = open("test.txt", flags)) > 0){
	        monitor_write("File test.txt opened!\n");
	}
	monitor_write("Reading the first file...\n");
	read(fd, buf, 256, 0);
	monitor_write("\t");
	monitor_write(buf);
	monitor_write("\n");
	monitor_write("Closing the first file...\n");
	close(fd);
}

void init_process_one(){
  register_interrupt_handler(0x0, &process_one);
}

static void process_two(registers_t reg){         //открываем файл, читаем его и записываем туда что-то
  int flags = 1;
  int fd;
  char buf[256];
  char* str = "New text for test2.txt";
  monitor_write("Opening test2.txt\n");
  if((fd = open("test2.txt", flags))>0){
    monitor_write("File test2.txt opened!\n");
  }
  monitor_write("Reading the second file...\n");
  read(fd, buf, 256,0);
  monitor_write("\t");
  monitor_write(buf);
  monitor_write("\n");
  monitor_write("Writing to second file...\n");
  write(fd,str,256,0);
  monitor_write("Reading the second file...\n");
  read(fd, buf, 256,0);
  monitor_write("\t");
  monitor_write(buf);
  monitor_write("\n");
  monitor_write("Closing the second file...\n");
  close(fd);
}

void init_process_two(){
  register_interrupt_handler(0x1, &process_two);
}
```
