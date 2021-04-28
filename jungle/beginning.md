# 🖍 Терминал: начало работы

{% hint style="info" %}
Сейчас раздел подготовлен только для ОС Windows.
{% endhint %}

### Windows

Для запуска терминала понадобится сделать следующее:

* Установить оболочку [Cmder](../wastelands/getting-started.md#cmder-installation)
* Разобраться с [репозиторием проекта](../wastelands/creating-a-repo.md)
* Обзавестись [IDE](../wastelands/getting-started.md#setting-up-ide), т.е. средой разработки

Код терминала и вспомогательного ПО, потребного для запуска, будет располагаться в репозитории в директории **terminal**.

#### Веб-сервер

В качестве сервера будет использоваться бета-версия [nginx для Windows](https://nginx.org/ru/docs/windows.html). Дистрибутив распространяется в уже скомпилированном виде, я включил последнюю версию \(распаковав архив [отсюда](https://nginx.org/ru/download.html)\) в репозиторий: сборка находится в поддиректории **nginx-win**. 

Для запуска терминала мы будем использовать **nginx**, настроенный на работу без дополнительных рабочих процессов. Будет только один главный процесс, задача которого - передавать файлы, когда их будет запрашивать браузер.

В поддиректорию **nginx-win/conf** помещаем файл **mime.types**, взятый из оригинального архива, и файл **nginx.conf**, где описываются все настройки обработки запросов \(большинство по умолчанию\). Простейший вариант для проверки работоспособности выглядит так:

{% code title="terminal/nginx-win/conf/nginx.conf" %}
```coffeescript
master_process off;
daemon off;
error_log stderr;

events {
    worker_connections 1024;
}

http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;

    server {
        listen 8080;
        server_name localhost;

        location / {
            add_header 'Content-Type' 'text/plain';
            return 200 'OK';
        }
    }
}
```
{% endcode %}

Для того, чтобы nginx запустился, необходимо наличие двух поддиректорий **temp** и **logs** в той же директории с исполнимым файлом **nginx.exe**. Можно автоматически создавать эти поддиректории в конфигурации для запуска WebStorm через скрипт оболочки. Настроим конфигурацию:

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; Add Configuration...](../.gitbook/assets/image%20%28189%29.png)

![&#x421;&#x43E;&#x437;&#x434;&#x430;&#x43D;&#x438;&#x435; &#x43A;&#x43E;&#x43D;&#x444;&#x438;&#x433;&#x443;&#x440;&#x430;&#x446;&#x438;&#x438; &#x434;&#x43B;&#x44F; &#x437;&#x430;&#x43F;&#x443;&#x441;&#x43A;&#x430; nginx &#x447;&#x435;&#x440;&#x435;&#x437; Batch](../.gitbook/assets/image%20%28222%29.png)

Для начала укажем путь к EXE и рабочую директорию:

![](../.gitbook/assets/image%20%28230%29.png)

В разделе **Before launch** выполним запуск команд по созданию поддиректорий **logs** и **temp**:

![&#x412;&#x44B;&#x431;&#x438;&#x440;&#x430;&#x435;&#x43C; Run External tool &#x438; &#x43D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x43D;&#x430; +](../.gitbook/assets/image%20%28233%29.png)

![](../.gitbook/assets/image%20%28224%29.png)

Имя произвольное, например, `mkdir temp&logs`

Программа для запуска - `powershell.exe`

Аргументы:

```bash
If(!(Test-Path logs)){New-Item -ItemType Directory -Force -Path logs};If(!(Test-Path temp)){New-Item -ItemType Directory -Force -Path temp}
```

Рабочая директория: `$FileParentDir$`

Флаг **Open console for tool output** можно снять.

Теперь можно запускать конфигурацию:

![nginx &#x437;&#x430;&#x43F;&#x443;&#x449;&#x435;&#x43D;, &#x432;&#x441;&#x451; &#x432; &#x43F;&#x43E;&#x440;&#x44F;&#x434;&#x43A;&#x435;](../.gitbook/assets/image%20%28232%29.png)

Если все сделано верно, сервер будет отвечать текстом **OK**:

![](../.gitbook/assets/image%20%28226%29.png)

#### Делаем соединение защищённым

Ряд функций веб-платформы работает только в том случае, если установлено защищённое соединение между браузером и сервером. И хотя локальная разработка через localhost может выполняться в незащищенной среде, всё равно рекомендуется настроить HTTPS.

Настройка заключается в получении HTTPS-сертификатов, которые мы подключим в nginx. Будем использовать [mkcert](https://github.com/FiloSottile/mkcert) - утилиту для генерации локально-доверенных сертификатов с **собственным центром сертификации**. Она работает под всеми ОС и не требует какой-то конфигурации.

Переходим на страницу со сборками:

{% embed url="https://github.com/FiloSottile/mkcert/releases" %}

Загружаем версию для `windows-amd64`.

#### Если вы хотите использовать готовые сертификаты из репозитория

В таком случае скопируйте ключи из директории **mkcert** репо в директорию, на которую должна указывать переменная окружения **CAROOT**:

```bash
$ .\mkcert-v1.4.3-windows-amd64.exe -CAROOT
```

Обычно это домашняя папка вида `C:\Users\pantini\WebstormProjects\jpx\mkcert`. Установить переменную окружения можно следующим образом \(ваш путь к репо может отличаться\):

```bash
$ setx CAROOT "C:\Users\pantini\WebstormProjects\jpx\mkcert"
```

Далее выполняйте команду:

```bash
$ .\mkcert-v1.4.3-windows-amd64.exe -install
```

#### Если вы хотите создать сертификаты самостоятельно

Выполняем команду через [cmder](../wastelands/getting-started.md#cmder-installation):

```bash
$ .\mkcert-v1.4.3-windows-amd64.exe -install
```

Появится предупреждение об установке [CA](https://ru.wikipedia.org/wiki/%D0%A6%D0%B5%D0%BD%D1%82%D1%80_%D1%81%D0%B5%D1%80%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D0%B8), выбираем **Да**. Генерируем сертификаты с ключами для localhost и 127.0.0.1:

```bash
$ .\mkcert-v1.4.3-windows-amd64.exe localhost 127.0.0.1
```

Результат:

![](../.gitbook/assets/image%20%28229%29.png)

Теперь скопируем сертификат с ключом в репозиторий в поддиректорию **nginx-win/certs**, а также дополним конфигурацию nginx, заодно подключив [HTTP/2](https://ru.wikipedia.org/wiki/HTTP/2).

{% code title="terminal/nginx-win/conf/nginx.conf" %}
```coffeescript
master_process off;
daemon off;
error_log stderr;

events {
    worker_connections 1024;
}

http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;

    server {
        listen 8080 ssl http2;
        server_name localhost;

        ssl_certificate ../certs/localhost+1.pem;
        ssl_certificate_key ../certs/localhost+1-key.pem;

        ssl_protocols TLSv1.3;
        ssl_session_timeout 1d;
        ssl_prefer_server_ciphers off;
        ssl_session_tickets off;

        add_header Strict-Transport-Security "max-age=63072000" always;

        location / {
            add_header 'Content-Type' 'text/plain';
            return 200 'OK';
        }
    }
}

```
{% endcode %}

Перезапускаем nginx через конфигурацию WebStorm, проверяем соединение:

![&#x41F;&#x43E;&#x44F;&#x432;&#x438;&#x43B;&#x441;&#x44F; &quot;&#x437;&#x430;&#x43C;&#x43E;&#x447;&#x435;&#x43A;&quot; &#x432; &#x430;&#x434;&#x440;&#x435;&#x441;&#x43D;&#x43E;&#x439; &#x441;&#x442;&#x440;&#x43E;&#x43A;&#x435; - &#x432;&#x441;&#x451; &#x43A;&#x43E;&#x440;&#x440;&#x435;&#x43A;&#x442;&#x43D;&#x43E; &#x440;&#x430;&#x431;&#x43E;&#x442;&#x430;&#x435;&#x442;](../.gitbook/assets/image%20%28223%29.png)

#### Оформление терминала в виде [приложения](https://web.dev/progressive-web-apps/)

Чтобы трансформировать сайт в приложение \(с возможностью установки\), необходимо выполнить [ряд условий](https://web.dev/install-criteria/#criteria). 

Для начала создадим файл с манифестом, в котором перечислим такие данные, как название, описание, значки, цвета и т.д. Вся файловая работа сейчас будет происходить в поддиректории **terminal** репозитория.

{% code title="manifest.json" %}
```javascript
{
  "name":        "Pantini Terminal",
  "short_name":  "Pantini Terminal",
  "icons":       [
    {
      "src":   "/icons/terminal/icon-512x512.png",
      "type":  "image/png",
      "sizes": "512x512"
    },
    {
      "src":   "/icons/terminal/icon-192x192.png",
      "type":  "image/png",
      "sizes": "192x192"
    }
  ],
  "lang":        "ru-RU",
  "start_url":   "/",
  "display":     "standalone",
  "theme_color": "#2196f3"
}
```
{% endcode %}

Chrome требует, чтобы в манифесте как минимум были значки-картинки с размерами 512x512 и 192x192.

{% hint style="info" %}
Картинки были сгенерированы из файла `icons/favicon.svg` с помощью приложения [IncScape](https://inkscape.org/ru/)
{% endhint %}

Теперь создадим файл скрипта [Service Worker](https://developer.mozilla.org/ru/docs/Web/API/Service_Worker_API/Using_Service_Workers), который будет выполнять функции посредника между браузером и nginx. Скрипт `terminal-sw.js` должен содержать логику выдачи страниц терминала даже при [потере соединения с сервером](https://web.dev/offline-fallback-page/). Это неактуально при локальной разработке, но требование есть требование.

{% code title="terminal-sw.js" %}
```javascript
// Incrementing OFFLINE_VERSION will kick off the install event and force
// previously cached resources to be updated from the network.
// This variable is intentionally declared and unused.
// Add a comment for your linter if you want:
// eslint-disable-next-line no-unused-vars
const OFFLINE_VERSION = 1;

const CACHE_NAME  = 'offline';
const OFFLINE_URL = 'offline.html';

self.addEventListener('install', (event) => {
  event.waitUntil(
    (async () => {
      const cache = await caches.open(CACHE_NAME);
      // Setting {cache: 'reload'} in the new request will ensure that the
      // response isn't fulfilled from the HTTP cache; i.e., it will be from
      // the network.
      await cache.add(new Request(OFFLINE_URL, {cache: 'reload'}));
    })()
  );
  // Force the waiting service worker to become the active service worker.
  self.skipWaiting();
});

self.addEventListener('activate', (event) => {
  event.waitUntil(
    (async () => {
      // Enable navigation preload if it's supported.
      // See https://developers.google.com/web/updates/2017/02/navigation-preload
      if ('navigationPreload' in self.registration) {
        await self.registration.navigationPreload.enable();
      }
    })()
  );

  // Tell the active service worker to take control of the page immediately.
  self.clients.claim();
});

self.addEventListener('fetch', (event) => {
  // We only want to call event.respondWith() if this is a navigation request
  // for an HTML page.
  if (event.request.mode === 'navigate') {
    event.respondWith(
      (async () => {
        try {
          // First, try to use the navigation preload response if it's supported.
          const preloadResponse = await event.preloadResponse;
          if (preloadResponse) {
            return preloadResponse;
          }

          // Always try the network first.
          return await fetch(event.request);
        } catch (error) {
          // catch is only triggered if an exception is thrown, which is likely
          // due to a network error.
          // If fetch() returns a valid HTTP response with a response code in
          // the 4xx or 5xx range, the catch() will NOT be called.
          console.log('Fetch failed; returning offline page instead.', error);

          const cache = await caches.open(CACHE_NAME);
          return await cache.match(OFFLINE_URL);
        }
      })()
    );
  }

  // If our if() condition is false, then this fetch handler won't intercept the
  // request. If there are any other fetch handlers registered, they will get a
  // chance to call event.respondWith(). If no fetch handlers call
  // event.respondWith(), the request will be handled by the browser as if there
  // were no service worker involvement.
});
```
{% endcode %}

Теперь изготовим начальную страницу терминала `index.html`. Вёрстки пока нет, только техническая надпись для проверки работоспособности.

```markup
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pantini Terminal</title>
  <link rel="manifest" href="manifest.json">
  <link rel="icon" href="icons/favicon.svg" type="image/svg+xml">
  <link rel="apple-touch-icon" href="icons/apple-touch-icon.png">

  <meta name="mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="application-name" content="Pantini Terminal">
  <meta name="apple-mobile-web-app-title" content="Pantini Terminal">
  <meta name="msapplication-TileColor" content="#2196f3">
  <meta name="theme-color" content="#2196f3">
  <meta name="msapplication-navbutton-color" content="#2196f3">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <meta name="msapplication-starturl" content="/">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
</head>
<body>
<p>Dummy, not ready yet!</p>
</body>
<script>
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('terminal-sw.js');
  });
</script>
</html>
```

Изготовим простейшую страницу `offline.html`, которая будет отдаваться при отсутствии соединения с сервером:

{% code title="offline.html" %}
```markup
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pantini Terminal</title>
  <link rel="icon" href="icons/favicon.svg" type="image/svg+xml">
  <link rel="apple-touch-icon" href="icons/apple-touch-icon.png">
</head>
<body>
<p>Offline :(</p>
</body>
</html>
```
{% endcode %}

Наконец, скорректируем конфигурацию **nginx**, чтобы сайт "заработал":

{% code title="nginx-win/conf/nginx.conf" %}
```coffeescript
master_process off;
daemon off;
error_log stderr;

events {
    worker_connections 1024;
}

http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;

    server {
        listen 8080 ssl http2;
        server_name localhost;

        ssl_certificate ../certs/localhost+1.pem;
        ssl_certificate_key ../certs/localhost+1-key.pem;

        ssl_protocols TLSv1.3;
        ssl_session_timeout 1d;
        ssl_prefer_server_ciphers off;
        ssl_session_tickets off;

        add_header Strict-Transport-Security "max-age=63072000" always;

        root ..;
        index index.html;

        location / { deny all; }

        location =/terminal-sw.js {}
        location =/manifest.json {}
        location /icons/ {}

        location =/ {
            # internal redirect to =/index.html
        }

        location =/index.html {
        }

        location =/offline.html {
        }
    }
}
```
{% endcode %}

Проверим выдачу и нажмём на кнопку установки приложения \(в правой части адресной строки\):

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x43D;&#x430; &#x43A;&#x43D;&#x43E;&#x43F;&#x43A;&#x443; &#x441;&#x43B;&#x435;&#x432;&#x430; &#x43E;&#x442; &#x2B50;&#xFE0F; ](../.gitbook/assets/image%20%28228%29.png)

![&#x412;&#x44B;&#x431;&#x438;&#x440;&#x430;&#x435;&#x43C; &#x423;&#x441;&#x442;&#x430;&#x43D;&#x43E;&#x432;&#x438;&#x442;&#x44C;](../.gitbook/assets/image%20%28225%29.png)

Результат:

![&#x41F;&#x440;&#x438;&#x43B;&#x43E;&#x436;&#x435;&#x43D;&#x438;&#x435; &#x440;&#x430;&#x431;&#x43E;&#x442;&#x430;&#x435;&#x442;](../.gitbook/assets/image%20%28221%29.png)

Теперь перейдём в инструменты разработчика \(F12\), вкладка **Network**. В разделе **Throttling** \(справа от флага **Disable cache**\) выберем вариант **Offline**.

![](../.gitbook/assets/image%20%28231%29.png)

В таком режиме обновление приложения \(F5\) выдаст страницу `offline.html`

