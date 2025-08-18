[Начало](/dajet-script)

### Команда REQUEST (HTTP)

DaJet Script реализует команду **REQUEST** для выполнения HTTP-запросов, интеграции различных Web API.

Синтаксис команды следующий:

```SQL
REQUEST '<url>'
  [WHEN <condition>]
   WITH <headers>
 SELECT <options>
   INTO <variable>
```
**\<url\>** - URL адрес запроса.<br>
**\<condition\>** - условие выполнения запроса: если условие не выполняется, то запрос тоже. Предложение **WHEN** опционально.<br>
**\<headers\>** - стандартные и пользовательские заголовки запроса.<br>
**\<options\>** - параметры выполнения запроса (смотри ниже).<br>
**\<variable\>** - переменная типа ```object```, имеющая предопределённый набор свойств (смотри ниже). В эту переменную возвращается результат выполнения запроса, то есть HTTP-ответ (response).

#### Таблица параметров HTTP-запроса (request)

|**Параметр**|**Тип данных**|**Описание**|
|------------|--------------|------------|
|Method|string|Метод HTTP-запроса (GET, POST и т.п.).<br>Значение по умолчанию: 'POST'.|
|OnError|string|Опция прерывания или продолжения выполнения последующих<br>команд скрипта в случае возникновения ошибки HTTP-запроса.<br>Возможные значения: 'break' (прервать) и 'continue' (продолжить).<br>Значение по умолчанию: 'break' (прервать).|
|Content|string|Тело HTTP-запроса (текст в формате UTF-8).|

#### Таблица свойств HTTP-ответа (response)

|**Параметр**|**Тип данных**|**Описание**|
|------------|--------------|------------|
|Code|string|Стандартный код HTTP-ответа (200, 404 и т.п.).|
|Value|string|Тело HTTP-ответа (текст в формате UTF-8).|

#### Пример простого HTTP-запроса и логирования HTTP-ответа

```SQL
DECLARE @response object -- Переменная для ответа на HTTP запрос

REQUEST 'http://localhost/1c/hs/test/query'
   WITH User-Agent   = 'DaJet Script'
      , Content-Type = 'text/plain; charset=utf-8'
 SELECT Method  = 'POST'    -- HTTP метод запроса
      , OnError = 'break'   -- break или continue
      , Content = 'Привет!' -- Тело HTTP запроса
   INTO @response           -- { "Code": "200", "Value": "text" }

PRINT 'Code = ' + @response.Code
PRINT 'Body = ' + @response.Value
```

[Наверх](#команда-request-http)
