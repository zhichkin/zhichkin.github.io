---
title: DaJet Web API
description: Сервис /md
---

[Назад](/dajet-web-api)

#### GET ```/md```

Получает список, зарегистрированных на сервере DaJet, баз данных.

**Запрос**
```
curl -X GET http://localhost:5000/md
```

**Ответ**
```
[
  {
    "Name": "ms-demo",
    "Description": "",
    "UseExtensions": false,
    "DatabaseProvider": "SqlServer",
    "ConnectionString": "Data Source=server;Initial Catalog=unf;Integrated Security=True;Encrypt=False;",
    "TypeCode": 50,
    "Identity": "e0539cc0-cccc-4423-88a5-a601ca1ad74f"
  },
  {
    "Name": "pg-demo",
    "Description": "",
    "UseExtensions": false,
    "DatabaseProvider": "PostgreSql",
    "ConnectionString": "Host=127.0.0.1;Port=5432;Database=unf;Username=postgres;Password=postgres;",
    "TypeCode": 50,
    "Identity": "955c1791-dd7e-4207-b082-ebfde236eac9"
  }
]
```

#### GET ```/md/{infobase}```

Получает свойства конфигурации базы данных по её имени.

**Запрос**
```
curl -X GET http://localhost:5000/md/ms-demo
```

**Ответ**
```
{
  "PlatformVersion": 80313,
  "CompatibilityVersion": 80321,
  "AppConfigVersion": "3.0.4.45",
  "Description": "Управление нашей фирмой, редакция 3.0",
  "DetailedDescription": "Управление нашей фирмой, редакция 3.0",
  "Provider": "Фирма \u00221С\u0022",
  "YearOffset": 0,
  "SyncCallsMode": 0,
  "DataLockingMode": 1,
  "ModalWindowMode": 2,
  "AutoNumberingMode": 1,
  "UICompatibilityMode": 2,
  "NamePrefix": null,
  "MapMetadataByUuid": true,
  "ExtensionCompatibility": 0,
  "Uuid": "de29c81d-d880-419b-8f65-cc49813f0e9a",
  "Parent": "00000000-0000-0000-0000-000000000000",
  "Name": "УправлениеНебольшойФирмой",
  "Alias": "Управление нашей фирмой, редакция 3.0",
  "Comment": ""
}
```

#### GET ```/md/{infobase}/{type}```

Получает список объектов метаданных определённого типа.

**Запрос**
```
curl -X GET http://localhost:5000/md/ms-demo/Справочник
```

**Ответ**
```
[
  {
    "Type": "cf4abea6-37b2-11d4-940f-008048da11f9",
    "Uuid": "40045984-a54c-42bd-8ea8-1c10672f40ec",
    "Name": "АвансовыйОтчетПрисоединенныеФайлы"
  },
  {
    "Type": "cf4abea6-37b2-11d4-940f-008048da11f9",
    "Uuid": "b8c0e639-11ba-4822-ab0b-0555e960d559",
    "Name": "АвансовыйПлатежИностранцаПоНДФЛПрисоединенныеФайлы"
  },
  ... и так далее ...
]
```

#### GET ```/md/{infobase}/{type}/{name}```

Получает свойства объекта метаданных определённого типа.

**Запрос**
```
curl -X GET http://localhost:5000/md/ms-demo/Справочник/БанковскиеСчета
```

**Ответ**
```
{
  "Code": 21,
  "Uuid": "46bd4919-eaaa-4d20-9448-1c15fffa60a4",
  "Type": "Справочник",
  "Name": "БанковскиеСчета",
  "Alias": "Банковские счета",
  "DbTable": "_Reference21",
  "Comment": "",
  "FullName": "Справочник.БанковскиеСчета",
  "Properties": [
    {
      "Uuid": "43078d73-5cc3-4032-af67-5a0e45e8dd09",
      "Name": "НомерСчета",
      "Alias": "Номер счета",
      "Comment": "",
      "Purpose": "Реквизит",
      "Columns": [
        {
          "DbName": "_Fld626",
          "Purpose": "Значение"
        }
      ],
      "DataType": [
        {
          "Type": "Строка(34,Переменная)"
        }
      ],
      "References": []
    },
    ... и так далее ...
    {
      "Uuid": "00000000-0000-0000-0000-000000000000",
      "Name": "Владелец",
      "Alias": "",
      "Comment": "",
      "Purpose": "СтандартныйРеквизит",
      "Columns": [
        {
          "DbName": "_OwnerID_TYPE",
          "Purpose": "Дискриминатор"
        },
        {
          "DbName": "_OwnerID_RTRef",
          "Purpose": "КодСсылки"
        },
        {
          "DbName": "_OwnerID_RRRef",
          "Purpose": "Ссылка"
        }
      ],
      "DataType": [
        {
          "Type": "Ссылка"
        }
      ],
      "References": []
    },
  "TableParts": []
}
```
