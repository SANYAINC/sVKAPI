# sVKAPI - Easy lib for studying vk api

Еще одна библиотека для работы с VK API.
Пока ее нигде нет, нужно просто добавить папку sVKAPI в своей проект:

`from sVKAPI.core import *`

## Инициализация

Для начала нужно создать объект для работы с api: `apiObj = API()`,
после чего необходимо СРАЗУ пройти аутентификацию. Сделать это можно тремя способами:
1. `apiObj.authenticate(username = "...", password = "...")` - идентификация через логин и пароль https запросами. После
успешной авторизации будет сохранена сессия и токен.
2. `apiObj.authenticate(token = "...")` или `apiObj.setToken("...")` - прямая передача токена в обьект.
3. `apiObj.authenticate()` - загрузка автоматически сохраненного токена из файла.

_Обратите внимание:_ Объект `apiObj` персистентный, т.е сохраняет данные авторизации (токен или сессия)
 в файл после выхода из программы (в зашифрованном виде). Таким образом пройдя авторизацию один раз, все последующие можно проходить
 3-им способом.
 
 **Важно!** Используя **1-ый** метод, либо **3-ий** посредством **1-го**, вам будет
 недоступна секция _Messages_, и, следовательно, _Long Poll_.
 
## Работа с API
### Стандартные запросы
Для стандартного запроса необходимо вызвать `apiObj.call(_)`, в который 
передать название метода, и необходимые для него параметры (**кроме токена и версии API**), например:

`apiObj.call("friends.get", user_id = 175486984, fields = "city")`. 

В качестве ответа мы получим словарь из всех друзей пользователя _id175486984_,
содержащий информацию о городе.

Со всеми методами VK API можно ознакомиться [тут](https://vk.com/dev/methods). 

### Long Poll

Для работы с Long Poll VK необходимо сначала получить информацию о сервере для _прослушки_. 
Метод `apiObj.setLongPollServer(_)` сам установит все необходимые параметры для объекта `apiObj`, 
либо выбросит исключение. После получения данных о сервере можно работать с `apiObj.longPoll(_)`.
Метод посылает запрос на сервер и ждет, пока сервер не вернет какие-либо события. Отправленные сервером данные
мы получаем в виде словаря ([подробнее](https://vk.com/dev/using_longpoll)), который можем обработать. 
Пример использования:

```
from sVKAPI.core import *


apiObj = API()
apiObj.authenticate(token = "...")
apiObj.setLongPollServer()

while True:
    updates = apiObj.longPoll()
    print(updates)

#   Ответ 
#   {
#   'ts': 1764496822,
#   'updates': [[4, 747386, 17, 175486984, 1589673104, 'Hello', {'title': ' ... '}, {}, 2100133870]]
#   }
#   означает, что в 1589673104 (timestamp) нам пришло сообщение с ID 747386 и флагами 17 от 175486984,
#   в котором содержится Hello
```

<hr>



#Для заметок

Если проблемы с авторизацией, попробуйте удалить файлы .encrypted в папке `sVKAPI/data`


