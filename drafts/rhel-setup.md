# Установка RHEL

Red Hat Enterprise Linux — дистрибутив Linux компании Red Hat, которую приобрела IBM. Для задач, связанных с разработкой, эта операционная система предоставляется бесплатно.

## Создание учётной записи разработчика

Переходим по ссылке:

{% embed url="https://developers.redhat.com/products/rhel/download" %}

Нажимаем **x86\_64** в разделе **Boot iso**.

![](../.gitbook/assets/image%20%28160%29.png)

Нас переводят на страницу входа в аккаунт. Если его нет, переходим по ссылке **Create one now** и создаём учётную запись. Подтверждаем аккаунт через почту \(придёт ссылка\). Далее загрузка дистрибутива в виде образа ISO начнётся автоматически.

## Установка RHEL в VMWare Workstation Player \(Windows\)

Запускаем VMWare Player:

![](../.gitbook/assets/image%20%28123%29.png)

Нажимаем **Create a New Virtual Machine** и выбираем вариант **I will install the operating system later**:

![](../.gitbook/assets/image%20%28134%29.png)

Далее выбираем тип системы:

![](../.gitbook/assets/image%20%28136%29.png)

Далее выбираем название, которое будет отображаться в интерфейсе VMWare Player, а также расположение файлов системы:

![](../.gitbook/assets/image%20%28135%29.png)

