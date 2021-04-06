# Автонажатие кнопок под сообщениями в TG-каналах

## Задача

Telegram позволяет размещать кнопки под сообщениями в группах и каналах. Пример на картинке:

![&#x41A;&#x43D;&#x43E;&#x43F;&#x43A;&#x438; &#x43F;&#x43E;&#x434; &#x441;&#x43E;&#x43E;&#x431;&#x449;&#x435;&#x43D;&#x438;&#x435;&#x43C; &#x432; &#x43A;&#x430;&#x43D;&#x430;&#x43B;&#x435; Pantini Fly](../.gitbook/assets/image%20%2898%29.png)

Полезно уметь нажимать эти кнопки программно без ручных действий пользователя. 

{% hint style="danger" %}
В конце раздела есть пример приёма сигналов без Telegram. Все кликеры предоставлены в академических образовательных целях
{% endhint %}

## Подготовка рабочего места <a id="getting-started"></a>

{% hint style="info" %}
Если вы пропустили раздел по начальной подготовке рабочей среды, то самое время с ним ознакомиться по [ссылке](../wastelands/getting-started.md).
{% endhint %}

Для решения задачи нам понадобится установить **Python** и ряд модулей для него.

#### Отключение псевдонима Python в Windows

В **Windows 10** попытка запустить не установленный **Python** из командной строки \(откройте **Power Shell**, введите команду **python** и нажмите **Enter**\) может автоматически открыть магазин приложений с предложением установки:

![](../.gitbook/assets/image%20%2899%29.png)

Такой вариант не слишком удобен, поэтому отключим это поведение в _управлении псевдонимами выполнения приложения_ \(введите выделенную фразу в поиске **Windows**\):

![](../.gitbook/assets/image%20%28107%29.png)

Отключите флажки:

![&#x41E;&#x442;&#x43A;&#x43B;&#x44E;&#x447;&#x430;&#x435;&#x43C; &#x43F;&#x441;&#x435;&#x432;&#x434;&#x43E;&#x43D;&#x438;&#x43C;&#x44B;](../.gitbook/assets/image%20%28101%29.png)

#### Установка Python <a id="installing-python"></a>

Переходим на сайт [https://www.python.org/downloads/](https://www.python.org/downloads/) ****и нажимаем жёлтую кнопку в верхней части страницы:

![](../.gitbook/assets/image%20%28103%29.png)

На момент написания этого раздела актуальная версия **3.9.2**

Запускаем загруженный установщик, ставим все флажки, нажимаем **Install Now:**

![&#x415;&#x441;&#x43B;&#x438; &#x43D;&#x430;&#x436;&#x430;&#x442;&#x44C; Customize installation, &#x442;&#x43E; &#x442;&#x430;&#x43C; &#x434;&#x43E;&#x43B;&#x436;&#x43D;&#x44B; &#x431;&#x44B;&#x442;&#x44C; &#x442;&#x43E;&#x436;&#x435; &#x432;&#x44B;&#x431;&#x440;&#x430;&#x43D;&#x44B; &#x432;&#x441;&#x435; &#x444;&#x43B;&#x430;&#x433;&#x438;](../.gitbook/assets/image%20%28106%29.png)

Дожидаемся окончания установки, запускаем **Cmder**, проверяем установку:

![&#x427;&#x442;&#x43E;&#x431;&#x44B; &#x432;&#x44B;&#x439;&#x442;&#x438; &#x438;&#x437; &#x440;&#x435;&#x436;&#x438;&#x43C;&#x430; &#x432;&#x432;&#x43E;&#x434;&#x430;, &#x432;&#x432;&#x435;&#x434;&#x438;&#x442;&#x435; quit\(\) &#x438; &#x43D;&#x430;&#x436;&#x43C;&#x438;&#x442;&#x435; Enter](../.gitbook/assets/image%20%28100%29.png)

#### Установка модулей для взаимодействия с Telegram

Для работы с TG будет использоваться широко известный модуль Telethon:

{% embed url="https://docs.telethon.dev/en/latest/" %}

Вводим команду и нажимаем **Enter**:

```bash
$ pip3 install -U telethon --user
```

![](../.gitbook/assets/image%20%28102%29.png)

{% hint style="info" %}
**pip3** - это установщик пакетов для **Python**.
{% endhint %}

## Получение API-ключей Telegram

Мы собираемся создать Telegram-клиент, который бы мог отслеживать новые сообщения и нажимать на кнопки. Для выполнения подобной задачи необходимо сначала получить ключи доступа на сайте **Telegram** и авторизоваться в **Telethon**.

{% hint style="info" %}
Процесс авторизации подробно описан в документации:

[https://docs.telethon.dev/en/latest/basic/signing-in.html](https://docs.telethon.dev/en/latest/basic/signing-in.html)
{% endhint %}

#### Авторизация по телефону

Вам понадобится действующий Telegram-аккаунт. Переходите по ссылке:

{% embed url="https://my.telegram.org/auth" %}

После авторизации переходите по ссылке [API Development tools](https://my.telegram.org/apps). В появившейся форме _Create new application_ заполняйте поля **App title** \(название приложения\), **Short name** \(короткое название\)**.**

**URL** можно не заполнять. ****Платформу можно выбрать **Desktop.**

Далее нажимайте **Create application.** В результате вы получите два ключа, которые называются **App api\_id** __и __**App api\_hash**. Эти данные следует держать в секрете.

## Написание кода

{% hint style="info" %}
В примере для данного раздела будет показано, как нажимать цветные кнопки автоматически для сообщений из канала [Pantini Fly](../faq/telegram-resources/fly.md).
{% endhint %}

На этом этапе у вас должна быть настроена рабочая среда. Код будет опубликован в репозитории [jpx](https://github.com/johnpantini/jpx). Если вы работаете [по 3-му варианту](../wastelands/creating-a-repo.md#mode-3-local-clone), просто обновите проект через Ctrl+T и примите изменения в ветке **johnpantini**.

В папке проекта создам новую директорию **python**, а в ней файл **telegram-clicker.py**, сразу же открою терминал нажатием соответствующей вкладки внизу окна, проверю работу:

![&#x41F;&#x440;&#x43E;&#x432;&#x435;&#x440;&#x438;&#x43C;, &#x447;&#x442;&#x43E; &#x432;&#x441;&#x435; &#x440;&#x430;&#x431;&#x43E;&#x442;&#x430;&#x435;&#x442;, &#x43A;&#x430;&#x43A; &#x43F;&#x43E;&#x43B;&#x43E;&#x436;&#x435;&#x43D;&#x43E;](../.gitbook/assets/image%20%28104%29.png)

Запуск скрипта осуществляется командой:

```javascript
# Команду нужно выполнять, находясь в подпапке python
# cd python\

$ python ./telegram-clicker.py
```

Telegram-ключи для подключения, полученные в предыдущем разделе, будем передавать через аргументы командной строки в следующем виде:

```javascript
# Вставьте свои реальные данные, полученные ранее, перед запуском
# Символы < и > обозначают заполнитель, их вводить не нужно
# Пример:
# python ./telegram-clicker.py 12345 0123456789abcdef0123456789abcdef

$ python ./telegram-clicker.py <api_id> <api_hash>
```

Новый вариант кода запишет переданные аргументы в соответствующие переменные, далее выведет содержимое на экран:

```python
import sys

# Работаем, только если было передано 2 или более аргумента
if len(sys.argv) >= 2:
  api_id = sys.argv[1]
  api_hash = sys.argv[2]

  print(api_id)
  print(api_hash)
  
```

![](../.gitbook/assets/image%20%28105%29.png)

Теперь, когда у нас есть способ передать программе конфиденциальные данные \(которые не следует хранить в репозитории\), можно написать код, реагирующий на новые сообщения в канале [Pantini Fly](../faq/telegram-resources/fly.md).

{% hint style="danger" %}
Имя файла заменяю на **pantini-fly-clicker.py**
{% endhint %}

Мы будем [прослушивать ](https://docs.telethon.dev/en/latest/basic/updates.html)событие [NewMessage](https://docs.telethon.dev/en/latest/modules/events.html#telethon.events.newmessage.NewMessage), смотреть на количество кнопок, нажимать **вторую** кнопку в **первом** ряду \(она красного цвета\).

Также мы будем хранить номер последнего принятого сообщения, чтобы не нажимать кнопку более одного раза \(при запущенном параллельно Telegram это может произойти\).

{% code title="python/pantini-fly-clicker.py" %}
```python
import sys
from telethon import TelegramClient, events

last_message_id = 0

if len(sys.argv) >= 2:
  api_id = sys.argv[1]
  api_hash = sys.argv[2]

  client = TelegramClient('pantini', api_id, api_hash)

  @client.on(events.NewMessage("https://t.me/pantini_fly"))
  async def new_message_event_handler(event):
    global last_message_id

    if event.message.buttons is not None and event.message.id > last_message_id:
      last_message_id = event.message.id
      await event.message.click(0, 1)

  client.start()
  client.run_until_disconnected()

```
{% endcode %}

```javascript
# Вставьте свои реальные данные, полученные ранее, перед запуском
# Символы < и > обозначают заполнитель, их вводить не нужно
# Пример:
# python ./pantini-fly.py 12345 0123456789abcdef0123456789abcdef

$ python ./pantini-fly.py <api_id> <api_hash>
```

{% hint style="info" %}
В методе **click** аргументы 0 и 1 означают координаты кнопки по горизонтали и вертикали соответственно. Нумерация начинается с нуля.
{% endhint %}

{% hint style="info" %}
Запущенный скрипт будет работать без дополнительных вмешательств, пока не будет остановлен внешним воздействием. Чтобы прервать работу запущенного скрипта вручную, нажмите **Ctrl+C**.
{% endhint %}

{% hint style="danger" %}
При первом запуске вас могут единоразово попросить ввести телефон и код подтверждения \(придёт в Telegram\) и пароль \(если есть\), чтобы проинициализировать активную Telegram-сессию. Файл сессии сохранится на диске в папке скрипта. **Этот файл не следует добавлять в репо, но стоит добавить в** `.gitignore`
{% endhint %}

## Автоматическое нажатие сигнальных кнопок в PPF

В коде нужно лишь поменять ссылку на канал, а также указать другие координаты кнопки при необходимости. Следующий код выполнит нажатие на кнопку под виджетом:

{% code title="python/ppf-clicker.py" %}
```python
import sys
from telethon import TelegramClient, events

last_message_id = 0

if len(sys.argv) >= 2:
  api_id = sys.argv[1]
  api_hash = sys.argv[2]

  client = TelegramClient('pantini', api_id, api_hash)

  @client.on(events.NewMessage("https://t.me/ppf_official"))
  async def new_message_event_handler(event):
    global last_message_id

    if event.message.buttons is not None and event.message.id > last_message_id:
      last_message_id = event.message.id
      await event.message.click(0, 0)

  client.start()
  client.run_until_disconnected()

```
{% endcode %}

Информация о виджетах и кнопках **PPF** будет опубликована в [соответствующем разделе](../faq/telegram-resources/primitive-future.md).

## Автоматическое выставление заявок по виджетам PPF

{% hint style="info" %}
Для тестирования виджетов присоединитесь к каналу **PPF Staging**:

[https://t.me/joinchat/HZLgHnK8TQAyZWQy](https://t.me/joinchat/HZLgHnK8TQAyZWQy)

В канале цены виджетов искусственно занижены для безопасности экспериментов.
{% endhint %}

Для нажатия кнопки **Отправить в Onaryx** в [PPF Staging ](https://t.me/joinchat/HZLgHnK8TQAyZWQy)код кликера следующий:

{% code title="python/ppf-staging-clicker.py" %}
```python
import sys
from telethon import TelegramClient, events

last_message_id = 0

if len(sys.argv) >= 2:
  api_id = sys.argv[1]
  api_hash = sys.argv[2]

  client = TelegramClient('pantini', api_id, api_hash)

  @client.on(events.NewMessage("https://t.me/joinchat/HZLgHnK8TQAyZWQy"))
  async def new_message_event_handler(event):
    global last_message_id

    if event.message.buttons is not None and event.message.id > last_message_id:
      last_message_id = event.message.id
      await event.message.click(0, 0)

  client.start()
  client.run_until_disconnected()
  
```
{% endcode %}

![&#x41A;&#x43E;&#x43E;&#x440;&#x434;&#x438;&#x43D;&#x430;&#x442;&#x44B; &#x43A;&#x43D;&#x43E;&#x43F;&#x43E;&#x43A; PPF](../.gitbook/assets/image%20%28212%29.png)

Для нажатия, например, красной кнопки 🔴 координаты нужно изменить на **\(1, 1\)**. Запуск скрипта аналогичен прошлым, меняется лишь имя файла:

```python
$ python ./ppf-staging-clicker.py <api_id> <api_hash>
```

### Подписка и обработка событий от Onaryx через Python-кликер

{% hint style="info" %}
Onaryx - защищенный через ключ и Telegram-id WebSocket-канал, который отслеживает нажатия кнопок в виджетах моей экосистемы.
{% endhint %}

Для начала приготовьте ваш ключ и Telegram-id. Их можно получить у [@pantini\_warden\_bot](https://t.me/pantini_warden_bot) в Telegram. Отправьте боту команду **/token**, если ранее этого не делали.

{% hint style="danger" %}
На этом этапе посетите раздел про [установку N\|Solid](../caves/setting-up-nsolid.md), где посмотрите, как в проект добавить файл **package.json**. Сейчас он нам понадобится!
{% endhint %}

Откройте cmder внутри WebStorm и введите команду:

```python
$ npm i socket.io-client@latest
```

Эта команда установит последнюю версию клиентской библиотеки [socket.io](https://socket.io/), которая понадобится для подключения к Onaryx.

{% hint style="info" %}
Если у вас появился файл **package-lock.json**, выделенный коричневым цветом \(вне git\), то добавьте его через сочетание Ctrl+Alt+A \(предварительно выделив\). Цвет должен измениться на зелёный.
{% endhint %}

Также появится папка **node\_modules**, но она будет проигнорирована git. Ничего дополнительно делать не нужно.

Зайдите в настройки WebStorm, поставьте отступы в 2 пробела на вкладке **Tabs and Indents**:

![](../.gitbook/assets/image%20%28201%29.png)

На вкладке **Punctuation** выставите использование одиночных кавычек для строковых литералов:

![](../.gitbook/assets/image%20%28203%29.png)

На вкладке Wrapping and Braces установите настройки **Align** в разделе **Objects** и **Variable declarations**:

![](../.gitbook/assets/image%20%28197%29.png)

Теперь нажатие сочетания Ctrl+Alt+L будет автоматически форматировать код JavaScript в редакторе:

![&#x41C;&#x435;&#x43D;&#x44E; Code &#x432; WebStorm](../.gitbook/assets/image%20%28191%29.png)

Создайте файл **onaryx.mjs** с содержимым:

{% code title="onaryx.mjs" %}
```javascript
import {io} from 'socket.io-client';

if (process.env.TG_ID && process.env.PANTINI_TOKEN) {
  const client = io('wss://onaryx.ru', {
    query: {
      id:    process.env.TG_ID,
      token: process.env.PANTINI_TOKEN
    }
  });

  client.on('ticker', data => {
    if (data.m === 'ppf-staging-tg') {
      console.log(data);
    }
  });
}

```
{% endcode %}

Сейчас мы будет запускать скрипт через терминал WebStorm без Cmder. Для этого создадим новую конфигурацию:

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; Add Configuration...](../.gitbook/assets/image%20%28189%29.png)

![&#x41D;&#x430;&#x436;&#x43C;&#x438;&#x442;&#x435; &#x43D;&#x430; + &#x432; &#x43F;&#x430;&#x43D;&#x435;&#x43B;&#x438; &#x437;&#x43D;&#x430;&#x447;&#x43A;&#x43E;&#x432;, &#x432;&#x44B;&#x431;&#x435;&#x440;&#x438;&#x442;&#x435; Node.js](../.gitbook/assets/image%20%28198%29.png)

![](../.gitbook/assets/image%20%28214%29.png)

![&#x42D;&#x442;&#x438; &#x434;&#x430;&#x43D;&#x43D;&#x44B;&#x435; &#x43C;&#x44B; &#x43F;&#x43E;&#x43B;&#x443;&#x447;&#x430;&#x43B;&#x438; &#x443; @pantini\_warden\_bot](../.gitbook/assets/image%20%28188%29.png)

Добавьте две переменные окружения, нажав на +, вводя свои реальные данные, полученные ранее.

Теперь запускайте новую конфигурацию, нажав на значок **Run**:

![](../.gitbook/assets/image%20%28215%29.png)

Сейчас, если у вас запущен и python-кликер, вы будете получать данные о сигнале в node.js:

![](../.gitbook/assets/image%20%28216%29.png)

**Описание полей:**

**m** означает **message**, содержит тип сообщения. Для сообщений, принятых от нажатий кнопок под сообщениями в Telegram, добавляется постфикс `-tg`.

**p** означает цену

**v** - объём

**t** - тикер

**d** - направление. **b** значит покупка, **s** - продажа

### Запуск в RHEL через [N\|Solid](../caves/setting-up-nsolid.md)

Скрипт **onaryx.mjs** можно запустить и изнутри виртуальной машины. Для этого нужно выполнить следующие команды:

```javascript
# cd /mnt/hgfs/jpx/
# TG_ID=12345678 PANTINI_TOKEN=JP-XXXXXX NSOLID_APPNAME=onaryx \ 
    NSOLID_COMMAND=localhost:9001 nsolid onaryx.mjs
```

Символ \ означает, что команда переносится на следующую строку.

### Подключение к Onaryx без Python-кликера и выставление заявок через торговый токен ТИ <a id="placing-orders-via-tinkoff-open-api"></a>

{% hint style="danger" %}
Onaryx передаёт сообщение подключенным по WebSocket клиентам в том числе и напрямую, минуя Telegram. При использовании этого способа приёма данных убедитесь, что все Python-кликеры **отключены**.
{% endhint %}

{% hint style="info" %}
Торговый токен для Tinkoff Open API можно получить по ссылке:

[https://www.tinkoff.ru/invest/settings/](https://www.tinkoff.ru/invest/settings/)

Его нужно хранить в секрете, не сохранять в репо.
{% endhint %}

Добавим опции:

* **allowSell** - если равен **true**, то скрипт будет следовать и за продажами. По умолчанию скрипт следует только за покупками.
* **whiteList** - массив тикеров, по которым будет проходить автоследование
* **blackList** - массив тикеров, по которым не будет проходить автоследование
* **channels** - каналы подписки

{% hint style="warning" %}
Тестовый канал имеет постфикс `-staging`

Канал передачи через кнопки под сообщениями в Telegram дополнительно имеет постфикс `-tg`
{% endhint %}

Инструмент будет поставлен на автоследование, если он находится в белом списке и не находится в чёрном.

{% hint style="info" %}
Onaryx выставляет объём \(поле `v`\) по умолчанию в 1 лот.
{% endhint %}

{% code title="onaryx.mjs" %}
```javascript
import {io} from 'socket.io-client';
import https from 'https';

const options = {  
  allowSell: false,
  whiteList: ['GTHX', 'SPCE'],
  blackList: ['AMZN', 'TSLA'],
  channels:  ['ppf-staging'],
};

const fetchTIOpenAPI = async ({path, body = '', method = 'GET'}) => {
  return new Promise(async (resolve, reject) => {
    const req = https.request({
      hostname: 'api-invest.tinkoff.ru',
      port:     443,
      path,
      method,
      headers:  {
        'Content-Type':   'application/json',
        'Content-Length': body.length,
        Authorization:    `Bearer ${process.env.TI_TOKEN}`
      }
    }, async res => {
      try {
        let responseText = '';

        for await (const chunk of res) {
          responseText += chunk;
        }

        resolve(JSON.parse(responseText || 'null'));
      } catch (error) {
        reject(error);
      }
    });

    if (body)
      req.write(body);

    req.end();
  });
};

if (process.env.TG_ID && process.env.PANTINI_TOKEN && process.env.TI_TOKEN) {
  try {
    const stocks = (await fetchTIOpenAPI({path: '/openapi/market/stocks'}))?.payload?.instruments;

    if (stocks) {
      const client = io('wss://onaryx.ru', {
        query: {
          id:    process.env.TG_ID,
          token: process.env.PANTINI_TOKEN
        }
      });

      client.on('error', (error) => {
        console.log(error);
      });

      client.on('ticker', async (data) => {
        if (options.channels.indexOf(data.m) > -1) {
          const {t, p, d, v} = data;

          if (options.whiteList.indexOf(t) < 0)
            return;

          if (options.blackList.indexOf(t) > -1)
            return;

          if (d === 'b' || options.allowSell) {
            const instrument = stocks.find(s => s.ticker === t);

            if (instrument?.figi) {
              const precision = instrument.minPriceIncrement.toString().split('.')[1]?.length || 0;

              const response = await fetchTIOpenAPI({
                path:   `/openapi/orders/limit-order?figi=${instrument.figi}`,
                method: 'POST',
                body:   JSON.stringify({
                  lots:      +v,
                  operation: d === 'b' ? 'Buy' : 'Sell',
                  price:     parseFloat((Math.round(parseFloat(p) / instrument.minPriceIncrement) *
                    instrument.minPriceIncrement).toFixed(precision))
                })
              });

              if (response?.status === 'Ok') {
                console.log(`${d === 'b' ? 'Buy' : 'Sell'} LMT ${t}@${p}`.toUpperCase());
              } else
                console.error(response);
            }
          }
        }
      });
    }
  } catch (error) {
    console.error(error);
  }
}


```
{% endcode %}

{% hint style="danger" %}
**Внимание**! Вы используете предложенный пример на свой страх и риск. Не забудьте сформировать белый \(строка 6\) и чёрный \(строка 7\) списки.
{% endhint %}

В коде используется [Оператор опциональной последовательности](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Optional_chaining). Учитывается и такой параметр, как минимальный шаг цены. Например, при приходе сигнала по тикеру $OBUV с ценою в 26,58 рублей будет выставлена заявка на 26,6 рублей. В противном случае была бы ошибка API.

Не забудьте добавить в конфигурацию новую переменную окружения **TI\_TOKEN**, куда поместите торговый токен Tinkoff Open API. Если будете запускать изнутри **RHEL**, то пример команды:

```javascript
# cd /mnt/hgfs/jpx/
# TG_ID=12345678 PANTINI_TOKEN=JP-XXXXXX NSOLID_APPNAME=onaryx \ 
    NSOLID_COMMAND=localhost:9001 TI_TOKEN='t.xxx' nsolid onaryx.mjs
```

### Просмотр истории изменений файлов

По ходу разработки мы будем дополнять разные файлы по нескольку раз, наращивая функциональность. Несмотря на то, что почти все этапы написания файлов задокументированы в книге, вы всегда можете вернуться к старым версиям через git. Для этого прямо в WebStorm есть вкладка Git, где все сохранения и изменения перечислены в хронологическом порядке. В правой части будет список файлов, затронутых выбранным изменением - там можно смотреть, что было ранее:

![](../.gitbook/assets/image%20%28210%29.png)

