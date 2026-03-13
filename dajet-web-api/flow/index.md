---
title: DaJet Web API
description: Администрирование конвейеров обработки и обмена данными
---

[Назад](/dajet-web-api)

Конвейеры DaJet Flow создаются по настройкам специальной фабрикой при запуске сервера DaJet. В разные моменты времени они могут находиться в разных состояниях.

**Значения свойства конвейера "State"**

|Значение|Описание|
|--------|--------|
|-1|Фабрика конвейеров не смогла создать конвейер по его описанию. В процессе сборки конвейера возникла критическая ошибка.|
|0|Конвейер успешно создан и ожидает команд - ничего не делает.|
|1|Получена команда "Старт", выполняется конфигурирование и запуск конвейера.|
|2|Конвейер выполняет свою работу.|
|3|Получена команда "Стоп", выполняется остановка работы конвейера и перевод его в режим ожидания команд.|
|4|Конвейер находится в "спящем" режиме - продолжит свою работу по истечении периода времени, указнного в настройке конвейера "SleepTimeout".|

#### GET ```/flow/monitor```

Получает список активных конвейеров, загруженных сервером DaJet.

**Запрос**
```
curl http://localhost:5000/flow/monitor
```

**Ответ**
```
[
  {
    "Uuid": "9820c413-7ed1-490e-97bd-e449420815c8",
    "Name": "dajet-script-demo",
    "Status": "",
    "Start": "0001-01-01T00:00:00",
    "Finish": "0001-01-01T00:00:00",
    "State": 0,
    "Activation": 1
  },
  {
    "Uuid": "9bfd0610-0df0-42a0-8d1c-3f74290673e2",
    "Name": "test-flow-script",
    "Status": "",
    "Start": "0001-01-01T00:00:00",
    "Finish": "0001-01-01T00:00:00",
    "State": 0,
    "Activation": 1
  }
]
```

Свойство **Activation** имеет два значения: 0 - Auto, 1 - Manual. В режиме активации **Auto** конвейер автоматически запускается при старте или перезагрузке сервера DaJet. Используется для "вечных" скриптов, работающих как сервисы. Режим **Manual** подразумевает запуск скриптов на выполнение вручную по нажатию кнопки.

#### GET ```/flow/monitor/{pipeline-uuid}```

Получает состояние конвейера по его идентификатору.

**Запрос**
```
curl http://localhost:5000/flow/monitor/9bfd0610-0df0-42a0-8d1c-3f74290673e2
```

**Ответ**
```
{
  "Uuid": "9bfd0610-0df0-42a0-8d1c-3f74290673e2",
  "Name": "test-flow-script",
  "Status": "",
  "Start": "0001-01-01T00:00:00",
  "Finish": "0001-01-01T00:00:00",
  "State": 0,
  "Activation": 1
}
```

#### POST ```/flow```

Создаёт новый конвейер DaJet Flow на сервере DaJet. В случае успеха возвращает HTTP-код 201 (Created), а в теле ответа - идентификатор вновь созданного конвейера.

>**На заметку.** Cервер DaJet необходимо перезапустить, чтобы новый конвейер стал доступным для использования.

**Запрос**
```
curl -v -X POST -H "Content-Type: application/json; charset=utf-8" -d @pipeline.json http://localhost:5000/flow
```

**Тело запроса в кодировке UTF-8, файл pipeline.json**
```
{
  "Name": "test-flow-script",
  "Activation": 1,
  "Options": [
    { "Name": "SleepTimeout",   "Type": "System.Int32",   "Value": "0" },
    { "Name": "ShowStackTrace", "Type": "System.Boolean", "Value": "true" }
  ],
  "Handlers": [
    {
      "Name": "DaJet.Flow.Script.Runner",
      "Options": [
        { "Name": "Script", "Type": "System.Int32", "Value": "code/flow-script.djs" }
      ]
    }
  ]
}
```

**Ответ**
```
< HTTP/1.1 201 Created
< Content-Type: application/json; charset=utf-8
< Date: Fri, 22 Aug 2025 18:06:50 GMT
< Server: Kestrel
< Location: test-flow-script
< Transfer-Encoding: chunked
<
"9bfd0610-0df0-42a0-8d1c-3f74290673e2"
```

#### PUT ```/flow/execute/{pipeline-uuid}```

Выполняет запуск конвейера на выполнение по его идентификатору. Возвращает HTTP-код 200 (OK) в случае успеха.

**Запрос**
```
curl -v -X PUT http://localhost:5000/flow/execute/9bfd0610-0df0-42a0-8d1c-3f74290673e2
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 0
< Date: Fri, 22 Aug 2025 18:49:15 GMT
< Server: Kestrel
```

#### PUT ```/flow/dispose/{pipeline-uuid}```

Останавливает работу конвейера по его идентификатору. Переводит конвейер в состояние ожидания команд. Возвращает HTTP-код 200 (OK) в случае успеха.

**Запрос**
```
curl -v -X PUT http://localhost:5000/flow/dispose/9bfd0610-0df0-42a0-8d1c-3f74290673e2
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 0
< Date: Fri, 22 Aug 2025 18:53:56 GMT
< Server: Kestrel
```

#### DELETE ```/flow/{pipeline-uuid}```

Удаляет конвейер из сервера DaJet. Возвращает HTTP-код 200 (OK) в случае успеха.

**Запрос**
```
curl -v -X DELETE http://localhost:5000/flow/9bfd0610-0df0-42a0-8d1c-3f74290673e2
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 0
< Date: Fri, 22 Aug 2025 19:00:19 GMT
< Server: Kestrel
```
