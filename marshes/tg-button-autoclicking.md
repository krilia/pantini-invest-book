# Автонажатие кнопок под сообщениями в TG-каналах

## Задача

Telegram позволяет размещать кнопки под сообщениями в группах и каналах. Пример на картинке:

![&#x41A;&#x43D;&#x43E;&#x43F;&#x43A;&#x438; &#x43F;&#x43E;&#x434; &#x441;&#x43E;&#x43E;&#x431;&#x449;&#x435;&#x43D;&#x438;&#x435;&#x43C; &#x432; &#x43A;&#x430;&#x43D;&#x430;&#x43B;&#x435; Pantini Fly](../.gitbook/assets/image%20%2898%29.png)

Полезно уметь нажимать эти кнопки программно без ручных действий пользователя. 

## Подготовка рабочего места

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

#### Установка Python

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
В примере для данного раздела будет показано, как нажимать цветные кнопки автоматически для сообщений из канала [Pantini Fly](../faq/telegram-resources/fly.md)
{% endhint %}

На этом этапе у вас должна быть настроена рабочая среда. Код будет опубликован в репозитории [jpx](https://github.com/johnpantini/jpx). Если вы работаете [по 3-му варианту](../wastelands/creating-a-repo.md#mode-3-local-clone), просто обновите проект через Ctrl+T и примите изменения в ветке **johnpantini**.

В папке проекта создам новую директорию **python**, а в ней файл **telegram-clicker.py**, сразу же открою терминал нажатием соответствующей вкладки внизу окна, проверю работу:

![&#x41F;&#x440;&#x43E;&#x432;&#x435;&#x440;&#x438;&#x43C;, &#x447;&#x442;&#x43E; &#x432;&#x441;&#x435; &#x440;&#x430;&#x431;&#x43E;&#x442;&#x430;&#x435;&#x442;, &#x43A;&#x430;&#x43A; &#x43F;&#x43E;&#x43B;&#x43E;&#x436;&#x435;&#x43D;&#x43E;](../.gitbook/assets/image%20%28104%29.png)

Запуск скрипта осуществляется командой:

```bash
# Команду нужно выполнять, находясь в подпапке python
# cd python\

$ python ./telegram-clicker.py
```

Telegram-ключи для подключения, полученные в предыдущем разделе, будем передавать через аргументы командной строки в следующем виде:

```bash
# Вставьте свои реальные данные, полученные ранее, перед запуском
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

Мы будем [прослушивать ](https://docs.telethon.dev/en/latest/basic/updates.html)событие [NewMessage](https://docs.telethon.dev/en/latest/modules/events.html#telethon.events.newmessage.NewMessage), смотреть на количество кнопок, нажимать **вторую** кнопку в **первом** ряду \(она красного цвета\).

```python
import sys
from telethon import TelegramClient, events

if len(sys.argv) >= 2:
  api_id = sys.argv[1]
  api_hash = sys.argv[2]

  client = TelegramClient('pantini', api_id, api_hash)

  @client.on(events.NewMessage("https://t.me/pantini_fly"))
  async def new_message_event_handler(event):
    if event.message.buttons is not None:
      await event.message.click(0, 1)

  client.start()
  client.run_until_disconnected()

```

{% hint style="info" %}
В методе **click** аргументы 0 и 1 означают координаты кнопки по горизонтали и вертикали соответственно. Нумерация начинается с нуля.
{% endhint %}

{% hint style="info" %}
Запущенный скрипт будет работать без дополнительных вмешательств, пока не будет остановлен внешним воздействием. Чтобы прервать работу запущенного скрипта вручную, нажмите **Ctrl+C**
{% endhint %}

{% hint style="danger" %}
При первом запуске вас могут единоразово попросить ввести телефон и код подтверждения \(придёт в Telegram\) и пароль \(если есть\), чтобы проинициализировать активную Telegram-сессию. Файл сессии сохранится на диске в папке скрипта. **Этот файл не следует добавлять в репо, но стоит добавить в** `.gitignore`
{% endhint %}

## Автоматическое нажатие сигнальных кнопок в PPF

В коде нужно лишь поменять ссылку на канал, а также указать другие координаты кнопки при необходимости. Следующий код выполнит нажатие на пользовательскую кнопку ⭐️:

```python
import sys
from telethon import TelegramClient, events

if len(sys.argv) >= 2:
  api_id = sys.argv[1]
  api_hash = sys.argv[2]

  client = TelegramClient('pantini', api_id, api_hash)

  @client.on(events.NewMessage("https://t.me/ppf_official"))
  async def new_message_event_handler(event):
    if event.message.buttons is not None:
      await event.message.click(0, 1)

  client.start()
  client.run_until_disconnected()

```

Информация о виджетах и кнопках **PPF** будет опубликована в [соответствующем разделе](../faq/telegram-resources/primitive-future.md).

