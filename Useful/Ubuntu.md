Полезная информация
===================

> ...

📖 [Today-I-Learned: рабочее пространство для осознанного самообразования](/README.md#til-today-i-learnedсегодня-я-узнал-вот-что)

Оглавление
===================

- Как изменить порт Apache
- Как удалить пакет
- Выключение сервера из командной строки
- Как найти публичный IP адрес вашего сервера
- Увеличение системного раздела
- Как настроить статический IP-адрес

Как изменить порт Apache
------------------------

HTTP сервер Apache – один из наиболее часто используемых веб-серверов в интернете на сегодняшний день. По своим характеристикам и многофункциональности он превосходит своих конкурентов (таких, как Nginx).
Одной из наиболее значимых функций Apache является возможность загружать и запускать различные типы модулей и конфигураций, не останавливая работу веб-сервера. Кроме того, не нужно каждый раз перекомпилировать сервер, добавляя в него новые модули. Важную роль играют .htaccess файлы, которые могут изменять конфигурации веб-сервера в определенных каталогах. В этой статье мы рассмотрим как изменить порт apache в Ubuntu и CentOS.
В системе Debian/Ubuntu, требуется изменить файл конфигурации /etc/apache2/ports.conf, а в дистрибутивах на основе RHEL/CentOS - /etc/httpd/conf/httpd.conf. В зависимости от дистрибутива системы, откройте нужный файл с помощью текстового редактора и добавьте новый порт следующим образом:

`sudo nano /etc/apache2/ports.conf`

После директивы Listen 80, дающей указание серверу прослушивать порт 80, добавить следующую строку:

`Listen 8081`

После того, как вы добавили приведенную выше строку, вам нужно создать или изменить виртуальные хосты Apache, чтобы привязать хост к нужному порту. В приведенном ниже примере мы изменим порт Apache для стандартного виртуального хоста веб-сервера с 80 на 8081. Нужно открыть и отредактировать файл /etc/apache2/sites-enabled/000-default.conf

`sudo nano /etc/apache2/sites-enabled/000-default.conf`

указав порт 8081.

```bash
<VirtualHost *:8081>
<Directory /var/www/html>
...
```

Чтобы применить изменения и новые порты Apache стали доступны, перезапустите сервис. После перезапуска порт 8081 должен отображаться в выводе утилиты ss или netstat. Команды для Ubuntu:

`sudo systemctl restart apache2`

`sudo netstat -tlpn | grep apache`

`sudo ss -tlpn | grep apache`

Настройка портов apache завершена. Теперь вы можете открыть браузер и зайти на веб-сервер, введя доменное имя или IP-адрес и порт 8081. Страница Apache должна отобразиться в браузере. Однако, если вы не можете просмотреть веб-страницу, вернитесь на консоль сервера и убедитесь, что правила брандмауэра установлены верно для разрешения сетевого трафика по данному порту:

`http://server_ip:8081`

Как удалить пакет
-----------------

В новых версиях Ubuntu для управления пакетами, а в том числе и удаления можно использовать новый менеджер пакетов - apt. Команда удаления выглядит таким образом:

`sudo apt remove имя_пакета`

Будет выполнено полное удаление пакета, включая конфигурационные файлы и зависимости, только рекомендованные программы останутся. Если вы хотите использовать apt-get чтобы удалить deb ubuntu, то порядок действий немного другой. Для обычного удаления пакета выполните:

`sudo apt-get remove имя_пакета`

Для удаления пакета вместе с его конфигурационными файлами, выполните:

`sudo apt-get purge имя_пакета`

А чтобы, также удалить зависимости, установленные вместе с пакетом, нужно после одной из предыдущих команд выполнить:

`sudo apt autoremove`

Выключение сервера из командной строки
--------------------------------------

Самая простая и самая часто используемая команда выключения компьютера linux, отключит компьютер немедленно:

```bash
sudo shutdown -h now
```

Как найти публичный IP адрес вашего сервера
-------------------------------------------

Если вы не знаете публичный IP адрес вашего сервера, его можно определить несколькими способами. Обычно, это адрес, который вы используете для соединения с вашим сервером по SSH.

Определить этот адрес можно с помощью командной строки. Сначала используйте инструмент `iproute2` для получения вашего адреса набрав следующую команду:

```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'

```

Результатом выполнения этой команды будут две или три строки, содержащие корректный адрес. Ваш компьютер, возможно, сможет использовать только один из них, поэтому попробуйте каждый вариант.

В качестве альтернативы можно узнать, как `curl` видит ваш сервер. Это можно сделать следующим образом:

```bash
sudo apt install curl
curl http://icanhazip.com

```

В независимости от метода, который вы использовали для получения своего IP адреса, вы можете использовать этот IP адрес для доступа к серверу через адресную строку веб-браузера.

Увеличение системного раздела
-----------------------------

У нас есть виртуальная машина с установленной операционной системой Ubuntu 
и требуется увеличить размер раздела, например системного. В этой статье мы рассмотрим на примере Ubuntu 20 увеличение системного раздела без LVM.

### Вводные данные

Наша Ubuntu 20 установлена на Hyper-V. Для начала проверим размер файловой системы, выполнив **df -h** (все команды выполняются от **root** пользователя):

```
# df -h
Filesystem           Size  Used Avail Use% Mounted on
tmpfs            394M         1,4M  392M            1% /run
/dev/sda3         24G         7,4G   16G           33% /
tmpfs            2,0G            0  2,0G            0% /dev/shm
tmpfs            5,0M            0  5,0M            0% /run/lock
tmpfs            4,0M            0  4,0M            0% /sys/fs/cgroup
/dev/sda2        512M         7,8M  505M            2% /boot/efi
tmpfs            394M         144K  394M            1% /run/user/1000
```

Наш системный раздел, смонтированный в **/** имеет размер 24 Гб.

Посмотрим вывод **fdisk -l**:

```
# fdisk -l
Disk /dev/sda: 25 GiB, 26843545600 bytes, 52428800 sectors
Disk model: Virtual Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: F5F02D9D-060D-422F-BA27-1981A6CA23F4

Device       Start      End  Sectors  Size Type
/dev/sda1     2048     4095     2048    1M BIOS boot
/dev/sda2     4096  1054719  1050624  513M EFI System
/dev/sda3  1054720 52426751 51372032 24.5G Linux filesystem
```

В данном примере у нас 1 диск **/dev/sda** размером 25 гигабайт, который разбит на 3 логических: **/dev/sda1**, **/dev/sda2** и **/dev/sda3** с типом **Linux filesystem** – он нас и интересует.

### Увеличение размера диска

В среде виртуализации увеличиваем размер жесткого диска нашей виртуальной машины. Скорее всего ваша система виртуализации попросит вас предварительно выключить ВМ. Я увеличил диск до 30 гигабайт, запускаем машину и проверяем:

```
# fdisk -l
Disk /dev/sda: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: Virtual Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: F5F02D9D-060D-422F-BA27-1981A6CA23F4

Device       Start      End  Sectors  Size Type
/dev/sda1     2048     4095     2048    1M BIOS boot
/dev/sda2     4096  1054719  1050624  513M EFI System
/dev/sda3  1054720 52426751 51372032 24.5G Linux filesystem
```

### Увеличение системного раздела

Внимание! Перед тем, как приступить к работам по расширению системного раздела, обязательно сделайте резервную копию данных!

После увеличения размера диска необходимо увеличить сам системный раздел. Выполним **fdisk /dev/sda**, где /dev/sda – метка нашего диска (Disk /dev/sda):

```
# fdisk /dev/sda

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help):
```

Вводим **p**, чтобы посмотреть на список разделов:

```
Command (m for help): p

Disk /dev/sda: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: Virtual Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: F5F02D9D-060D-422F-BA27-1981A6CA23F4

Device       Start      End  Sectors  Size Type
/dev/sda1     2048     4095     2048    1M BIOS boot
/dev/sda2     4096  1054719  1050624  513M EFI System
/dev/sda3  1054720 52426751 51372032 24.5G Linux filesystem
```

Чтобы расширить раздел требуется предварительно удалить информацию о нём. Для этого вводим **d** и указываем раздел (3 для /dev/sda3):

```
Command (m for help): d
Partition number (1-3, default 3): 3

Partition 3 has been deleted.
```

При этом удаляется только запись о разделе, сами данные остаются на диске!

Вводим **n** – создание нового раздела:

```
Command (m for help): n
```

Далее указываем порядковый номер раздела:

```
Partition number (3-128, default 3):3
```

Далее указываются начальный и конечный сектор. Обязательно проверьте, чтобы они совпадали со значениям, указанными через дефис. Таким образом мы используем все неразмеченное пространство:

```
First sector (1054720-62914526, default 1054720): 1054720
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1054720-62914526, default 62914526): 62914526

Created a new partition 3 of type 'Linux filesystem' and of size 29.5 GiB.
```

Как видим, был создан раздел на 29.5 гигабайт с типом **Linux filesystem**.

Также будет задан вопрос, хотим ли мы удалить текущую файловую систему. Отвечаем отказом:

```
Partition #3 contains a ext4 signature.

Do you want to remove the signature? [Y]es/[N]o: N
```

Осталось только сохранить таблицу разделов:

```
Command (m for help): w
```

Перезагружаем виртуальную машину:

```
# reboot
```

Теперь воспользуемся утилитой **resize2fs** (для **ext4**) для увеличения размера файловой системы:

```
# resize2fs /dev/sda3
resize2fs 1.45.6 (20-Mar-2020)
Filesystem at /dev/sda3 is mounted on /; on-line resizing required
old_desc_blocks = 4, new_desc_blocks = 4
The filesystem on /dev/sda3 is now 7732475 (4k) blocks long.
```

Проверяем результат:

```
# df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           394M  1.4M  392M   1% /run
/dev/sda3        29G  7.4G   21G  27% /
tmpfs           2.0G     0  2.0G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           4.0M     0  4.0M   0% /sys/fs/cgroup
/dev/sda2       512M  7.8M  505M   2% /boot/efi
tmpfs           394M   84K  394M   1% /run/user/126
tmpfs           394M   72K  394M   1% /run/user/1000
```

Как настроить статический IP-адрес
----------------------------------

**Настройка сети в Ubuntu Server 20.04 LTS** осуществляется через утилиту **Netplan**.

**NetPlan** — это инструмент для управления настройками сети, представленный в Ubuntu начиная с версии 17.10 и выше.

Этот инструмент заменяет файл статических интерфейсов `/etc/network/interfaces`, который ранее использовался для настройки сетевых интерфейсов в Ubuntu. Теперь нужно использовать `/etc/netplan/*.yaml` для ваших изменений в настройках сетевых интерфейсов.

### Определение сетевых интерфейсов

Определите все доступные сетевые интерфейсы используя команду `ip` или `lshw`:

```
sudo ip a// Илиsudo lshw -class network
```

![GitHub](/img/Useful/ubuntu.static-ip.1.png)

У меня уже настроен IP адрес: 192.169.0.150. Я изменю его.

### Настройки локальной сети

Отредактируйте файл конфигурации **netplan** который находится в директории `/etc/netplan/`:

```
sudo vim /etc/netplan/00-installer-config.yaml
```

![GitHub](/img/Useful/ubuntu.static-ip.2.png)

Здесь придётся прописать всё ручками, если у вас ещё нет доступа по SSH. У меня этот файл уже редактировался, мне необходимо изменить только IP адрес.

**Основные настройки**:

- **addresses** — ip адрес который будет назначен вашей сетевой карте.
- **gateway4** — ip адрес вашего роутера.
- **nameservers** — DNS сервера. Первый - наш роутер.
- **search** — домен в котором будет произведен поиск. Домен можно настроить при помощи DNS сервера

**Мои настройки:**

```
network: ethernets:  enp0s3:   addresses:    - 192.168.0.105/24   gateway4: 192.168.0.1   nameservers:    addresses: [192.168.0.1, 8.8.4.4]   optional: true version: 2 renderer: networkd
```

> ВАЖНО!Обратите внимание на пробелы! Именно пробелы должны быть, а не табуляция. Если у вас после сохранения файла не появилась ошибка типа: Error while loading /etc/netplan/00-installer-config.yaml, то файл отредактирован правильно с точки зрения его синтаксиса.
> 

После редактирования файла: `Esc` -> `Shift + :` -> `wq!` -> `Enter`.

### Применение конфигурации

Использование **netplan** для генерации необходимой конфигурации:

```
sudo netplan generate
```

Для подробного вывода информации при генерации, используйте опцию `--debug`:

```
sudo netplan --debug generate
```

Применение конфигурации **netplan**:

```
sudo netplan apply
```

Для подробного вывода информации при применении, используйте опцию `--debug`:

```
sudo netplan --debug apply
```

![GitHub](/img/Useful/ubuntu.static-ip.3.png)

Далее рекомендуется перезапустить интерфейсы или перезагрузить сервер:

```
sudo reboot
```

### Подключение по SSH

В **Windows PowerShell**:

```
ssh user@ip_addres
```

![GitHub](/img/Useful/ubuntu.static-ip.4.png)

Используем **Putty**. Указываем IP адрес и задаем имя сессии, жмём **Open**:

![GitHub](/img/Useful/ubuntu.static-ip.5.png)

При первом подключении к серверу мы увидим сообщение. Жмём **ДА**.

![GitHub](/img/Useful/ubuntu.static-ip.6.png)

Вводим имя пользователя и пароль. Если всё прошло успешно, увидим:

![GitHub](/img/Useful/ubuntu.static-ip.7.png)

На этом настройка статического IP-адреса в Ubuntu Server 20.04 LTS закончена.

### Пример конфигурации

Пример, который показывает большинство доступных функций:

```
network: version: 2 # if specified, can only realistically have that value, as networkd cannot # render wifi/3G. renderer: NetworkManager ethernets:  # opaque ID for physical interfaces, only referred to by other stanzas  wlp3s0:    match:     macaddress: 00:11:22:33:44:55    wakeonlan: true    dhcp4: true    addresses:     - 192.168.14.2/24     - 192.168.14.3/24     - "2001:1::1/64"    gateway4: 192.168.14.1    gateway6: "2001:1::2"    nameservers:     search: [foo.local, bar.local]     addresses: [8.8.8.8]    routes:     - to: 0.0.0.0/0      via: 11.0.0.1      table: 70      on-link: true      metric: 3    routing-policy:     - to: 10.0.0.0/8      from: 192.168.14.2/24      table: 70      priority: 100     - to: 20.0.0.0/8      from: 192.168.14.3/24      table: 70      priority: 50    # only networkd can render on-link routes and routing policies    renderer: networkd   lom:     match:      driver: ixgbe     # you are responsible for setting tight enough match rules     # that only match one device if you use set-name     set-name: lom1     dhcp6: true   switchports:     # all cards on second PCI bus unconfigured by     # themselves, will be added to br0 below     # note: globbing is not supported by NetworkManager     match:      name: enp2*     mtu: 1280 wifis:   all-wlans:     # useful on a system where you know there is     # only ever going to be one device     match: {}     access-points:      "Joe's home":       # mode defaults to "infrastructure" (client)       password: "s3kr1t"   # this creates an AP on wlp1s0 using hostapd   # no match rules, thus the ID is the interface name   wlp1s0:    access-points:     "guest":      mode: ap      # no WPA config implies default of open bridges:  # the key name is the name for virtual (created) interfaces  # no match: and set-name: allowed  br0:   # IDs of the components; switchports expands into multiple interfaces   interfaces: [wlp1s0, switchports]   dhcp4: true
```

Подробнее об использовании Netplan [здесь](https://netplan.io/examples/).
