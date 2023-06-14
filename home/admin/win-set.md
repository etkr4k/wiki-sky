---
title: Windows
description: 
published: true
date: 2022-11-29T14:36:03.815Z
tags: 
editor: markdown
dateCreated: 2022-07-11T09:10:49.797Z
---

# Webadmin

### WinRM
`WinRM enumerate winrm/config/listener` 

`winrm quickconfig`
# PowerShell

## Удаление файлов через n дней
`FORFILES /p "full patch" /s /m *.* /d -7 /c "CMD /c del /Q @FILE"`

`powershell.exe -executionpolicy bypass -file .\script-name.ps1`

## Виртуальный диск для ЭЦП

Создаем батник
Добавляем виртуальный диск по задонуму пути
`subst [<drive1>: [<drive2>:]<path>]` 

Удаляем виртуальный диск по задонуму пути
`subst <drive1>: /d` 

Пример:

`subst x: c:\user\user\desktop`
  
# Create installation media FAT32
Запускаем PowerShell
Запускаем DISKPART
`DISKPART`
Определяем номер флэшки
`list disk`
Выбираем флэшку, в примере нужная располагается под номером 2
`select disk 2`
Очищаем флэшку от таблицы разделов
`clean`
Создаем первичнный раздел
`create partition primary`
Делаем созданный раздел активным
`active`
Форматируем созданный раздел
`format fs=FAT32 quick`
Монтируем созданный раздел (присваивается первая свободная буква (F:\)
`assign`
Выходим из diskpart
`exit`
Монтируем образ Windows (присваивается первая свободная буква (G:\)
`Mount-DiskImage -ImagePath "C:\Local\ru_windows_10_enterprise_ltsc_2019_x64_dvd_78e7853a.iso"`
Копируем установочный носитель на флэшку
`ROBOCOPY G:\ F:\ /S /E /PURGE /ZB /MT:32 /MAX:3800000000`
Получаем информацию о файле install.wim
`DISM /Get-WimInfo /WimFile:G:\sources\install.wim`
Разделяем install.wim для соответствия с FAT32
`DISM /Split-Image /ImageFile:G:\sources\install.wim /SWMFile:F:\sources\install.swm /FileSize:3800`
Размонтируем образ Windows
`Dismount-DiskImage -ImagePath "C:\Local\ru_windows_10_enterprise_ltsc_2019_x64_dvd_78e7853a.iso"`
# READONLY DRIVE
`attributes disk`
`attributes disk set readonly`
`attributes disk clear readonly`
# Удаление дефолтного мусора Windows
 -= Powershell =-

POWERSHELL -ExecutionPolicy Bypass -File .\remove_default_apps.ps1




# Установка и настройка Windows Server 2019
## Изменение дериктории для Users Profile
**HKLM\Software\Microsoft\WindowsNT\CurrentVersion\ProfileList**
Значение параметра «ProfilesDirectory» (по умолчанию «%SystemDrive%\Users») меняем, например на «D:\Profiles». 
После этого перезагружаем сервер

## Включение Windows Photo Viewer
**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Photo Viewer\Capabilities\FileAssociations**
Добавить аналогичные строки для .png .jpeg .jpg и т.д.


## Reset Grace Period
**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\GracePeriod**
Удалить "L$RTMTIMEBOMB"

# Remove RUS-ENG Keyboard

- Подключаемся к удалённому рабочему столу
- Нажимаем кнопку **Пуск**
- вводим **regedit**
- справа выбираем **Запуск от имени администратора**
- открываем путь **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout**
- создаём параметр **IgnoreRemoteKeyboardLayout**
    - для этого нажимаем справа Правой Кнопкой Мышки
    - выбираем **Создать**
    - далее **Параметр DWORD (32 бита)**
    - Новый параметр #1 переименовываем в **IgnoreRemoteKeyboardLayout**
- меняем его значение с **0** на **1**, открыв его двойным щелчком
- закрываем все программы, сохраняем документы
- завершаем сеанс, заново переподключаемся.
- Теперь у вас только русская раскладка РУС и английская ENG

---

Также можно создать reg-файл в **Блокноте**, затем простым двойным щелчком добавить в реестр без копания по его веткам и параметрам. Для этого выполняем следующее:

Открываем **Блокнот**, Вставляем туда следующий текст

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
"IgnoreRemoteKeyboardLayout"=dword:00000001
```

- В меню выбираем **Файл** — **Сохранить как**…
- В выпадающем списке **Тип файла** выбираем **Все файлы (*.*)**
- **Имя файла** пишем любое название, но в конце добавляем **.reg**