Далее указываем максимальный размер дискового пространства для RHEL. Для наших целей подойдут [минимальные ](https://access.redhat.com/articles/rhel-limits)10 GB.

![](../.gitbook/assets/image%20%28127%29.png)

В конце будет показана сводка с параметрами и оборудованием устанавливаемой системы:

![](../.gitbook/assets/image%20%28161%29.png)

Нажимаем **Customize Hardware...**, переходим в раздел **New CD/DVD \(SATA\)**, где выбираем загруженный ранее файл с образом ISO:

![](../.gitbook/assets/image%20%28147%29.png)

Можно отключить звук за ненадобностью:

![](../.gitbook/assets/image%20%28132%29.png)

Закрываем окно, нажимаем **Finish**:

![&#x412;&#x438;&#x440;&#x442;&#x443;&#x430;&#x43B;&#x44C;&#x43D;&#x430;&#x44F; &#x43C;&#x430;&#x448;&#x438;&#x43D;&#x430; &#x433;&#x43E;&#x442;&#x43E;&#x432;&#x430; &#x43A; &#x437;&#x430;&#x43F;&#x443;&#x441;&#x43A;&#x443;](../.gitbook/assets/image%20%28157%29.png)

#### Настройка общей папки

Далее мы сделаем папку с проектом видимой для файловой системы виртуальной машины.

Нажимаем **Edit virtual machine settings**, переходим на вкладку **Options**, далее в раздел **Shared Folders**. Здесь нужно включить общие папки, затем через кнопку **Add...** добавить папку репозитория под псевдонимом **jpx**:

![](../.gitbook/assets/image%20%28151%29.png)

![](../.gitbook/assets/image%20%28144%29.png)

{% hint style="info" %}
Путь к папке с проектом не должен содержать кириллические символы.
{% endhint %}

![](../.gitbook/assets/image%20%28155%29.png)

Итоговый результат:

![](../.gitbook/assets/image%20%28163%29.png)

Нажимаем ОК, далее - **Play virtual machine**. Если старт сопровождается ошибками, то нужно выполнить инструкцию по их устранению. Так, при первом запуске на моей системе оказалось, что AMD-V отключен:

![](../.gitbook/assets/image%20%28152%29.png)

Правка соответствующей настройки \(Advanced ➡️ CPU Configuration ➡️ SVM Mode\) в BIOS при перезагрузке устранила проблему.

![&#x41F;&#x440;&#x438;&#x432;&#x435;&#x442;&#x441;&#x442;&#x432;&#x435;&#x43D;&#x43D;&#x44B;&#x439; &#x438;&#x43D;&#x442;&#x435;&#x440;&#x444;&#x435;&#x439;&#x441; &#x443;&#x441;&#x442;&#x430;&#x43D;&#x43E;&#x432;&#x43A;&#x438; RHEL](../.gitbook/assets/image%20%28133%29.png)

{% hint style="info" %}
Может быть предложено подключить периферийные устройства в виртуальную машину. Пропускаем это предложение.
{% endhint %}

{% hint style="info" %}
Если вы нажмёте мышью в окне виртуальной машины, произойдёт "захват" курсора. Чтобы выйти из этого режима, надо нажать сочетание Ctrl+Alt. 
{% endhint %}

#### Установка системы

Выбираем пункт **Install Red Hat Enterprise Linux 8.3** стрелками на клавиатуре, далее Enter. После инициализации ядра и запуска установщика, попадем в следующий интерфейс выбора языка:

![&#x41E;&#x441;&#x442;&#x430;&#x432;&#x43B;&#x44F;&#x435;&#x43C; &#x431;&#x435;&#x437; &#x438;&#x437;&#x43C;&#x435;&#x43D;&#x435;&#x43D;&#x438;&#x439;, &#x43D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; Continue](../.gitbook/assets/image%20%28162%29.png)

Далее попадаем на установочную сводку, где значком ⚠️ помечены настройки, требующие внимания:

![](../.gitbook/assets/image%20%28165%29.png)

Выбираем раздел **Installation Destination** \(вверху справа\) и нажимаем **Done**, ничего не изменяя:

![](../.gitbook/assets/image%20%28159%29.png)

Далее выбираем раздел **Network & Host Name**, включаем предложенный адаптер в состояние **On**, в поле ввода **Host Name** вводим любое название машины, например, **rhel8**, далее нажимаем **Apply** и затем **Done**:

![](../.gitbook/assets/image%20%28131%29.png)

Теперь можно авторизоваться с учётной записью Red Hat в разделе **Connect to Red Hat**. Вводим учётные данные, нажимаем **Register** и ожидаем активации подписки:

![&#x417;&#x430;&#x442;&#x435;&#x43C; &#x43D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; Done](../.gitbook/assets/image%20%28140%29.png)

Теперь переходим в раздел **Software Selection**, выбираем вариант **Minimal Install** с опциями, указанными на картинке:

![&#x414;&#x430;&#x43B;&#x435;&#x435; &#x43D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; Done](../.gitbook/assets/image%20%28153%29.png)

Осталось задать пароль для администратора системы, раздел **Root Password**:

![](../.gitbook/assets/image%20%28142%29.png)

Вводим желаемый пароль, нажимаем **Done**. Если пароль не слишком длинный, придётся нажать **Done** 2 раза. Теперь всё готово для установки, нажимаем **Begin Installation**:

![](../.gitbook/assets/image%20%28128%29.png)

После установки нужно будет перезагрузиться через кнопку **Reboot System**. Далее система будет запущена и готова к работе:

![](../.gitbook/assets/image%20%28139%29.png)

#### Просмотр файловой структуры через GUI

Чтобы иметь легкий доступ к файлам в RHEL, можно загрузить приложение WinSCP \(зелёная кнопка\):

{% embed url="https://winscp.net/eng/download.php" %}

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x41F;&#x440;&#x438;&#x43D;&#x44F;&#x442;&#x44C;](../.gitbook/assets/image%20%28126%29.png)

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x414;&#x430;&#x43B;&#x435;&#x435;](../.gitbook/assets/image%20%28138%29.png)

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x423;&#x441;&#x442;&#x430;&#x43D;&#x43E;&#x432;&#x438;&#x442;&#x44C;](../.gitbook/assets/image%20%28129%29.png)

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x417;&#x430;&#x432;&#x435;&#x440;&#x448;&#x438;&#x442;&#x44C;](../.gitbook/assets/image%20%28148%29.png)

При запуске будет предложено создать новое подключение:

![](../.gitbook/assets/image%20%28154%29.png)

В имя хоста нужно ввести сетевой адрес RHEL. Чтобы его узнать, вернитесь в **VMWare Player**, введите логин **root** и пароль \(см. ниже информацию про ввод пароля\) который указывали ранее при установке RHEL. Далее выполните команду, введя с клавиатуры \(затем Enter\):

```text
# ifconfig
```

{% hint style="info" %}
Символ \# вводить не нужно. Он означает, что команда вводится в режиме администратора.
{% endhint %}

{% hint style="danger" %}
При вводе пароля вы не увидите символов, но на деле пароль вводится.
{% endhint %}

![](../.gitbook/assets/image%20%28146%29.png)

В моём случае адрес машины **192.168.40.128**. Адрес **127.0.0.1** - это локальный адрес виртуальной машины, доступный только изнутри, т.н. [loopback](https://ru.wikipedia.org/wiki/Loopback).

![](../.gitbook/assets/image%20%28158%29.png)

Нажимаем **Ещё...**, переходим в раздел **Подключение**, выставляем настройки:

![&#x41F;&#x443;&#x441;&#x442;&#x44B;&#x435; &#x43F;&#x430;&#x43A;&#x435;&#x442;&#x44B; SSH &#x43F;&#x43E;&#x43C;&#x43E;&#x433;&#x443;&#x442; &#x434;&#x435;&#x440;&#x436;&#x430;&#x442;&#x44C; &#x441;&#x43E;&#x435;&#x434;&#x438;&#x43D;&#x435;&#x43D;&#x438;&#x435; &#x434;&#x43E;&#x43B;&#x44C;&#x448;&#x435;](../.gitbook/assets/image%20%28167%29.png)

Далее в разделе SSH/Аутентификация отключите флаг **Пробовать аутентификацию GSSAPI \(SSH-2\)**. Это может ускорить вход в систему по SSH через WinSCP.

![](../.gitbook/assets/image%20%28177%29.png)

Нажимаем ОК, далее нажимаем **Сохранить**, и появляется следующее окно:

![](../.gitbook/assets/image%20%28137%29.png)

Нажимаем ОК, затем - **Войти**. Если появится предупреждение о соединении с неизвестным сервером, нажимаем **Да**.

![&#x421;&#x43F;&#x440;&#x430;&#x432;&#x430; &#x444;&#x430;&#x439;&#x43B;&#x43E;&#x432;&#x430;&#x44F; &#x441;&#x438;&#x441;&#x442;&#x435;&#x43C;&#x430; RHEL, &#x441;&#x43B;&#x435;&#x432;&#x430; - &#x43A;&#x43E;&#x43C;&#x43F;&#x44C;&#x44E;&#x442;&#x435;&#x440;&#x430;](../.gitbook/assets/image%20%28149%29.png)

#### Настройка текстового редактора в WinSCP

Настроим текстовый редактор для WinSCP. Для этого загрузим [Notepad++](https://notepad-plus-plus.org/downloads/) \(или другой любимый редактор\), после чего укажем его в настройках \(в WinSCP Меню **Параметры**, далее **Настройки**\) как предпочтительный:

![&#x41D;&#x430;&#x436;&#x438;&#x43C;&#x430;&#x435;&#x43C; &#x414;&#x43E;&#x431;&#x430;&#x432;&#x438;&#x442;&#x44C;...](../.gitbook/assets/image%20%28164%29.png)

![&#x412;&#x44B;&#x431;&#x438;&#x440;&#x430;&#x435;&#x43C; notepad++.exe &#x447;&#x435;&#x440;&#x435;&#x437; &#x41E;&#x431;&#x437;&#x43E;&#x440;...](../.gitbook/assets/image%20%28143%29.png)

![&#x41F;&#x435;&#x440;&#x435;&#x43D;&#x43E;&#x441;&#x438;&#x43C; Notepad++ &#x432; &#x43D;&#x430;&#x447;&#x430;&#x43B;&#x43E; &#x43A;&#x43D;&#x43E;&#x43F;&#x43A;&#x43E;&#x439; &#x412;&#x432;&#x435;&#x440;&#x445;](../.gitbook/assets/image%20%28141%29.png)

Теперь файлы в WinSCP будут открывать в Notepad++.

#### Подключение к RHEL изнутри WebStorm <a id="rhel-webstorm-connection"></a>

Для этого открываем уже привычный терминал [Cmder](getting-started.md#cmder-installation), вводим команду \(**символы &lt; и &gt; вводить не нужно**\):

```text
$ ssh root@<IP-адрес машины>
```

При первом подключении появится предупреждение **The authenticity of host can't be established**. Вписываем ответ **yes**, нажимаем **Enter**. Далее вводим пароль и ещё раз Enter:

![&#x41F;&#x43E;&#x43A;&#x430; &#x43D;&#x435; &#x43E;&#x431;&#x440;&#x430;&#x449;&#x430;&#x439;&#x442;&#x435; &#x432;&#x43D;&#x438;&#x43C;&#x430;&#x43D;&#x438;&#x44F; &#x43D;&#x430; &#x43F;&#x430;&#x43F;&#x43A;&#x443; node\_modules &#x438; &#x444;&#x430;&#x439;&#x43B;&#x44B; package\*.json &#x43D;&#x430; &#x43A;&#x430;&#x440;&#x442;&#x438;&#x43D;&#x43A;&#x435;](../.gitbook/assets/image%20%28145%29.png)

Теперь можно вводить команды на машине RHEL, например:

```bash
# pwd
```

Эта команда выведет текущую директорию.

#### Проверка общей папки

Ранее на этапе настройки виртуальной машины мы добавили папку с проектом в список общих директорий с файловой системой RHEL. Чтобы проверить, что эта функциональность корректно работает, нужно в WinSCP перейти в директорию /mnt из корня файловой системы:

![&#x41F;&#x435;&#x440;&#x435;&#x445;&#x43E;&#x434;&#x438;&#x43C; &#x432; /mnt](../.gitbook/assets/image%20%28156%29.png)

Обновляем содержимое директории /mnt через **Ctrl+R** \(две зелёных стрелки на панели значков\). Должна появиться поддиректория **hgfs**. Даже если она есть, проводим дополнительную настройку. Переходим в директорию /etc, открываем файл **fstab**, добавляем туда ещё одну строчку в конец:

```bash
.host:/ /mnt/hgfs fuse.vmhgfs-fuse allow_other 0 0
```

После чего перезагружаем RHEL либо через VMWare Player \(Меню **Player**, затем **Power**, затем **Restart Guest**\), либо через терминал, введя команду:

```css
# reboot now
```

{% hint style="danger" %}
Для подключения общей папки без правки /etc/fstab можно воспользоваться командой \(при перезагрузке изменения потеряются\):

```bash
# vmhgfs-fuse .host:/ /mnt/hgfs -o subtype=vmhgfs-fuse,allow_other
```
{% endhint %}

После перезагрузки нужно повторно подключиться к RHEL. Теперь в директории /mnt появится поддиректория **hgfs**, а в ней - нужна нам синхронизированная **jpx**. Можно проверить список директорий командой **ls**:

![](../.gitbook/assets/image%20%28166%29.png)

{% hint style="info" %}
Команда **cd** служит для изменения текущей директории.
{% endhint %}

![&#x41D;&#x435; &#x437;&#x430;&#x431;&#x443;&#x434;&#x44C;&#x442;&#x435; &#x43E;&#x431;&#x43D;&#x43E;&#x432;&#x438;&#x442;&#x44C; &#x441;&#x43E;&#x434;&#x435;&#x440;&#x436;&#x438;&#x43C;&#x43E;&#x435; &#x43E;&#x43A;&#x43D;&#x430; &#x447;&#x435;&#x440;&#x435;&#x437; Ctrl+R \(&#x434;&#x432;&#x435; &#x437;&#x435;&#x43B;&#x451;&#x43D;&#x44B;&#x445; &#x441;&#x442;&#x440;&#x435;&#x43B;&#x43A;&#x438; &#x43D;&#x430; &#x43F;&#x430;&#x43D;&#x435;&#x43B;&#x438; &#x437;&#x43D;&#x430;&#x447;&#x43A;&#x43E;&#x432;\)](../.gitbook/assets/image%20%28150%29.png)

На этом начальная настройка виртуальной машины завершена.
