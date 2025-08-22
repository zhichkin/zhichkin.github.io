---
title: DaJet Web API
description: Администрирование конвейеров обработки и обмена данными
---

[Назад](/dajet-web-api)

#### GET ```/flow/{infobase}```

Получает список установленных на сервере DaJet конвейеров.

**Запрос**
```
curl -X GET http://localhost:5000/flow
```

**Ответ**
```
[
  {
    "Identity": "575842b9-1d09-11f0-9d46-3c64cfca4840",
    "IsActive": true,
    "Version": null,
    "Order": 1,
    "Scope": 1,
    "Purpose": 1,
    "RootFile": "a6d1218720ded63f052e943223ce1c82a59f6fdf",
    "FileName": null,
    "Updated": "2025-08-15T13:43:59",
    "MasterNode": "0:00000000000000000000000000000000",
    "IsDistributed": false,
    "Uuid": "00000000-0000-0000-0000-000000000000",
    "Parent": "00000000-0000-0000-0000-000000000000",
    "Name": "Расширение1",
    "Alias": "Расширение1",
    "Comment": ""
  }
]
```
