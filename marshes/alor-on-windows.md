# ✏️ Подключение к рыночным данным Alor на Windows

## Открытие брокерского счёта

Открываем по ссылке:

{% embed url="https://www.alorbroker.ru/open" %}

Дожидаемся активации портфелей - на почту придёт письмо с логином \(начинается на D\) и паролем.

Авторизуемся в терминале [Алор-Трейд](https://www.alorbroker.ru/trading/distributives).

![](../.gitbook/assets/image%20%28114%29.png)

Первый раз будет предложено изменить пароль - выполняем.

## Установка Redis

Redis - это база данных с дополнительными возможностями, через которую терминал будет взаимодействовать с поставщиком рыночных данных Alor. Скачиваем здесь:

{% embed url="https://github.com/microsoftarchive/redis/releases" %}

Выбираем файл  [Redis-x64-3.2.100.msi](https://github.com/microsoftarchive/redis/releases/download/win-3.2.100/Redis-x64-3.2.100.msi), запускаем, устанавливаем. Указываем порт **26379**. 

![](../.gitbook/assets/image%20%28110%29.png)

![](../.gitbook/assets/image%20%28109%29.png)

![](../.gitbook/assets/image%20%28108%29.png)

![](../.gitbook/assets/image%20%28113%29.png)

![](../.gitbook/assets/image%20%28112%29.png)

После установки Redis будет запущен как служба Windows с автозагрузкой при запуске системы.

