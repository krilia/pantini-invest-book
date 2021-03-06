# GitHub



#### Переименование репозитория

Сейчас, когда fork создан, ваша копия имеет такое же имя, что и оригинал \(johnpantini.github.io\). Нужно переименовать репо, используя своё имя пользователя. Для этого переходим в настройки, вкладка **Settings**:

![&#x412;&#x43A;&#x43B;&#x430;&#x434;&#x43A;&#x430; Settings &#x43F;&#x43E;&#x434;&#x441;&#x432;&#x435;&#x447;&#x435;&#x43D;&#x430; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x43C;](../.gitbook/assets/image%20%28238%29.png)

Вместо johnpantini вводим своё имя пользователя \(которое указывали при регистрации\) и нажимаем **Rename**:

![](../.gitbook/assets/image%20%28249%29.png)

{% hint style="info" %}
Например, пользователь [https://github.com/catmandante](https://github.com/catmandante) указывал бы Repository name в виде [catmandante.github.io](https://github.com/catmandante/catmandante.github.io)
{% endhint %}

{% hint style="danger" %}
Можно указать любое допустимое имя даже без домена github.io, например, **my-terminal**.

В таком случае ваш сайт с терминалом примет вид \(вместо звёздочки будет ваше имя пользователя\):

\*.github.io/my-terminal
{% endhint %}

#### Настройка персонального токена

На текущий момент у вас уже будет работать сайт GitHub Pages `*.github.io` с поддоменом в виде вашего имени пользователя вместо звёздочки.

Персональный токен понадобится для выполнения авторизованных запросов к API GitHub. Это будет необходимо для синхронизации вашего сайта с содержимым копии fork после принятия правок из оригинального репо.

Переходим в **настройки персональных токенов**:

{% embed url="https://github.com/settings/tokens" %}

Нажимаем кнопку **Generate new token**. В следующей форме вводим в поле **Note** содержимое USER\_TOKEN, ставим флаг **repo**:

![&#x421;&#x442;&#x430;&#x432;&#x438;&#x43C; &#x442;&#x43E;&#x43B;&#x44C;&#x43A;&#x43E; &#x444;&#x43B;&#x430;&#x433; repo](../.gitbook/assets/image%20%28247%29.png)

Нажимаем кнопку **Generate token**. После этого на экране появится ваш персональный токен. Скопируйте его.

{% hint style="danger" %}
Если вы закроете страницу на данном этапе, не скопировав токен, он потеряется. В таком случае нужно будет его удалить на [странице ](https://github.com/settings/tokens)и создать заново.
{% endhint %}

Теперь необходимо поместить скопированный токен в хранилище секретов репозитория. Для этого перейдите в настройки репозитория, вкладка **Settings**:

![&#x412;&#x43A;&#x43B;&#x430;&#x434;&#x43A;&#x430; Settings &#x43F;&#x43E;&#x434;&#x441;&#x432;&#x435;&#x447;&#x435;&#x43D;&#x430; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x43C;](../.gitbook/assets/image%20%28238%29.png)

Из боковой панели вкладок выбираем пункт **Secrets**:

![&#x412;&#x43A;&#x43B;&#x430;&#x434;&#x43A;&#x430; Secrets &#x43F;&#x43E;&#x434;&#x441;&#x432;&#x435;&#x447;&#x435;&#x43D;&#x430; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x43C;](../.gitbook/assets/image%20%28242%29.png)

Нажимаем кнопку **New repository secret**:

![&#x412;&#x441;&#x442;&#x430;&#x432;&#x43B;&#x44F;&#x435;&#x43C; &#x442;&#x43E;&#x43A;&#x435;&#x43D; &#x438; &#x43D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; Add secret](../.gitbook/assets/image%20%28236%29.png)

Теперь можно будет выполнять ручное обновление своего сайта GitHub Pages в любой момент. Процедура будет рассмотрена в следующем разделе ниже. 

#### Настройка GitHub Pages

GitHub Pages - бесплатный хостинг статических сайтов прямо из репозитория GitHub. Для активации, находясь в том же разделе настроек, слева из боковой панели вкладок надо выбрать пункт **Pages**:

![&#x412;&#x43A;&#x43B;&#x430;&#x434;&#x43A;&#x430; Pages &#x43F;&#x43E;&#x434;&#x441;&#x432;&#x435;&#x447;&#x435;&#x43D;&#x430; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x43C;](../.gitbook/assets/image%20%28237%29.png)

Теперь выбираем из выпадающего списка **Source** ветку johnpantini, папку оставляем **/ \(root\)**, нажимаем **Save**:

![](../.gitbook/assets/image%20%28239%29.png)

{% hint style="info" %}
GitHub начнёт первичную сборку сайта. Стоит подождать минуту и обновить страницу, чтобы появилась такая же зелёная плашка, как на фрагменте снимка экрана выше.
{% endhint %}

## Обновление терминала

Обновление терминала состоит из двух этапов:

1. Приём изменений из [оригинального репо](https://github.com/johnpantini/johnpantini.github.io)
2. Пересборка своего сайта GitHub Pages

#### Приём изменений из оригинального репо

Переходите в свою копию репо,  **выбирайте ветвь johnpantini** и нажимайте кнопку **Fetch upstream**:

![&#x412;&#x430;&#x436;&#x43D;&#x44B;&#x435; &#x44D;&#x43B;&#x435;&#x43C;&#x435;&#x43D;&#x442;&#x44B; &#x443;&#x43F;&#x440;&#x430;&#x432;&#x43B;&#x435;&#x43D;&#x438;&#x44F; &#x432;&#x44B;&#x434;&#x435;&#x43B;&#x435;&#x43D;&#x44B; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x43C;](../.gitbook/assets/image%20%28241%29.png)

В появившемся окне нажимаем кнопку **Fetch and merge**:

![](../.gitbook/assets/image%20%28246%29.png)

Теперь ваш репо будет синхронизирован с оригиналом.

#### Пересборка сайта

Переходим на вкладку **Actions**:

![&#x412;&#x43A;&#x43B;&#x430;&#x434;&#x43A;&#x430; Actions &#x432;&#x44B;&#x434;&#x435;&#x43B;&#x435;&#x43D;&#x430; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x43C;](../.gitbook/assets/image%20%28240%29.png)

{% hint style="info" %}
Первый раз вы можете получить предупреждение, что GitHub отключил все рабочие процессы \(workflows\) в целях безопасности, потому что вы приняли их через fork из стороннего репо. 

Нажимаем кнопку **I understand my workflows, go ahead and enable them**
{% endhint %}

На странице действий выбираем пункт **Rebuild GitHub Pages** из боковой панели, далее нажимаем кнопку **Run workflow**, и во всплывающем окне ещё раз **Run workflow**:

![](../.gitbook/assets/image%20%28245%29.png)

Ожидаем несколько минут, пока действие не выполнится до конца. Страница будет обновляться автоматически:

![&#x41E;&#x43F;&#x435;&#x440;&#x430;&#x446;&#x438;&#x44F; &#x432;&#x44B;&#x43F;&#x43E;&#x43B;&#x43D;&#x435;&#x43D;&#x430; &#x443;&#x441;&#x43F;&#x435;&#x448;&#x43D;&#x43E;](../.gitbook/assets/image%20%28243%29.png)

Теперь стоит подождать несколько минут, ваш сайт обновится.

## Дополнительно: установка терминала в виде приложения

Это действие требуется выполнить только один раз. В браузере Google Chrome/Google Chrome Canary/Blisk при открытии вашего сайта в адресной строке справа появится кнопка установки приложения. Нажимайте её, далее **Установить**:

![&#x412; &#x431;&#x440;&#x430;&#x443;&#x437;&#x435;&#x440;&#x435; Blisk, &#x434;&#x43B;&#x44F; Chrome &#x43A;&#x43D;&#x43E;&#x43F;&#x43A;&#x430; &#x43D;&#x430;&#x445;&#x43E;&#x434;&#x438;&#x442;&#x441;&#x44F; &#x432; &#x430;&#x43D;&#x430;&#x43B;&#x43E;&#x433;&#x438;&#x447;&#x43D;&#x43E;&#x43C; &#x43C;&#x435;&#x441;&#x442;&#x435;](../.gitbook/assets/image%20%28248%29.png)

{% hint style="danger" %}
Работайте **только** с вашим сайтом \*.github.io, где звёздочка - ваше имя пользователя на GitHub. Только в таком случае вы будете в полной безопасности от возможной кражи паролей доступа к брокерским счетам, токенов и т.д.
{% endhint %}

## Дополнительно: установка терминала на Android

Загружаем приложение **Kiwi Browser**. Браузер поддерживает установку расширений.

{% embed url="https://kiwibrowser.com/" %}

Открываем свой сайт с терминалом,  в меню \(в правом верхнем углу рядом с адресной строкой\) выбираем пункт **Добавить на главный экран**:

![&#x414;&#x43E;&#x431;&#x430;&#x432;&#x438;&#x442;&#x44C; &#x43D;&#x430; &#x433;&#x43B;&#x430;&#x432;&#x43D;&#x44B;&#x439; &#x44D;&#x43A;&#x440;&#x430;&#x43D;](../.gitbook/assets/image%20%28251%29.png)

Нажимаем **Добавить**:

