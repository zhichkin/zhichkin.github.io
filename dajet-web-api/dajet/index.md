---
title: DaJet Web API
description: Администрирование и выполнение скриптов DaJet Script
---

[Назад](/dajet-web-api)

[Управление каталогами скриптов]

[Управление файлами скриптов]

#### GET ```/dajet/ping```

Проверка соединения с сервером DaJet. Возвращает текущую дату сервера DaJet.

**Запрос**
```
curl -v http://localhost:5000/dajet/ping
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 23
< Content-Type: text/plain; charset=utf-8
< Date: Thu, 21 Aug 2025 18:51:37 GMT
< Server: Kestrel
<
2025-08-21 21:51:38:069
```

#### GET ```/dajet/log```

Возвращает содержание файла ```dajet.log```, расположенного в корневом каталоге сервера DaJet.

**Запрос**
```
curl -v http://localhost:5000/dajet/log
```

**Ответ**
```
< HTTP/1.1 200 OK
< Content-Length: 48
< Content-Type: text/plain; charset=utf-8
< Date: Thu, 21 Aug 2025 18:57:04 GMT
< Server: Kestrel
<
[2025-08-21 21:56:57] Hello from DaJet Script!
```

#### POST ```/dajet/exe/{**path}```

Выполняет указанный в параметре ```{**path}``` скрипт. Входящие параметры передаются в теле HTTP-запроса как объект JSON. Если скрипт не принимает входящих параметров, то тело HTTP-запроса должно быть пустым. Результат работы скрипта возвращается в теле HTTP-ответа. Подробно процесс вызова скрипта DaJet Script по HTTP описан в [документации](/dajet-script/http).

> **Важно!** Вызываемый скрипт обязательно должен возвращать значение типа ```object```, либо не возвращать ничего (не использовать команду RETURN).

**Пример скрипта say-hello.djs**
```
DECLARE @input  string -- Входящий параметр
DECLARE @response object -- Тело HTTP-ответа

-- Формируем объект HTTP-ответа
MODIFY @response SELECT value = 'Привет, ' + @input + '!'

RETURN @response
```

**Запрос**
```
curl -X POST -H "Content-Type: application/json; charset=utf-8" -d @parameters.json http://localhost:5000/dajet/exe/say-hello.djs
```

**Тело запроса в кодировке UTF-8, файл parameters.json**
```
{
  "input": "DaJet Script"
}
```

**Ответ**
```
{
  "value": "Привет, DaJet Script!"
}
```