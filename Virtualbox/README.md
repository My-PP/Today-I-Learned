Начало работы с Virtualbox
==========================

> VirtualBox – это специальное средство для виртуализации, позволяющее запускать операционную систему внутри другой. Оно поставляется в двух версиях – с открытым и закрытым исходным кодом. С помощью VirtualBox мы можем не только запускать ОС, но и настраивать сеть, обмениваться файлами и делать многое другое.

📖 [Today-I-Learned: рабочее пространство для осознанного самообразования](/README.md#til-today-i-learnedсегодня-я-узнал-вот-что)

Оглавление
----------

- [Перенос виртуальной машины из Hyper-V в VirtualBox](#перенос-виртуальной-машины-из-hyper-v-в-virtualbox-конвертируем-виртуальный-жёсткий-диск-vhd-в-vdi)

Перенос виртуальной машины из Hyper-V в VirtualBox: конвертируем виртуальный жёсткий диск VHD в VDI
---------------------------------------------------------------------------------------------------

Если у вас есть виртуальная машина, созданная в Microsoft Hyper-V, то при необходимости ее можно перенести на сервер Oracle VirtualBox. В плане форматов VirtualBox достаточно универсален, он поддерживает все более-менее известные форматы виртуальных дисков, и, что более интересно, умеет преобразовывать их из одного формата в другой.

Сделать это можно из командной строки, с помощью утилиты vboxmanage.exe. Формат команды такой:

```powershell
vboxmanage.exe clonemedium disk filename.vhdx filename.vdi −−format vdi
```

Для примера возьмем виртуальный дискWindows2012R2.vhdx и сконвертируем его в формат VDI:

```powershell
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" clonemedium disk "C:\VMs\Windows2012R2.vhdx" "C:\VMs\Windows2012R2.vdi" --format vdi
```

📖 [Начало работы с Virtualbox](#оглавление) | [Today-I-Learned](/README.md#til-today-i-learnedсегодня-я-узнал-вот-что)
