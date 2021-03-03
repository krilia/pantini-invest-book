# ✏️ Подключение к рыночным данным

{% hint style="danger" %}
Материал этого раздела может быть изменен/удалён в любой момент без предупреждения
{% endhint %}

## Открытие брокерского счёта

Открываем по ссылке:

{% embed url="https://www.alorbroker.ru/open" %}

Дожидаемся активации портфелей - на почту придёт письмо с логином \(начинается на D\) и паролем.

Авторизуемся в терминале [Алор-Трейд](https://www.alorbroker.ru/trading/distributives).

![](../.gitbook/assets/image%20%28116%29.png)

Первый раз будет предложено изменить пароль - выполняем.

## Получение доступа к Alor OpenAPI

{% embed url="https://www.alorbroker.ru/blog/alor-openapi---prostoy-dostup-v-finteh" %}

Регистрируемся по ссылке:

{% embed url="https://alor.dev/register" %}

После регистрации подтверждаем учетную запись через почту. Далее входим в портал для разработчиков через [https://alor.dev/login](https://alor.dev/login) и авторизуемся.

Далее переходим в раздел с токенами доступа по ссылке [https://alor.dev/open-api-tokens](https://alor.dev/open-api-tokens)

Вводите логин и пароль от личного кабинета Alor, далее нажимайте кнопку **Связать аккаунт разработчика с торговым счетом**. Далее нажимайте кнопку **Выписать токен**. 

{% hint style="danger" %}
Полученный токен нужно хранить в секрете. Он равносилен логину и паролю.
{% endhint %}

Документация к Open API располагается по ссылке [https://alor.dev/docs](https://alor.dev/docs)

Информацию о нововведениях, а также техническую поддержку можно получить в официальном канале и чате Alor Open API:

[https://t.me/alor\_openapi](https://t.me/alor_openapi)

[https://t.me/alor\_openapi\_chat](https://t.me/alor_openapi_chat)

## Установка Redis на Windows \(опционально\)

Redis - это база данных с дополнительными возможностями, через которую терминал будет взаимодействовать с некоторыми поставщиками рыночных данных. Скачиваем здесь:

{% embed url="https://github.com/microsoftarchive/redis/releases" %}

Выбираем файл [Redis-x64-3.2.100.msi](https://github.com/microsoftarchive/redis/releases/download/win-3.2.100/Redis-x64-3.2.100.msi), запускаем, устанавливаем. Указываем порт **26379**. 

![](../.gitbook/assets/image%20%28111%29.png)

![](../.gitbook/assets/image%20%28110%29.png)

![](../.gitbook/assets/image%20%28109%29.png)

![](../.gitbook/assets/image%20%28114%29.png)

![](../.gitbook/assets/image%20%28113%29.png)

После установки Redis будет запущен как служба Windows с автозагрузкой при запуске системы.

## Установка Visual Studio \(опционально\)

Понадобится для сборки проектов на C\# \(касается некоторых брокеров\). Переходим на страницу загрузки:

{% embed url="https://visualstudio.microsoft.com/ru/" %}

Скачиваем версию **Community** через кнопку **Скачать Visual Studio**. ****Загрузка начнётся автоматически, по окончании запускаем файл. Установщик предложит выбрать компоненты, можно выбрать все, а можно только два: **Разработка классических приложений .NET** и **Разработка классических приложений на C++**.

![](../.gitbook/assets/image%20%28108%29.png)

