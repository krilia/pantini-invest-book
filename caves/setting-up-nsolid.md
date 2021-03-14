# Установка N\|Solid

Мы уже [установили Node.js](../wastelands/getting-started.md#setting-up-node-js) на локальный компьютер. Сейчас сделаем примерно то же самое, но для виртуальной машины.

N\|Solid  - это Node.js для Enterprise, содержащая, помимо основной платформы, ещё и веб-интерфейс.

{% embed url="https://nodesource.com/products/nsolid" %}

Для разработчиков N\|Solid предоставляется бесплатно \(можно запускать до 5 экземпляров\):

![](../.gitbook/assets/image%20%28171%29.png)

N\|Solid понадобится нам для различных тестов и служебных задач.

## Регистрация учётной записи

Переходим по ссылке:

{% embed url="https://accounts.nodesource.com/sign-up" %}

Будет предложено авторизоваться через Github или Google. Можно создать учётную запись по E-mail. В любом случае при регистрации вы получите письмо на почту с ключом доступа, который понадобится позднее:

![](../.gitbook/assets/image%20%28184%29.png)

{% hint style="danger" %}
Если ключ не приходит на почту, попробуйте [зарегистрироваться ](https://accounts.nodesource.com/sign-in)через VPN на другой e-mail.
{% endhint %}

##  Установка в RHEL

Авторизуйтесь в систему через терминал, как делали [ранее](rhel-setup.md#rhel-webstorm-connection):

![&#x41D;&#x430; &#x442;&#x435;&#x43A;&#x441;&#x442; jpx@1.0.0 &#x43F;&#x43E;&#x43A;&#x430; &#x43D;&#x435; &#x43E;&#x431;&#x440;&#x430;&#x449;&#x430;&#x439;&#x442;&#x435; &#x432;&#x43D;&#x438;&#x43C;&#x430;&#x43D;&#x438;&#x44F;](../.gitbook/assets/image%20%28176%29.png)

Мы установим версию [Fermium](https://docs.nodesource.com/nsolid/4.5/docs#nsolid-runtime), последнюю на момент написания этого раздела \(соответствует Node.js 14\). Инструкция находится по ссылке:

{% embed url="https://docs.nodesource.com/nsolid/4.5/docs\#installing-nsolid-packages" %}

Выполняем команду:

```text
# curl -sL https://nsolid-rpm.nodesource.com/nsolid_setup_4.x | bash -
```

Эта команда внесёт ссылки на нужные нам библиотеки в системный список \(/etc/yum.repos.d\). Далее устанавливаем платформу N\|Solid:

```text
# dnf -y install nsolid-fermium
```

Теперь установим веб-интерфейс консоли N\|Solid:

```text
# dnf -y install nsolid-console
```

Запускаем консоль после установки:

```text
# systemctl start nsolid-console
```

Если всё в порядке, консоль перейдёт в статус active:

```text
# systemctl status nsolid-console
```

![&#x427;&#x442;&#x43E;&#x431;&#x44B; &#x432;&#x44B;&#x439;&#x442;&#x438; &#x438;&#x437; &#x44D;&#x442;&#x43E;&#x439; &#x43A;&#x43E;&#x43C;&#x430;&#x43D;&#x434;&#x44B;, &#x43D;&#x430;&#x436;&#x43C;&#x438;&#x442;&#x435; q &#x438;&#x43B;&#x438; Ctrl+C](../.gitbook/assets/image%20%28170%29.png)

#### Проверка работы, ввод ключа

Проверим версию установленной платформы Node.js \(обе команды одинаковы\):

```text
# node -v
# nsolid -v
```

Откройте браузер, введите адрес виртуальной машины и порт **6753**:

![&#x41A;&#x43E;&#x43D;&#x441;&#x43E;&#x43B;&#x44C; N\|Solid &#x440;&#x430;&#x431;&#x43E;&#x442;&#x430;&#x435;&#x442; &#x43D;&#x430; &#x43F;&#x43E;&#x440;&#x442;&#x443; 6753](../.gitbook/assets/image%20%28182%29.png)

Тем не менее, если попытаться прочитать содержимое изнутри RHEL, то нас успешно перенаправят на страницу /welcome:

```text
# curl localhost:6753

Выведет:
Found. Redirecting to /welcome/
```

{% hint style="info" %}
Команда curl позволяет выполнять запросы к сетевым \(и не только\) ресурсам. localhost - это "внутренний адрес" виртуальной машины.
{% endhint %}

Если доступ есть внутри системы, а изнутри его нет, то можно сделать вывод, что срабатывает какая-то защита.

{% hint style="info" %}
В данном случае эта защита - сетевой экран \(firewall\), не позволяющий обращаться к порту 6753 извне системы RHEL.
{% endhint %}

Добавим порт 6753 в список доступных извне, выполнив две команды:

```text
# firewall-cmd --zone=public --permanent --add-port=6753/tcp
# systemctl restart firewalld
```

Теперь:

![](../.gitbook/assets/image%20%28174%29.png)

Нажмите на ссылку **ENTER LICENSE KEY**, введите ключ, полученный ранее:

![](../.gitbook/assets/image%20%28172%29.png)

После ввода ключа вас вернут на прежнюю страницу; авторизуйтесь через Google или Github \(или по E-mail\), перейдите в раздел **Settings**, убедитесь, что ключ записался:

![](../.gitbook/assets/image%20%28173%29.png)

На вкладке **Show Demo** можно посмотреть на возможности N\|Solid с небольшим обучением. Мы же переключимся на вкладку **Applications**, где пока ничего не запущено:

![](../.gitbook/assets/image%20%28185%29.png)

Попробуем запустить простейшее приложение. Сделаем веб-сервер, который будет отдавать страницу с текстом [Hello World](https://nodejs.org/en/about/).

Для начала оформим в проекте файл **package.json**, в котором пока что перечислим некоторые атрибуты проекта \(название, версия, описание\):

{% code title="package.json" %}
```javascript
{
  "name": "jpx",
  "description": "Trading goodies by John Pantini",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/johnpantini/jpx.git"
  },
  "license": "MIT",
  "version": "1.0.0",
  "dependencies": {},
  "devDependencies": {
  }
}

```
{% endcode %}

![](../.gitbook/assets/image%20%28180%29.png)

Теперь создадим папку nsolid через WebStorm, а в ней файл test.mjs:

{% code title="nsolid/test.mjs" %}
```javascript
import http from 'http';

const hostname = '0.0.0.0';
const port = 8080;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});

```
{% endcode %}

Откроем порт 8080:

```javascript
# firewall-cmd --permanent --add-port=8080/tcp
# firewall-cmd --reload
```

Переместимся в папку **nsolid** в RHEL и запустим веб-сервер командами:

```javascript
# cd /mnt/hgfs/jpx/nsolid/
# NSOLID_APPNAME=nsolid-test NSOLID_COMMAND=localhost:9001 nsolid test.mjs
```

{% hint style="info" %}
Переменная окружения NSOLID\_APPNAME определяет то, как наш процесс будет называться в веб-интерфейсе.

NSOLID\_COMMAND нужна для связывания запущенного процесса с веб-интерфейсом
{% endhint %}

![](../.gitbook/assets/image%20%28181%29.png)

Если всё сделано верно, процесс появится в консоли:

![](../.gitbook/assets/image%20%28179%29.png)

А также будет доступен из браузера на локальном компьютере:

![](../.gitbook/assets/image%20%28175%29.png)



