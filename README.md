В этом гайде мы покажем принципы работы и настроим API Google Sheets. Создадим Whatsapp бота на NodeJS, используя наш шлюз, который будет связан с Гугл Таблицами.
В данном примере бот будет реагировать на команды, поступающие ему в виде обычных сообщений в WhatsApp и отвечать на них либо выполнять команду. Не забудьте скачать готового бота с нашего репозитория и использовать его в своей работе!

### Какой функционал у данного бота?

1.  Запись информации в таблицу;
2.  Считывание информации из таблицы;
3.  Рассылка сообщений по номерам из таблицы;
4.  Отправка файла из ячейки таблицы;

<div class="anchor-box" id="Google">

## Подготовка и настройка сервисов Google

[Официальная статья "Быстрый старт" на NodeJS от Google](https://developers.Google.com/sheets/api/quickstart/nodejs?hl=ru) содержит в себе пример работы с API. Возьмем её за основу.

### Настройка аккаунта на работу с API

Прежде всего необходимо зайти на [сайт разработчиков](https://console.developers.Google.com/) и создать новый проект.

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-1.png) <span class="image-caption">Создание проекта в Google APIs</span>](https://chat-api.com/img/googlesheets/google-api-sheet-1.png) </div>

После создания проекта, необходимо выбрать вкладку "Библиотека".

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-3.png) <span class="image-caption">Открываем Библиотеку API</span>](https://chat-api.com/img/googlesheets/google-api-sheet-3.png) </div>

И в списке выбрать Google Sheets API. После чего нужно нажать на кнопку "Включить" ("Enable")

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-5.png) <span class="image-caption">Кнопка "Включить"</span>](https://chat-api.com/img/googlesheets/google-api-sheet-5.png) </div>

Должно произойти перенаправление на страницу настроек данного API. На этой странице, необходимо будет создать доступы. Нажимаем на кнопку "Создать учетные данные" ("Create credentials").

В появившемся окне нужно выбрать настройки для сервисного аккаунта и нажать на кнопку "Выбрать тип учетных данных". Рекомендуем использовать такие, как на скриншоте:

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-7.png) <span class="image-caption">Рекомендуем использовать эти настройки для сервисного аккаунта</span>](https://chat-api.com/img/googlesheets/google-api-sheet-7.png) </div>

Далее необходимо задать имя аккаунту и роль. Необходимо выбрать роль **Редактор**.

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-8.png) <span class="image-caption">Добавление учетных данных. Выбираем роль - Редактор</span>](https://chat-api.com/img/googlesheets/google-api-sheet-8.png) </div>

Тип ключа - _JSON_. Нажимаем продолжить. Будет предложено скачать JSON файл с данными. Сохраняем его в папку с проектом, переименовываем в **keys.json** для более удобного обращения к нему из проекта.

На этом настройка аккаунтов практически закончена. Осталось лишь назначить в качестве редактора только что созданный аккаунт в нашей Google таблице. Для этого откроем её и нажмем на кнопку "Настройки доступа".

Необходимо добавить аккаунт, который мы создали в качестве редактора данной таблицы. Для этого нужно посмотреть его email в [консоли разработчика](https://console.developers.Google.com/) во вкладке "Учетные данные" и скопировать почту.

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-10.png) <span class="image-caption">Учетные данные, откуда нужно скопировать email</span>](https://chat-api.com/img/googlesheets/google-api-sheet-10.png) </div>

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-11.png) <span class="image-caption">Назначение редактором созданный аккаунт</span>](https://chat-api.com/img/googlesheets/google-api-sheet-11.png) </div>

На этом настройку сервисов можно считать законченной. Перейдем к самому проекту.

</div>

<div class="anchor-box" id="Googleapi">

## Создадим модуль для работы с Google API

Для начала создадим файл **config.js** в котором мы будем хранить конфигурационные данные для бота. И запишем в него ID нашей Google таблицы. Посмотреть его можно в адресной строке.

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-12.png) <span class="image-caption">Как найти ID Google таблицы</span>](https://chat-api.com/img/googlesheets/google-api-sheet-12.png) </div>

### **config.js**

    module.exports = {
        spreadid:"1M6fyEhv64ug7zILRz86H1PBKEKHUgKV9pWSW2m_r4SI",  // ID Google таблицы
    }

После чего создадим файл **Googleapi.js**. В нем у нас будут храниться все функции и данные по работе с API Google. Для начала необходимо установить модуль по работе с Google Api для NodeJS.  
Введем команду **npm install Googleapis@39 --save** в терминале, для установки данного модуля. В самом файле импортируем зависимости.

    const config = require("./config.js");
    const {Google} = require('Googleapis');
    const keys = require('./keys.json');

И создадим объект клиента, который будет авторизовывать нас в Google.

    const client = new Google.auth.JWT(
        keys.client_email,
        null,
        keys.private_key,
        ['https://www.Googleapis.com/auth/spreadsheets']
    ) //Json Web Token

Параметры, которые принимает функция _JWT_:

*   Email из json файла с доступами;
*   Путь до файла с закрытым ключом (мы его не передаем, поэтому null);
*   Приватный ключ из json файла с доступами;
*   Список доступов. У нас из доступов только Google sheets. При необходимости передаются в этот список и другие API от Google.

Далее необходимо вызвать функцию, которая нас авторизует в системе.

    client.authorize(function(err, tokens) {
        if (err){
            console.log(err);
            return;
        }

        console.log('Connected Google Sheets Api!');
        gsrun(client);
    });

    let gsapi;

    async function gsrun(cl){
        gsapi = Google.sheets({version:'v4', auth:cl})
    }

Если все успешно прошло, то выводим в консоль "Connected Google Sheets Api!" и записываем в объект _gsapi_ класс _Sheets_, который в параметры принимает версию используемого API и объект _Client_, который мы создавали ранее. После этого нам остается описать функции, которые будут работать с данными.

### **Метод для получения данных**

    async function getValues(range)
    {
        const opt = {
            spreadsheetId: config.spreadid,
            range : range
        }

        let data = await gsapi.spreadsheets.values.get(opt);
        let dataArray = data.data.values;

        return dataArray;
    }

Для того, чтобы получать данные из таблицы, мы напишем функцию. В параметры она принимает диапазон ячеек в таком формате: **"Лист1!A1:B2"**, где Лист1 - это имя вашего листа в таблице. Будьте, внимательны, когда указываете этот параметр.

**opt** - Это словарь параметров, которые мы передаем в запрос к Api Google.

*   _spreadsheetId_ - id таблицы;
*   _range_ - Диапазон значений, откуда извлекать информацию;

Для того, чтобы извлечь данные из таблицы, _opt_ нужно передать в метод _gsapi.spreadsheets.values.get(opt);_

Данный метод возвращает всю информацию о запросе, а конкретно данные хранит в _data.values_

Теперь напишем метод, который позволит нам записывать данные в таблицу. Чтобы записывать данные в конец таблицы, нам нужно сначала узнать номер последней строки. API не позволяет этого сделать напрямую, поэтому мы сначала опишем метод, который будет возвращать номер последней строки, прежде чем записывать данные.

    async function getLastRow() // Получить номер последней строки в таблице
    {
        const opt = {
            spreadsheetId: config.spreadid,
            range: 'Data!A1:A'
        }
        let response = await gsapi.spreadsheets.values.get(opt);
        return response.data.values.length;
    }

Его суть в том, чтобы получить все данные из диапазона A1:1 - То есть до конца таблицы, а потом просто вернуть длину получившегося массива.

### **Метод по записи данных**

    async function updateSheet(name, phone) // Записать в последнюю строку таблицы данные.
    {
        let lastRow = await getLastRow() + 1;
        const opt = {
                spreadsheetId : config.spreadid,
                range: 'Data!A' + lastRow,
                valueInputOption:'USER_ENTERED',
                resource: {values: [[name, phone]]}
        }
        await gsapi.spreadsheets.values.update(opt);
    }

В параметры принимает имя и телефон (Их мы будем хранить в таблице). Также словарь _opt_ теперь содержит дополнительные параметры в виде наших данных. Обратите внимание, что _values_ - это массив массивов. Так, мы можем передавать диапазон данных, а не только одну строку. Для записи используется метод _update_.

<div class="row">

<div class="col-lg-6">

На этом наша работа с GoogleApi закончена, осталось лишь экспортировать методы по работе с Api, чтобы мы могли их вызывать из другого класса.

</div>

<div class="col-lg-6">

    module.exports.updateSheet = updateSheet;
    module.exports.getValues = getValues;
    module.exports.getLastRow = getLastRow;

</div>

</div>

</div>

<div class="anchor-box" id="waapi">

## Работа с WhatsApp API

<div class="is-relative">

<div class="note is-absolute">

<div class="note__title">Внимание!</div>

<div class="note__text">Чтобы бот работал, телефон должен быть всегда подключен к интернету и не должен использоваться Whatsapp Web.</div>

</div>

</div>

<div class="d-flex flex-column flex-lg-row align-items-start mb-25">

<div class="pr-30">[![Авторизация Whatsapp через QR код](/img/whatsapp_auth_ru.gif)](/img/whatsapp_auth_ru.gif) </div>

<div class="">

В самом начале, сразу свяжем whatsapp с нашим скриптом, чтобы по мере написания кода - проверять его работу. Для этого переходим [в личный кабинет](https://app.chat-api.com) и получаем там QR-код. Далее открываем WhatsApp на мобильном телефоне, заходим в Настройки -> WhatsApp Web -> Сканируем QR-код.

<div>[<span>Получить доступ к WhatsApp API</span>](https://app.chat-api.com)</div>

</div>

</div>

Для работы с Whatsapp API вам потребуется _token_ и _Uri_ из вашего [личного кабинета](https://app.chat-api.com). Вы найдете их в "шапке" вашего инстанса.

Запишем их в конфигурационный файл:

    module.exports = {
        apiUrl: "https://eu115.chat-api.com/instance12345/", // URL адрес для обращений к API
        token: "1hi0xwfzaxsews12345", // Токен для работы с API из личного кабинета
        spreadid:"1M6fyEhv64ug7zILRz86H1PBKEKHUgKV9pWSW2m_r4SI",  // ID Google таблицы
    }

После чего создадим файл **index.js**. Он будет содержать всю логику работы бота и сервер по обработке запросов от Weebhook. Импортируем все зависимости.

    const config = require("./config.js");
    const Googleapi = require("./Googleapi.js");
    const token = config.token, apiUrl = config.apiUrl;
    const menu_text = config.menuText;
    const app = require('express')();
    const bodyParser = require('body-parser');
    const fetch = require('node-fetch');

*   _node-fetch_ позволит совершать запросы к API, config подгрузит наши данные с другого файла;
*   _token_ и _apiUrl_ наши данные из конфигурационного файла, которые позволяют обращаться к WA Api;
*   Модуль _Express_ нужен для развертывания веб-сервера, который будет обрабатывать запросы;
*   _body-parser_ позволит удобно извлекать поток входящих запросов;
*   _Googleapi_ - наш модуль по работе с GoogleApi;

<div class="row">

<div class="col-lg-6">

Далее говорим серверу, что будем парсить _Json_ данные:

</div>

<div class="col-lg-6">

    app.use(bodyParser.json());

</div>

</div>

<div class="row">

<div class="col-lg-6">

Вешаем обработчик ошибок:

</div>

<div class="col-lg-6">

    process.on('unhandledRejection', err => {
        console.log(err)
    });

</div>

</div>

И описываем функцию, которая будет работать с Whatsapp Api.

    async function apiChatApi(method, params){
        const options = {};
        options['method'] = "POST";
        options['body'] = JSON.stringify(params);
        options['headers'] = { 'Content-Type': 'application/json' };

        const url = `${apiUrl}/${method}?token=${token}`;

        const apiResponse = await fetch(url, options);
        const jsonResponse = await apiResponse.json();
        return jsonResponse;
    }

В параметры данная функция принимает метод, который необходимо выполнить и объект с параметрами, которые мы будем передавать в запросе. Внутри функции создаём объект _options_, который сразу пополняем двумя ключами: _json_ и _method_. В первом мы передаём параметры, необходимые для API, а во втором указываем метод, с котором обращаемся и в котором хотим получить ответ. Далее мы объявляем константу – наш url адрес для обращения к API. Он будет содержать в себе, собственно, сам url (из конфига), метод и токен. После этого посылаем запрос к Chat-Api.

Теперь, когда у нас функция готова, можем описать основную логику работы бота. Опишем обработчик, на который будут приходить данные от _webhook_.

    app.post('/', async function (req, res) {
        const data = req.body;
    }

Данная функция - это и есть обработчик, который обрабатывает POST запросы по основному адресу сервера (За это отвечает '/' - путь). **data** - это пришедший json файл.

Для того, чтобы узнать, какой именно JSON нам будет приходить на сервер. Воспользуемся инструментами [тестирования](https://app.chat-api.com/testing)

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-14.png) <span class="image-caption">Тестирование запросов - Симуляция вебхука</span>](https://chat-api.com/img/googlesheets/google-api-sheet-14.png) </div>

    app.post('/', async function (req, res) {
        const data = req.body;
        for (var i in data.messages) {
            const body = String(data.messages[i].body.toLowerCase());
            const chatId = data.messages[i].chatId;
            splitBody = body.split(' ');
            command = splitBody[0];

            if(data.messages[i].fromMe)
                return;

            if(command == 'помощь')
            {
                await apiChatApi('sendMessage', {chatId:chatId, body: menu_text});
            }
            else if (command == 'запись')
            {
                name = splitBody[1];
                phone = splitBody[2];
                await Googleapi.updateSheet(name, phone)
                await apiChatApi('sendMessage', {chatId:chatId, body: 'Успешно записано'})
            }

            else if (command == 'инфо')
            {
                let result;
                if (splitBody.length == 1){
                    result = await getInfoDataFromSheet('A2:D2');
                }
                else{
                    result = await getInfoDataFromSheet(splitBody[1]);
                }
                x = await apiChatApi('sendMessage', {chatId:chatId, body: result})
                console.log(x);
            }

            else if (command == 'файл')
            {
                linkFile = (await Googleapi.getValues('Data!D2'))[0][0];
                x = await apiChatApi('sendFile', {chatId:chatId, body: linkFile, 'filename':'testfile'})
            }

            else if (command == 'рассылка'){
                lastRow = await Googleapi.getLastRow() + 1;
                dataAll = await Googleapi.getValues('Data!A2:D' + lastRow);
                dataAll.forEach(async function(entry){
                    await apiChatApi('sendMessage', {phone:entry[1], body: `Привет, ${entry[0]}, это тестовая рассылка.`});
                });
            }

            else
            {
                await apiChatApi('sendMessage', {chatId:chatId, body: menu_text})
            }
        }
        res.send('Ok');
    });

В данном обработчике мы, в зависимости от присылаемой команды, выполняем нужные действия. Разберем каждое по отдельности и сразу покажем результат тестирования.

#### Заполнение ячейки

Для записи данных в таблицу мы берем сообщение, разбиваем его с помощью метода _split_ и передаем имя и телефон в функцию, которую мы написали для работы с Google API.

     else if (command == 'запись'){
        name = splitBody[1];
        phone = splitBody[2];
        await Googleapi.updateSheet(name, phone)
        await apiChatApi('sendMessage', {chatid:chatId, body: 'Успешно записано'})
    }

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-19.png) <span class="image-caption">Тестируем запись в ячейку</span>](https://chat-api.com/img/googlesheets/google-api-sheet-19.png) </div>

#### Прочитать из ячейки, получить информацию

Для получения данных мы либо передаем входящий диапазон данных из сообщения, либо если пользователь не отправил диапазон, то отправляем стандартный A2:D2

     else if (command == 'инфо'){
        let result;
        if (splitBody.length == 1){
            result = await getInfoDataFromSheet('A2:D2');
        }
        else{
            result = await getInfoDataFromSheet(splitBody[1]);
        }
        await apiChatApi('sendMessage', {chatId:chatId, body: result})
    }

Функция _GetInfoDataFromSheet_ просто формирует строку из массивов данных, которые вернул нам GoogleApi.

    async function getInfoDataFromSheet(range){
        data = await Googleapi.getValues('Data!' + range);
        result = "";
        data.forEach(function(entry) {
            result += entry.join(' ') + "\n"
        });
        return result;
    }

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-21.png) <span class="image-caption">Тестируем чтение из ячейки</span>](https://chat-api.com/img/googlesheets/google-api-sheet-21.png) </div>

#### Отправить файл в WhatsApp

Для отправки файла мы берем прямую ссылку на файл из ячейки таблицы и отправляем с помощью метода _sendFile_.

     else if (command == 'файл'){
        linkFile = (await Googleapi.getValues('Data!D2'))[0][0];
        x = await apiChatApi('sendFile', {chatId:chatId, body: linkFile, 'filename':'testfile'})
    }

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-23.png) <span class="image-caption">Отправка файла Whatsapp из гугл ячейки</span>](https://chat-api.com/img/googlesheets/google-api-sheet-23.png) </div>

<div class="is-relative">

#### Рассылка в WhatsApp

Для рассылки мы просто проходимся по всей таблице и отправляем сообщения на указанные номера. Для тестирования рассылки в таблицу добавили наш номер в две строки.

<div class="note is-absolute">

<div class="note__title">Внимание!</div>

<div class="note__text">Мы призываем наших клиентов не отправлять нежелательные сообщения, делать массовые маркетинговые рассылки. Иначе ваш аккаунт может быть заблокирован антиспам-системой WhatsApp! Свяжитесь с нашей тех.поддержкой для уточнения рекомендаций.</div>

</div>

</div>

    else if (command == 'рассылка'){
        lastRow = await Googleapi.getLastRow() + 1;
        dataAll = await Googleapi.getValues('Data!A2:D' + lastRow);
        dataAll.forEach(async function(entry){
            await apiChatApi('sendMessage', {phone:entry[1], body: `Привет, ${entry[0]}, это тестовая рассылка.`});
        });
    }

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-22.png) <span class="image-caption">Массовая рассылка в Whatsapp по списку номеров</span>](https://chat-api.com/img/googlesheets/google-api-sheet-22.png) </div>

Все тесты закончены удачно. Теперь мы можем загрузить нашего бота на сервер и установить Webhook.

</div>

<div class="anchor-box" id="weebhook">

## Weebhook

Webhook решает проблему с задержкой на отклик входящих сообщений. Без него, нашему боту пришлось бы постоянно спрашивать у сервера о входящих данных, делать периодические фиксированные во времени запросы к серверам. Тем самым имея некоторую задержку в отклике, а также это способствовало бы нагрузке на сервер.

Но, если мы укажем адрес Webhook сервера, то данная необходимость перестанет быть актуальной. Сервера сами будут присылать уведомления о входящих изменениях, как только они появятся. А задача Webhook сервера их принять и правильно обработать, реализовывая логику бота. Указывать можно как домен, так и ip адрес.

Поэтому сейчас нам необходимо загрузить бота на хостинг (выделенный сервер) и запустить его. Когда мы это сделаем, то укажем домен или IP адрес в личном кабинете в качестве вебхука и протестируем работу бота.

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-16.png) <span class="image-caption">Устанавливаем вебхук в личном кабинете</span>](https://chat-api.com/img/googlesheets/google-api-sheet-16.png) </div>

При запуске должно появляться сообщение с успешным подключением:

<div class="mb-30">[![](https://chat-api.com/img/googlesheets/google-api-sheet-17.png) <span class="image-caption">Сообщение об успешном подключении</span>](https://chat-api.com/img/googlesheets/google-api-sheet-17.png) </div>

</div>

<div class="anchor-box" id="final">

## Whatsapp бот в связке с Google Sheets готов

Итак, мы описали работу простого ватсап чатбота и выложили исходник с готовым функционалом на github.

Вам необходимо только подставить в коде свой токен из личного кабинета и номер инстанса.

Теперь необходимо загрузить наш сервер вместе с ботом на хостинг и в качестве webhook указать ваш домен. При каждом входящем сообщении на сервер будут приходить и обрабатываться данные. Если у вас возникнут какие-либо вопросы, вы всегда можете обратиться в нашу техническую поддержку!


</div>