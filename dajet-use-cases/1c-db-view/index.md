---
title: DaJet Script
description: Представления СУБД (view) для 1С:Предприятие 8
---
[Начало](/dajet-use-cases)

### Генерация представлений СУБД для 1С:Предприятие 8

DaJet Script имеет встроенный процессор [DbViewProcessor](https://github.com/zhichkin/dajet/blob/main/src/dajet-runtime/extensions/DbViewProcessor.cs) генерации представлений СУБД для 1С:Предприятие 8.

Данный процессор позволяет генерировать отдельный файл скрипта ```sql```, а также создавать и удалять представления СУБД: выполнять команды CREATE VIEW или DROP VIEW непосредственно в целевой СУБД. Возможна генерация представлений, как для одиночного объекта метаданных 1С, так и для всех объектов конфигурации сразу.

> Поддерживаются MS SQL Server и PostgreSQL (специальная сборка для 1С).<br>Нюансы использования для PostgreSQL [смотри ниже](#нюансы-использования-для-postgresql).

Процессор поддерживает три команды:
- [**SCRIPT**](#генерация-файла-скрипта-sql) : Генерация скрипта ```sql``` для целевой СУБД
- [**CREATE**](#создание-представлений-субд) : Создание представлений непосредственно в СУБД
- [**DELETE**](#удаление-представлений-субд) : Удаление представлений непосредственно в СУБД

Последующие примеры выполняются при помощи [DaJet Script Host](/dajet-host). Результат выполнения - консольный вывод программы. Пример запуска скрипта на выполнение в командной строке:
```
C:\dajet>dajet ./dbv/single-object-view.djs
```

#### Генерация файла скрипта ```sql```

В результате выполнения ниже следующего скрипта будет создан файл ```Справочник.Номенклатура.sql```, который можно выполнить в целевой СУБД. Строка подключения к базе данных указывается в [команде **USE**](/dajet-script/use).

```SQL
-- **************************
-- * single-object-view.djs *
-- **************************

DECLARE @action string = 'SCRIPT' -- 'SCRIPT' 'CREATE' 'DELETE'
DECLARE @schema string = 'dbo'
DECLARE @object string = 'Справочник.Номенклатура'
DECLARE @output string = 'C:\dajet\dbv\Справочник.Номенклатура.sql'

PRINT '[DbViewProcessor] {' + @action + '} ' + @object

USE 'mssql://server/unf'
   PROCESS @action WITH DaJet.Runtime.DbViewProcessor
    SELECT SchemaName = @schema -- Схема базы данных
         , ObjectName = @object -- Полное имя объекта 1С
         , OutputFile = @output -- Файл для генерации SQL
END

PRINT '[DbViewProcessor] {DONE}'
```

**Результат работы скрипта**

```
[HOST] Running
[SCRIPT] ./dbv/single-object-view.djs
[DbViewProcessor] {SCRIPT} Справочник.Номенклатура
[DbViewProcessor] {DONE}
[200][TIME 3627 ms]
[HOST] Stopped
```

[Наверх](#генерация-представлений-субд-для-1спредприятие-8)

#### Создание представлений СУБД

В результате выполнения ниже следующего скрипта будут созданы представления СУБД для всех объектов соответствующей конфигурации 1С:Предприятие 8. В данном случае для получения списка объектов метаданных 1С используется ещё один встроенный процессор DaJet Script [Metadata Streamer](/dajet-script/md-streamer).

```SQL
-- ***************************
-- * stream-object-views.djs *
-- ***************************

DECLARE @action string = 'CREATE' -- 'SCRIPT' 'CREATE' 'DELETE'
DECLARE @schema string = 'dbo'
DECLARE @output string = 'C:\dajet\dbv\unf.sql' -- Необязательно
DECLARE @stream string = 'stream-metadata'
DECLARE @object object -- Объект метаданных конфигурации 1С:Предприятие 8
DECLARE @count number = 0

PRINT '[DbViewProcessor] {' + @action + '}'

USE 'mssql://server/unf'

   PROCESS @stream WITH DaJet.Runtime.MetadataStreamer INTO @object

   CASE WHEN @object.Тип = 'ИнформационнаяБаза' THEN
             -- Информация о конфигурации 1С:Предприятие 8
             PRINT @object.Синоним
                 + ' [' + @object.ВерсияКонфигурации + ']'
                 + ' (' + @object.РежимСовместимости + ')'
   
        -- Фильтр по объектам метаданных, для которых возможно создание представлений
        WHEN @object.Тип <> 'ОбщийРеквизит' AND @object.Тип <> 'ОпределяемыйТип' THEN
             
             PROCESS @action WITH DaJet.Runtime.DbViewProcessor
              SELECT SchemaName = @schema           -- Схема базы данных
                   , ObjectName = @object.ПолноеИмя -- Объект метаданных 1С
                   , OutputFile = @output           -- Использовать не обязательно
   END
   SET @count = @count + 1 -- Общее количество объектов конфигурации
END

PRINT '[DbViewProcessor] {' + @count + '}'
```

**Результат работы скрипта**

```
[HOST] Running
[SCRIPT] ./dbv/stream-object-views.djs
[DbViewProcessor] {CREATE}
Управление нашей фирмой, редакция 3.0 [3.0.4.45] (80321)
[DbViewProcessor] {3364}
[200][TIME 20105 ms]
[HOST] Stopped
```

[Наверх](#генерация-представлений-субд-для-1спредприятие-8)

#### Удаление представлений СУБД

В результате выполнения ниже следующего скрипта будут удалены все представления СУБД, созданные в предыдущим скриптом ```stream-object-views.djs```. В этом примере также используется процессор [Metadata Streamer](/dajet-script/md-streamer). Кроме этого демонстрируется возможность многопоточного параллельного выполнения операции удаления представлений при помощи [команды **FOR ... MAXDOP**](/dajet-script/algorithm/parallelism).

```SQL
-- *****************************
-- * parallel-object-views.djs *
-- *****************************

DECLARE @action string = 'DELETE' -- 'CREATE' 'DELETE'
DECLARE @schema string = 'dbo'
DECLARE @stream string = 'select-metadata'
DECLARE @object object -- Объект метаданных конфигурации 1С
DECLARE @config array  -- Массив всех объектов конфигурации 1С
DECLARE @count number  -- Количество объектов метаданных 1С

PRINT '[DbViewProcessor] {' + @action + '}'

USE 'mssql://server/unf'
   
   PROCESS @stream WITH DaJet.Runtime.MetadataStreamer INTO @config

   SET @count = ARRAY_COUNT(@config)

   FOR @object IN @config MAXDOP 4 -- Количество ядер CPU

      PROCESS @action WITH DaJet.Runtime.DbViewProcessor
       SELECT SchemaName = @schema           -- Схема базы данных
            , ObjectName = @object.ПолноеИмя -- Объект метаданных 1С
   END
END

PRINT '[DbViewProcessor] {' + @count + '}'
```

**Результат работы скрипта**

```
[HOST] Running
[SCRIPT] ./dbv/parallel-object-views.djs
[DbViewProcessor] {DELETE}
[DbViewProcessor] {2892}
[200][TIME 11655 ms]
[HOST] Stopped
```

[Наверх](#генерация-представлений-субд-для-1спредприятие-8)

#### Нюансы использования для PostgreSQL

Длина наименований объектов СУБД в PostgreSQL по умолчанию ограничена 63 байтами. Большая длина может быть установлена только пересборкой СУБД из исходников. Ниже приводится цитата из официальной документации PostgreSQL.

> The system uses no more than NAMEDATALEN-1 bytes of an identifier; longer names can be written in commands, but they will be truncated. By default, NAMEDATALEN is 64 so the maximum identifier length is 63 bytes. If this limit is problematic, it can be raised by changing the NAMEDATALEN constant in src/include/pg_config_manual.h.