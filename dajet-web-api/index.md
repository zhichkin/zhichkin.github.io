---
title: DaJet Web API
description: Управление сервером DaJet и создание собственных Web API
---
[Главная](/#dajet-web-api)

Примеры использования **DaJet Web API** демонстрируются в документации при помощи утилиты [**CURL**](https://curl.se/). Возможно также их воспроизведение при помощи [**DaJet Studio**](/dajet-studio). Для этого используйте команду [**REQUEST**](/dajet-script/request) DaJet Script:

```
DECLARE @response object -- Переменная для HTTP-ответа

-- Можно вызывать "самого себя"
REQUEST 'http://localhost:5000/md'
   WITH User-Agent   = 'DaJet Script'
      , Content-Type = 'application/json; charset=utf-8'
 SELECT Method  = 'GET'     -- HTTP-метод запроса
      , Content = 'Привет!' -- Тело HTTP-запроса
   INTO @response           -- HTTP-ответ сервера

-- Вывод в лог сервера DaJet (dajet.log)
-- PRINT 'Code = ' + @response.Code
-- PRINT 'Body = ' + @response.Value

-- Вывод в интерфейс DaJet Studio
RETURN JSON(@response.Value)
```

### Сервис /md

- Администрирование списка баз данных на сервере DaJet
- Чтение метаданных конфигураций 1С:Предприятие 8
- Чтение логической структуры (связей) метаданных
- Управление кэшем метаданных на сервере DaJet
- Диагностика правильности/полноты чтения метаданных

[Подробнее](/dajet-web-api/md)

### Сервис /mdex

Чтение метаданных расширений конфигураций 1С:Предприятие 8

[Подробнее](/dajet-web-api/mdex)

### Сервис /dajet

Администрирование и выполнение скриптов DaJet Script

[Подробнее](/dajet-web-api/dajet)

### Сервис /flow

Администрирование конвейеров обработки и обмена данными

[Подробнее](/dajet-web-api/flow)

### Сервис /db
- Администрирование объектов СУБД (ограниченно)
- Генератор представлений СУБД для 1С:Предприятие 8
