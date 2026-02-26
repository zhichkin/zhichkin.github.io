[Начало](/dajet-script)

### Строки подключения к базам данных

> Доступно, начиная с версии DaJet 3.13.5

Для команд [USE](/dajet-script/use) и [REQUEST](/dajet-script/request-sql) доступно использование "родных" строк подключения.

Для использования "родных" строк подключения формат команды имеет следующий вид:

```SQL
USE '[<database_provider>]<connection_string>'
REQUEST '[<database_provider>]<connection_string>'
```
[Документация для строк подключения Sqlite](https://learn.microsoft.com/en-us/dotnet/standard/data/sqlite/connection-strings)

**\<database_provider\>** - провайдер данных:<br>
 - [mssql]  - SQL Server без расширений конфигурации 1С:Предприятие 8
 - [mssqlx] - SQL Server с подключением расширений 1С:Предприятие 8
 - [pgsql]  - PostgreSQL без расширений конфигурации 1С:Предприятие 8
 - [pgsqlx] - PostgreSQL с подключением расширений 1С:Предприятие 8
 - [sqlite] - Sqlite (только для команды REQUEST)

**\<connection_string\>** - строка подключения соответствующего провайдера данных.

#### Строка подключения SQL Server

```SQL
DECLARE @record object

USE '[mssql]Data Source=my_server;Initial Catalog=my_database;Integrated Security=True;Encrypt=False'
  SELECT TOP 1 Наименование
    INTO @record
    FROM Справочник.Номенклатура
  RETURN @record
END
```

#### Строка подключения PostgreSQL

```SQL
DECLARE @record   object
DECLARE @provider string = 'pgsql'
DECLARE @database string = 'Host=127.0.0.1;Port=5432;Database=unf;Username=postgres;Password=postgres'

USE '[{@provider}]{@database}'
   SELECT TOP 1 Наименование
     INTO @record
     FROM Справочник.Номенклатура
   RETURN @record
END
```

#### Строка подключения Sqlite

```SQL
DEFINE Запись(
    Идентификатор string,
    Владелец      string,
    Родитель      string,
    ЭтоКаталог    integer,
    Имя           string,
    Скрипт        string
)
DECLARE @НаборЗаписей array OF Запись
DECLARE @Идентификатор string
DECLARE @provider string = 'sqlite'
DECLARE @database string = 'Data Source=C:\dajet-studio-windows\dajet.db;Mode=ReadOnly'

SET @Идентификатор = '00befd3c-6dac-40e1-84ac-' + '0331538551d7'

REQUEST '[{@provider}]{@database}'
   WITH Script = 'SELECT uuid, owner, parent, is_folder, name, script FROM scripts WHERE uuid = @Идентификатор'
 SELECT Идентификатор = @Идентификатор
   INTO @НаборЗаписей

RETURN @НаборЗаписей
```

[Наверх](#строки-подключения-к-базам-данных)
