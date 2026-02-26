[Начало](/dajet-script)

### Вызов скриптов по HTTP

Скрипты DaJet Script можно вызвать удалённо по протоколу ```HTTP/S```. Для этого необходимо разместить файлы скриптов в каталоге ```code``` корневого каталога [web-сервера DaJet](/dajet-studio). Например, создадим в каталоге ```code``` вложенный каталог ```api``` и поместим в него файл скрипта ```test.djs```.

> Если в корневом каталоге сервера DaJet каталог ```code``` отсутствует, то создайте его вручную.

**Скрипт test.djs**

```SQL
DECLARE @input  string -- Входящий параметр
DECLARE @output object -- Тело HTTP-ответа

SET @output = SELECT say = 'Hello, ' + @input + '!'

RETURN @output
```

Вызвать этот скрипт можно, обратившись к сервису ```dajet/exe``` http-сервера DaJet. Путь к скрипту для этого сервиса будет выглядеть следующим образом: ```/api/test.djs```. Сервис обслуживает каталог ```code``` - указывать его в URL не нужно.

#### Пример вызова скрипта ```test.djs``` по http при помощи программы Postman

![dajet-script-http-postman](/dajet-script/http/img/postman-example.png)

#### Пример вызова скрипта ```test.djs``` по http при помощи DaJet Studio

```SQL
-- ***************
-- * request.djs *
-- ***************
DECLARE @request  object -- Тело http-запроса
DECLARE @response object -- Тело http-ответа

SET @request = SELECT input = 'DaJet'

REQUEST 'http://localhost:5000/dajet/exe/api/test.djs'
   WITH Content-Type = 'text/plain;charset=utf-8'
 SELECT Method  = 'POST'         -- HTTP метод запроса
      , Content = JSON(@request) -- Тело HTTP запроса
   INTO @response

PRINT @response.Value.say

-- Результат выполнения скрипта в логе http-сервера DaJet
[2024-10-12 21:24:21] Hello, DaJet!
```

#### Пример вызова скрипта ```test.djs``` по http при помощи утилиты ```curl```

![dajet-script-http-curl](/dajet-script/http/img/curl-example.png)

[Наверх](#вызов-скриптов-по-http)
