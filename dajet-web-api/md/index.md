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

**Поддерживаемые значения для параметра ```{type}```**

<table>
  <tr><td>ОпределяемыйТип</td><td>ОбщийРеквизит</td><td>Константа</td></tr>
  <tr><td>Перечисление</td><td>Справочник</td><td>Документ</td></tr>
  <tr><td>ПланВидовХарактеристик</td><td>ПланОбмена</td><td>ПланСчетов</td></tr>
  <tr><td>РегистрСведений</td><td>РегистрНакопления</td><td>РегистрБухгалтерии</td></tr>
  <tr><td>Задача</td><td>БизнесПроцесс</td><td></td></tr>
</table>

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

Получает свойства объекта метаданных по его имени.

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
  ... и так далее ...
  "TableParts": []
}
```

#### GET ```/md/{infobase}/{type}/{name}?{details=full}```

Получает логические связи (внешние ссылки) объектов метаданных. Заполняется коллекция значений ```References``` соответствующего свойства объекта метаданных. В приведённом ниже примере HTTP-ответа это свойство ```Владелец```, которое может ссылаться на три справочника.

**Запрос**
```
curl -X GET http://localhost:5000/md/ms-demo/Справочник/БанковскиеСчета?details=full
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
      "References": [
        {
          "FullName": "Справочник.Организации"
        },
        {
          "FullName": "Справочник.Контрагенты"
        },
        {
          "FullName": "Справочник.ФизическиеЛица"
        }
      ]
    },
  ... и так далее ...
  "TableParts": []
}
```

#### GET ```/md/reset/{infobase}```

Очищает кэш метаданных на сервере DaJet по имени базы данных.

**Запрос**
```
curl -v -X GET http://localhost:5000/md/reset/ms-demo
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 0
< Date: Wed, 20 Aug 2025 19:04:32 GMT
< Server: Kestrel
```

#### GET ```/md/diagnostic/{infobase}```

Выполняет диагностику чтения метаданных платформой DaJet. Выполняется сравнение того, что DaJet прочитал, с реальной структурой базы данных. Сравниваются имена таблиц и полей СУБД. Сервер DaJet в теле ответа возвращает только несоответствия, в противном случае тело ответа пустое. Не все несоответствия являются критичными, чаще всего они не влияют на работу DaJet.

**Запрос**
```
curl -v -X GET http://localhost:5000/md/diagnostic/ms-demo
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 289
< Content-Type: text/plain; charset=utf-8
< Date: Wed, 20 Aug 2025 19:11:02 GMT
< Server: Kestrel
<
[_AccumRg29548] РегистрНакопления.ДвиженияДенежныхСредств
* insert (отсутствующие поля)
  - _dimhash
[_AccumRg8102] РегистрНакопления.Продажи
* insert (отсутствующие поля)
  - _dimhash
```

#### POST ```/md```

Регистрирует новую базу данных на сервере DaJet. В случае успеха возвращается HTTP-код 201 (Created), а в теле ответа - идентификатор записи в реестре баз данных сервера DaJet, который был передан в свойстве "Identity". Все свойства объекта JSON обязательны для заполнения.

**Запрос**
```
curl -v -X POST -H "Content-Type: application/json; charset=utf-8" -d @infobase.json http://localhost:5000/md
```

**Тело запроса в кодировке UTF-8, файл infobase.json**
```
{
  "Code": 50,
  "Identity": "78ffd48e-93d6-4628-8b5d-ef3a8c9569c0",
  "Name": "new-infobase",
  "Description": "Новая информационная база данных",
  "UseExtensions": false,
  "DatabaseProvider": "SqlServer",
  "ConnectionString": "Data Source=server;Initial Catalog=database;Integrated Security=True;Encrypt=False;"
}
```

**Ответ**
```
< HTTP/1.1 201 Created
< Content-Type: application/json; charset=utf-8
< Date: Wed, 20 Aug 2025 19:23:40 GMT
< Server: Kestrel
< Location: new-infobase
< Transfer-Encoding: chunked
<
"78ffd48e-93d6-4628-8b5d-ef3a8c9569c0"
```

#### PUT ```/md```

Обновляет свойства существующей базы данных на сервере DaJet. Значение свойства "Identity" обязательно должно соответствовать тому, что записано в реестре баз данных сервера DaJet, иначе будет возвращена ошибка 404 (Not found). Все свойства объекта JSON обязательны для заполнения.

**Запрос**
```
curl -v -X PUT -H "Content-Type: application/json; charset=utf-8" -d @infobase.json http://localhost:5000/md
```

**Тело запроса в кодировке UTF-8, файл infobase.json**
```
{
  "Code": 50,
  "Identity": "78ffd48e-93d6-4628-8b5d-ef3a8c9569c0",
  "Name": "updated-infobase",
  "Description": "Обновлённая информационная база данных",
  "UseExtensions": false,
  "DatabaseProvider": "SqlServer",
  "ConnectionString": "Data Source=server;Initial Catalog=database;Integrated Security=True;Encrypt=False;"
}
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 0
< Date: Thu, 20 Aug 2025 19:23:41 GMT
< Server: Kestrel
```

#### DELETE ```/md```

Удаляет существующую запись в реестре баз данных сервера DaJet. Поиск выполняется по значению свойства "Identity". Если указанный идентификатор отсутствует, то будет возвращена ошибка 404 (Not found). Все свойства объекта JSON обязательны для заполнения.

**Запрос**
```
curl -v -X DELETE -H "Content-Type: application/json; charset=utf-8" -d @infobase.json http://localhost:5000/md
```

**Тело запроса в кодировке UTF-8, файл infobase.json**
```
{
  "Code": 50,
  "Identity": "78ffd48e-93d6-4628-8b5d-ef3a8c9569c0",
  "Name": "updated-infobase",
  "Description": "Удаление базы данных из реестра сервера DaJet",
  "UseExtensions": false,
  "DatabaseProvider": "SqlServer",
  "ConnectionString": "Data Source=server;Initial Catalog=database;Integrated Security=True;Encrypt=False;"
}
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 0
< Date: Thu, 20 Aug 2025 19:23:45 GMT
< Server: Kestrel
```
