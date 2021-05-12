# Аврора - сохранение пароля и автоматический вход

{% hint style="warning" %}
Для этого раздела понадобятся следующие языки, программы и технологии: 

[C\#](https://metanit.com/sharp/tutorial/), [dnSpy](https://github.com/dnSpy/dnSpy/releases), [Aurora](https://unitedtraders.com/trading/aurora)
{% endhint %}

{% hint style="danger" %}
 Любая информация, содержащаяся в этом разделе, _предоставляется в ознакомительных целях_ и носит обзорный характер. Данная информация не является руководством к действию.
{% endhint %}

{% hint style="danger" %}
В этом разделе будет модифицирован терминал Аврора. Настоятельно рекомендую повторить весь процесс самостоятельно. Только так вы получите гарантию отсутствия каких-либо вирусов или вредоносных эффектов. 

Не открывайте файл терминала, полученный от сторонних лиц. 
{% endhint %}

## Задачи

{% hint style="info" %}
Скачать Аврору, а также получить больше информации можно [по ссылке](../faq/aurora.md).

**Для работы используется версия Авроры 3.33.1.102**
{% endhint %}

![&#x424;&#x43E;&#x440;&#x43C;&#x430; &#x432;&#x445;&#x43E;&#x434;&#x430; &#x432; &#x410;&#x432;&#x440;&#x43E;&#x440;&#x443;](../.gitbook/assets/image%20%2851%29.png)

При запуске Аврора проверяет обновления, после чего встречает нас формой для ввода логина и пароля. Штатными средствами пароль не сохраняется, автоматический вход в систему не предусмотрен.

Поставим себе цель решить три задачи, чтобы максимально ускорить запуск терминала:

* Сохраним пароль через ярлык для запуска
* Сделаем автоматический вход
* Отключим проверку обновлений

![&#x42D;&#x442;&#x43E; &#x43E;&#x43A;&#x43D;&#x43E; &#x43E;&#x442;&#x43A;&#x43B;&#x44E;&#x447;&#x438;&#x43C;](../.gitbook/assets/image%20%2858%29.png)

## Подготовка рабочего места

Для решения вышеперечисленных задач нужны дополнительные программы. Чтобы поменять логику работы программы так, как нам потребно, понадобится внести изменения в исполнимый файл. При этом доступа к исходному коду Авроры у нас нет.

Для исследования и модификации внутренностей приложений без исходного кода применяются [отладчики ](https://ru.wikipedia.org/wiki/%D0%9E%D1%82%D0%BB%D0%B0%D0%B4%D1%87%D0%B8%D0%BA)и [декомпиляторы](https://ru.wikipedia.org/wiki/%D0%94%D0%B5%D0%BA%D0%BE%D0%BC%D0%BF%D0%B8%D0%BB%D1%8F%D1%82%D0%BE%D1%80). Такого рода действия называются **обратной разработкой** \(reverse engineering, RE\).

{% hint style="info" %}
**Декомпилятор** выдает примерный эквивалент исходного кода программы на её оригинальном языке программирования, **отладчик** позволяет выполнять программу пошагово, изучая содержимое памяти и принципы работы.
{% endhint %}

Поскольку программы пишутся на разных языках и технологиях, декомпилятор и отладчик надо подбирать под конкретную задачу.

### Сбор информации о цели

Обратную разработку стоит начинать с "визуального осмотра": нужно собрать данные об исследуемом процессе. Если вы устанавливали Аврору в папку по умолчанию, то это будет **Program Files \(x86\)\UnitedTraders\Aurora**. Символы x86 в пути означают, что исполняемый файл **Terminal.exe** терминала **32-битный**. Разрядность \(32 или 64\) иногда требуется, чтобы правильно выбрать декомпилятор \(по ходу чтения материала этот момент прояснится\). 

![&#x418;&#x441;&#x43F;&#x43E;&#x43B;&#x43D;&#x438;&#x43C;&#x44B;&#x439; &#x444;&#x430;&#x439;&#x43B; &#x410;&#x432;&#x440;&#x43E;&#x440;&#x44B; &#x43D;&#x430;&#x437;&#x44B;&#x432;&#x430;&#x435;&#x442;&#x441;&#x44F; Terminal.exe](../.gitbook/assets/image%20%2849%29.png)

Есть и другой способ определить разрядность программы. Для этого можно воспользоваться Диспетчером задач, перейдя на вкладку "**Подробности**", где выбрать столбец "**Платформа**" в таблице. Чтобы выбирать столбцы, нужно нажать правой кнопкой мыши по заголовку таблицы со списком запущенных процессов, далее появится нужный пункт контекстного меню.

![&#x412;&#x44B;&#x431;&#x43E;&#x440; &#x441;&#x442;&#x43E;&#x43B;&#x431;&#x446;&#x43E;&#x432;](../.gitbook/assets/image%20%2841%29.png)

Мы пойдём еще дальше, воспользуемся более продвинутой утилитой просмотра списка процессов - ****[**Process Explorer**](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer). Скачайте архив с программой, распакуйте в любую директорию, запустите файл **procexp.exe** или **procexp64.exe** \(если у вас 64-битная Windows\). При первом запуске будет предложено принять лицензионное соглашение; выберите **Agree**.

Сразу выполним настройку.

> **View ➡️ Select Columns... ➡️ Image Type \(64 vs 32-bit\)**
>
> Установите флаг, чтобы в таблице видеть разрядность запущенных программ

![&#x412;&#x44B;&#x431;&#x43E;&#x440; &#x441;&#x442;&#x43E;&#x43B;&#x431;&#x446;&#x43E;&#x432; &#x434;&#x43B;&#x44F; &#x43E;&#x442;&#x43E;&#x431;&#x440;&#x430;&#x436;&#x435;&#x43D;&#x438;&#x44F;](../.gitbook/assets/image%20%2852%29.png)

Далее включим выделение разных типов процессов цветом.

> **Options ➡️ Configure Colors...**
>
> Установите все флаги, кроме Relocated DLLs \(нас не интересуют\)

![&#x41D;&#x430;&#x441;&#x442;&#x440;&#x43E;&#x439;&#x43A;&#x430; &#x446;&#x432;&#x435;&#x442;&#x43E;&#x432;](../.gitbook/assets/image%20%2854%29.png)

Посмотрим, каким цветом выделился процесс Авроры. На моей системе это выглядит следующим образом:

![&#x422;&#x430;&#x431;&#x43B;&#x438;&#x446;&#x430; &#x43F;&#x440;&#x43E;&#x446;&#x435;&#x441;&#x441;&#x43E;&#x432; &#x432; Process Explorer](../.gitbook/assets/image%20%2833%29.png)

Делаем вывод, что Аврора - это **.NET Process**. Это означает, что терминал был скомпилирован для платформы [.NET](https://ru.wikipedia.org/wiki/.NET_Framework) от Microsoft.

{% hint style="info" %}
Процесс вместо жёлтого может быть приоритетнее подсвечен коричневым цветом \(Jobs\).
{% endhint %}

### Декомпиляция

{% hint style="danger" %}
Закройте Аврору на период декомпиляции, если она была запущена ранее.
{% endhint %}

Для .NET созданы различные декомпиляторы, есть платные и бесплатные. Например, **dotPeek** от **JetBrains**:

{% embed url="https://www.jetbrains.com/ru-ru/decompiler/" %}

Но нам требуется не просто исследовать внутренности терминала, а также изменить их. При использовании **dotPeek** это достигается через экспорт декомпилированного кода в проект для [**Visual Studio**](https://visualstudio.microsoft.com/ru/) \(среда разработки от Microsoft\); в проект вносятся необходимые изменения; проект компилируется заново. 

Для нашей задачи этот путь потребует серьёзных усилий. Поступим проще и воспользуемся другим бесплатным декомпилятором с открытым кодом: **dnSpy,** который умеет модифицировать код для .NET через свой графический интерфейс.

{% embed url="https://github.com/dnSpy/dnSpy/releases" %}

Скачиваем архив по ссылке, распаковываем в любую директорию, как обычно. Какой конкретно файл скачивать? Тут нам пригодятся ранее полученные знания о разрядности Авроры \(32 бита\). Скачиваем файл **dnSpy-net-win32.zip**

![&#x412;&#x430;&#x440;&#x438;&#x430;&#x43D;&#x442;&#x44B; &#x434;&#x43B;&#x44F; &#x437;&#x430;&#x433;&#x440;&#x443;&#x437;&#x43A;&#x438;](../.gitbook/assets/image%20%2847%29.png)

После распаковки запускаем приложение **dnSpy.exe с правами администратора**:

![&#x413;&#x43B;&#x430;&#x432;&#x43D;&#x43E;&#x435; &#x43E;&#x43A;&#x43D;&#x43E; dnSpy](../.gitbook/assets/image%20%2855%29.png)

Из меню **Файл** нажимаем **Открыть...**, после чего в диалоговом окне выбираем файл **C:\Program Files \(x86\)\UnitedTraders\Aurora\Terminal.exe**

![&#x422;&#x435;&#x440;&#x43C;&#x438;&#x43D;&#x430;&#x43B; &#x437;&#x430;&#x433;&#x440;&#x443;&#x436;&#x435;&#x43D; &#x432; &#x434;&#x435;&#x43A;&#x43E;&#x43C;&#x43F;&#x438;&#x43B;&#x44F;&#x442;&#x43E;&#x440;](../.gitbook/assets/image%20%2838%29.png)

### Поиск места с проверкой обновлений

В левом навигационном дереве разверните узел **Terminal**, а затем **Terminal.exe**. Появится длинный список т.н. **пространств имён** \(namespaces, NS\), которые используются в том числе для группировки кода в программе. Здесь наш друг - знание английского языка. Беглый просмотр позволяет прикинуть, для чего могут быть нужны некоторые пространства. Например, пространство **Aurora.ClientTerminal.Part.Clock** наверняка отвечает за логику работы часов в Авроре.

{% hint style="info" %}
Пространства имён в дальнейшем для краткости будем обозначать как ПИ.
{% endhint %}

Где искать нужный код? Мы знаем, что проверка обновлений происходит в самом начале запуска Авроры, а значит, нужно **осмотреть** места, где программа только-только запускается. Да-да, именно осмотреть. В любой незнакомой программе нужно с чего-то начинать, и хорошая отправная точка для старта - "начало" программы.

Для программ на C\# началом является функция **Main**, ****завёрнутая в класс **Program**:

```csharp
using System;

namespace HelloWorld
{
  class Program
  {
    static void Main(string[] args)
    {
      Console.WriteLine("Hello World!");    
    }
  }
}
```

{% hint style="info" %}
[Класс ](https://ru.wikipedia.org/wiki/%D0%9A%D0%BB%D0%B0%D1%81%D1%81_%28%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%29)- это специальным образом оформленный код, в котором функции и переменные связаны неким общим смыслом. Аврора делится на сотни классов, каждый отвечает за свою часть общей логики. Стакан, лента сделок, часы, профиль пользователя и т.д. - всё это смоделировано классами, большими и маленькими.
{% endhint %}

Класс **Program** обычно находится в самом первом ПИ, если смотреть в дерево **dnSpy**. Найдите и откройте его. В появившемся поддереве найдите функцию **Main**.

![&#x414;&#x435;&#x43A;&#x43E;&#x43C;&#x43F;&#x438;&#x43B;&#x438;&#x440;&#x43E;&#x432;&#x430;&#x43D;&#x43D;&#x430;&#x44F; &#x444;&#x443;&#x43D;&#x43A;&#x446;&#x438;&#x44F; Main](../.gitbook/assets/image%20%2834%29.png)

В 31 строке на снимке экрана виден текст "**Disable updates check**". Скорее всего это то, что нам нужно. Далее вы должны посмотреть на контекст, в котором появляется этот текст. Видно, что он фигурирует в методе **Add** объекта типа **OptionSet**. Нажмите на слово **OptionSet**, которое выделено цветом в коде программы \(не optionSet, регистр символов важен\). Мы попадём в ПИ под названием **NDesk.Options**.

{% hint style="info" %}
**Метод** - это функция, объявленная \(прописанная\) внутри класса.
{% endhint %}

Обратившись к документации поиском на просторах сети, выясняем, что тип **OptionSet** нужен для работы с параметрами командной строки программ**.** 

{% embed url="http://www.ndesk.org/doc/ndesk-options/NDesk.Options/OptionSet.html" %}

При этом метод **Add** принимает два параметра. Первый - это непосредственно имя аргумента командной строки, второй - описание аргумента на понятном человеку языке.

В нашем случае аргумент командной строки - это **noupdate**. Чтобы вернуться обратно, нажмите **Backspace**.

Тут же открываем ярлык Авроры \(**создайте его для файла Terminal.exe предварительно самостоятельно**\) и пробуем добавить аргумент к строке запуска:

{% hint style="info" %}
Аргумент командой строки **noupdate** нужно вводить, предварив двумя дефисами --

Если поставить один дефис, то интерпретатор в общем случае разделит текст **noupdate** на 8 однобуквенных аргументов \(n, o, u ,p, d, a, t, e\).

В ряде случаев можно и вовсе не ставить дефисы, но я не рекомендую этого делать здесь по соображениям переносимости кода на другие системы и оболочки.
{% endhint %}

![&#x414;&#x43E;&#x431;&#x430;&#x432;&#x43B;&#x435;&#x43D;&#x438;&#x44F; &#x430;&#x440;&#x433;&#x443;&#x43C;&#x435;&#x43D;&#x442;&#x430; &#x43A;&#x43E;&#x43C;&#x430;&#x43D;&#x434;&#x43D;&#x43E;&#x439; &#x441;&#x442;&#x440;&#x43E;&#x43A;&#x438;](../.gitbook/assets/image%20%2853%29.png)

Запускайте Аврору и наблюдайте. Если всё сделано верно, обновления проверяться не будут, терминал запустится немного быстрее.

### Сохранение пароля

Не выходя из кода функции **Main** немного ниже в 51-й строке видим потенциально интересный нам код:

```csharp
optionSet.Add(PasswordCommandLineParameter.Name, 
  "Application restart parameter. Do not use it manually", 
  delegate(string v)
{
	PasswordCommandLineParameter.EncryptedPassword = v;
});
```

Здесь аналогично используется метод **Add** для экземпляра объекта типа **OptionSet**, но первый параметр теперь не текстовый литерал \(как было раньше\), а свойство другого типа из другого ПИ \(**Aurora.ClientTerminal.Framework.Program.Restart**\): 

```csharp
PasswordCommandLineParameter.Name
```

{% hint style="warning" %}
Нам говорят, что не стоит использовать параметр вручную, т.к. он применяется во внутренних задачах Авроры:

```csharp
"Application restart parameter. Do not use it manually"
```

Судя по ПИ под названием **Aurora.ClientTerminal.Framework.Program.Restart**, параметр 

```csharp
PasswordCommandLineParameter.Name
```

 нужен для передачи пароля при каких-то технических перезапусках Авроры.

Тем не менее, мы попробуем решить свою задачу именно таким способом, ведь никто не запрещает экспериментировать.
{% endhint %}

Нажав на **Name** в редакторе, попадаем в новый листинг \(у вас могут отличаться закомментированные значения - это не принципиально\):

```csharp
using System;

namespace Aurora.ClientTerminal.Framework.Program.Restart
{
	// Token: 0x0200098F RID: 2447
	public static class PasswordCommandLineParameter
	{
		// Token: 0x17001403 RID: 5123
		// (get) Token: 0x060048C7 RID: 18631 RVA: 0x000B480C File Offset: 0x000B2A0C
		// (set) Token: 0x060048C8 RID: 18632 RVA: 0x000B4814 File Offset: 0x000B2A14
		public static string EncryptedPassword { get; set; }

		// Token: 0x04001D9F RID: 7583
		public static string Name = "key=";
	}
}

```

Получается, что ключ аргумента командной строки тут называется **key** \(знак равенства в коде в строке 14 означает, что аргумент ожидает значения; в прошлый раз сам факт наличия аргумента **noupdate** был достаточен\), а также есть свойство **EncryptedPassword** с каким-то зашифрованным паролем.

Нажмите правой кнопкой на **PasswordCommandLineParameter**, а в открывшемся большом контекстном меню выберите пункт **Анализировать** \(Ctrl+Shift+R\). Это действие найдёт все известные места, где использовался класс **PasswordCommandLineParameter**:

![&#x410;&#x43D;&#x430;&#x43B;&#x438;&#x437; &#x438;&#x441;&#x43F;&#x43E;&#x43B;&#x44C;&#x437;&#x43E;&#x432;&#x430;&#x43D;&#x438;&#x44F; &#x43A;&#x43B;&#x430;&#x441;&#x441;&#x430; PasswordCommandLineParameter](../.gitbook/assets/image%20%2860%29.png)

Нижние два использования в **Program.Main** мы уже видели, знаем о них, а значит, пропускаем. Собственно, с них мы и начали проводить дальнейший анализ. Остаются три места, первое из которых менее интересно, нежели другие:

* **OfferRestart** скорее всего отвечает за передачу пароля в зашифрованном виде при перезапуске. Если мы хотим сохранить пароль, то нас этот метод особо не интересует.
* Использование в пространствах имен **LoginControl** и **LoginForm** интереснее - наверняка там пароль будет расшифрован, чтобы можно было передать его на сервер авторизации в исходном виде. Посмотрим первый метод-конструктор **LoginWpfView**\(\):

```csharp
...

namespace Aurora.ClientTerminal.Part.LoginControl
{
	// Token: 0x02000543 RID: 1347
	public class LoginWpfView : System.Windows.Controls.UserControl, IComponentConnector, ILoginControlView
	{
		// Token: 0x06002CC4 RID: 11460 RVA: 0x00069288 File Offset: 0x00067488
		public LoginWpfView()
		{
			this.restorePasswordCommand = new SimpleDelegateCommand(new CommandExecuteHandler(this.ExecuteRestorePasswordCommand), 
			  null);
			this.InitializeComponent();
			
			// Логика срабатывает только при непустом пароле в аргументах командной строки
			if (!string.IsNullOrEmpty(PasswordCommandLineParameter.EncryptedPassword))
			{
			  // TxtPass - наверняка поле ввода пароля. То, что нам нужно!
				this.TxtPass.Password = CryptoHelper.Decrypt(PasswordCommandLineParameter.EncryptedPassword);
			}
		}
		
		...
```

Тут происходит проверка \(строка 16\) на непустое значение свойства **PasswordCommandLineParameter.EncryptedPassword**.  Сразу цепляемся за метод **CryptoHelper.Decrypt** - очевидно, он ответственен за расшифровку пароля \(переходим к коду нажатием левой кнопки мыши\), если пароль был задан в аргументах командной строки:

```csharp
...

namespace Aurora.ClientTerminal.Framework.Program.Restart
{
	// Token: 0x0200098E RID: 2446
	public static class CryptoHelper
	{
		// Token: 0x060048C4 RID: 18628 RVA: 0x000B46E4 File Offset: 0x000B28E4
		public static string Encrypt(string value)
		{
			string result = string.Empty;
			try
			{
				byte[] bytes = Encoding.UTF8.GetBytes(value);
				ICryptoTransform transform = Rijndael.Create().CreateEncryptor(new PasswordDeriveBytes(CryptoHelper._key, null).GetBytes(16), new byte[16]);
				MemoryStream memoryStream = new MemoryStream();
				CryptoStream cryptoStream = new CryptoStream(memoryStream, transform, CryptoStreamMode.Write);
				cryptoStream.Write(bytes, 0, bytes.Length);
				cryptoStream.FlushFinalBlock();
				result = Convert.ToBase64String(memoryStream.ToArray());
			}
			catch (Exception exception)
			{
				CryptoHelper.Logger.Error(exception, "Error while encryption!");
			}
			return result;
		}
		
		public static string Decrypt(string encrypted)
		{
			string result = string.Empty;
			try
			{
			  // Шифр Рейндал ? Определённо...
				ICryptoTransform transform = Rijndael.Create().CreateDecryptor(new PasswordDeriveBytes(CryptoHelper._key, null).GetBytes(16), new byte[16]);
				result = new StreamReader(new CryptoStream(new MemoryStream(Convert.FromBase64String(encrypted)), transform, CryptoStreamMode.Read)).ReadToEnd();
			}
			catch (Exception exception)
			{
				CryptoHelper.Logger.Error(exception, "Error while login and password decryption!");
			}
			return result;
		}
		
		
		// Token: 0x04001D9D RID: 7581
		private static string _key = "SDfadfb1345hfag9";
		
		...
```

В коде \(строка 35 листинга\) видим слово **Rijndael**; поиск в сети говорит, что это шифр [Рейндал](https://ru.wikipedia.org/wiki/Advanced_Encryption_Standard). В качестве ключа шифрования используется неизменяемый литерал \(строка 47 листинга\):

```csharp
SDfadfb1345hfag9
```

А метод **Encrypt** наверняка отвечает за шифрование пароля. Можно сразу попробовать зашифровать пароль, передать его аргументом командной строки **key** и посмотреть на реакцию Авроры.

Чтобы выполнить код шифрования, не нужно устанавливать никаких дополнительных средств разработки. Просто зайдите на сайт онлайн-компилятора C\# **.NET** **Fiddle**:

{% embed url="https://dotnetfiddle.net/" %}

{% hint style="info" %}
В криптографии тоже разбираться не обязательно. Код уже есть, нужно его запустить, подставив свои значения.
{% endhint %}

Вставьте следующий адаптированный код метода **Encrypt**, а вместо **YourPassword** впишите свой пароль от Авроры:

```csharp
using System;
using System.Security.Cryptography;
using System.Text;
using System.IO;
					
public class Program
{
	public static void Main()
	{
		string result = string.Empty;
		byte[] bytes = Encoding.UTF8.GetBytes("YourPassword");
		ICryptoTransform transform = Rijndael.Create().CreateEncryptor(new PasswordDeriveBytes("SDfadfb1345hfag9", null).GetBytes(16), 
		  new byte[16]);
		MemoryStream memoryStream = new MemoryStream();
		CryptoStream cryptoStream = new CryptoStream(memoryStream, transform, CryptoStreamMode.Write);
		cryptoStream.Write(bytes, 0, bytes.Length);
		cryptoStream.FlushFinalBlock();
            
		Console.WriteLine(Convert.ToBase64String(memoryStream.ToArray()));
	}
}
```

{% hint style="danger" %}
Если переживаете за утечку пароля, установите [Visual Studio](https://visualstudio.microsoft.com/ru/) и скомпилируйте программу самостоятельно локально. Картинка для создания проекта ниже:
{% endhint %}

![&#x415;&#x441;&#x43B;&#x438; &#x445;&#x43E;&#x442;&#x438;&#x442;&#x435; &#x441;&#x430;&#x43C;&#x43E;&#x441;&#x442;&#x43E;&#x44F;&#x442;&#x435;&#x43B;&#x44C;&#x43D;&#x43E; &#x441;&#x43A;&#x43E;&#x43C;&#x43F;&#x438;&#x43B;&#x438;&#x440;&#x43E;&#x432;&#x430;&#x442;&#x44C; &#x448;&#x438;&#x444;&#x440;&#x43E;&#x432;&#x430;&#x43B;&#x44C;&#x449;&#x438;&#x43A; &#x43F;&#x430;&#x440;&#x43E;&#x43B;&#x435;&#x439; &#x434;&#x43B;&#x44F; &#x410;&#x432;&#x440;&#x43E;&#x440;&#x44B;](../.gitbook/assets/image%20%2827%29.png)

На сайте **.NET** **Fiddle в** верхней панели нажмите **Run** и скопируйте получившийся шифр.

{% hint style="info" %}
Для пароля **YourPassword** это будет **pfFCrLy84Hsx46KpIjx5bg==**
{% endhint %}

Пробуем в бою, запускаем терминал:

![&#x414;&#x43E;&#x431;&#x430;&#x432;&#x43B;&#x44F;&#x435;&#x43C; &#x437;&#x430;&#x448;&#x438;&#x444;&#x440;&#x43E;&#x432;&#x430;&#x43D;&#x43D;&#x44B;&#x439; &#x43F;&#x430;&#x440;&#x43E;&#x43B;&#x44C; &#x432; &#x430;&#x440;&#x433;&#x443;&#x43C;&#x435;&#x43D;&#x442; --key](../.gitbook/assets/image%20%2856%29.png)

Но случается беда - теперь Аврора вовсе не загружается. Не зря же нам говорили, "**Application restart parameter. Do not use it manually**". Нужно понять причину "вылета" программы. 

Переходим ко второму использованию класса **PasswordCommandLineParameter**, на этот раз в метод **ValidateData** класса **LoginPresenter**:

```csharp
...

public LoginResult ValidateData(string password, bool needAuth)
{
	if (!needAuth)
	{
		return LoginResult.Succeded;
	}
	int port = int.Parse(this.model.ServerPort);
	LocalizedString localizedString = this.serversMultiplexor.SetAuthenticationServer(this.model.ServerName, port);
	if (localizedString != null)
	{
		this.controlView.ShowErrors(Aurora.ClientTerminal.Framework.Strings.TMessage(localizedString));
		return LoginResult.Error;
	}
	LoginResult loginResult = this.Authorize(password);
	if (loginResult != LoginResult.Succeded)
	{
		return loginResult;
	}
	string text;
	try
	{
		text = this.currentUserService.FindLoginSync(new TimeSpan?(TimeSpan.FromSeconds(20.0)));
	}
	catch (InvalidOperationException)
	{
		string errorDescription = this.GetErrorDescription(AuthenticationResult.NoAuthResponseReceived);
		this.controlView.ShowErrors(errorDescription);
		return LoginResult.Error;
	}
	if (text == null)
	{
		LoginPresenter.logger.Error("Something went wrong - {0} does not even know that we tried to log in", "CurrentUserService");
		string errorDescription2 = this.GetErrorDescription(AuthenticationResult.NoAuthResponseReceived);
		this.controlView.ShowErrors(errorDescription2);
		return LoginResult.Error;
	}
	if (text.ToLower() != this.model.UserName.ToLower())
	{
		LoginPresenter.logger.Error<string, string>("Server returned '{0}' but we requested '{1}'", text, this.model.UserName);
		string errorDescription3 = this.GetErrorDescription(AuthenticationResult.NoAuthResponseReceived);
		this.controlView.ShowErrors(errorDescription3);
		return LoginResult.Error;
	}
	this.settingsApplier.Settings.LastLogin = this.model.UserName;
	this.settingsApplier.Settings.Port = this.model.ServerPort;
	if (this.overriddenServerName == null)
	{
		this.settingsApplier.Settings.Host = this.model.ServerName;
	}
	this.settingsApplier.Settings.Simulation = this.model.IsSimulationMode;
	
	// Просто сохранение пароля в свойство, а больше ничего и нет :(
	PasswordCommandLineParameter.EncryptedPassword = CryptoHelper.Encrypt(password);
	return LoginResult.Succeded;
}

...

```

Ничего интересного и потенциально проблемного. Введённый пользователем пароль шифруется и запоминается в свойство **PasswordCommandLineParameter.EncryptedPassword** до востребования \(55 строка листинга\). Едва ли этот код может привести к сбою.

### Прибегаем к помощи отладчика

Очевидные варианты поиска проблемы кончились, а нужно двигаться дальше.  Отладчик может помочь понять, какие участки кода выполняются, а какие остаются в "мертвой зоне". Таким образом можно сузить область поиска и найти проблемный код. Также можно будет исследовать содержимое памяти программы по мере её выполнения.

Раз уж использование пароля в **ValidateData** не представляет интереса, вернёмся в метод-конструктор **LoginWpfView\(\)** и поставим \(нажмите левой кнопкой в том месте, где видите красный кружок на снимке экрана снизу\) _**точку останова**_ на строке, где происходит запись расшифрованного пароля в поле ввода:

![&#x423;&#x441;&#x442;&#x430;&#x43D;&#x430;&#x432;&#x43B;&#x438;&#x432;&#x430;&#x435;&#x43C; breakpoint](../.gitbook/assets/image%20%2826%29.png)

{% hint style="info" %}
Точка останова \(breakpoint\) - пометка для отладчика, что при её достижении выполнение программы нужно остановить \(заморозить\). Обозначается визуально красным кружком 🔴

Когда программа остановлена, разработчик может изучать содержимое памяти, модифицировать код, выполнять программу шаг за шагом.
{% endhint %}

Теперь запустим терминал в режиме отладки. Это можно сделать, нажав **F5.** Либо через панель инструментов **dnSpy** \(кнопка "**Старт**"\). Появится окно ввода параметров запуска, **не забудьте указать аргументы командной строки**:

![&#x41F;&#x430;&#x440;&#x430;&#x43C;&#x435;&#x442;&#x440;&#x44B; &#x437;&#x430;&#x43F;&#x443;&#x441;&#x43A;&#x430; &#x434;&#x43B;&#x44F; &#x43E;&#x442;&#x43B;&#x430;&#x434;&#x43A;&#x438;](../.gitbook/assets/image%20%2829%29.png)

{% hint style="info" %}
Для копирования:

**--noupdate --key=pfFCrLy84Hsx46KpIjx5bg==**
{% endhint %}

Нажимаем ОК, ждём срабатывания точки останова \(терминал будет загружаться несколько дольше обычного, паниковать не стоит\). Сработавший breakpoint подсвечивает строку желтым выделением, а в красном кружке появляется стрелка вправо, обозначающая текущую инструкцию программы, которая ожидает своего выполнения.

Чтобы выполнить строку с кодом, не заходя во внутрь промежуточных методов, нажимаем **F10**. И тут нас сразу перекидывает в следующий код:

```csharp
...
				
		finally
		{
			if (activationAttributes != null)
			{
				ActivationServices.PopActivationAttributes(this);
				activationAttributes = null;
			}
		}
	}
	// Этот блок "ловит" ошибки в методе, Exception значит исключение
	catch (Exception)
	{
		throw;
	}
	return result;
}

...
```

{% hint style="info" %}
Чтобы выполнять код, в том числе заходя внутрь в промежуточные методы, нужно нажимать **F11** или эквивалентную кнопку на панели инструментов.
{% endhint %}

Явно не то, что мы ожидали. Нажимаем **F10** еще два раза. В итоге попадаем в блок **catch**, который обрабатывает исключительные \(ошибочные\) ситуации.

{% hint style="info" %}
**Исключения \(exceptions\)** - это ошибки времени выполнения и другие возможные проблемы, которые могут возникнуть при выполнении программы и приводят к невозможности \(бессмысленности\) дальнейшей отработки программой её базового алгоритма.
{% endhint %}

Смотрим в локальные переменные:

![&#x41F;&#x43E;&#x439;&#x43C;&#x430;&#x43B;&#x438; &#x438;&#x441;&#x43A;&#x43B;&#x44E;&#x447;&#x435;&#x43D;&#x438;&#x435; &#x432; &#x43F;&#x440;&#x43E;&#x433;&#x440;&#x430;&#x43C;&#x43C;&#x435;](../.gitbook/assets/image%20%2842%29.png)

Видим переменную **$exception** с подробностями ошибки. Наводим курсор на её значение, смотрим на всплывающую подсказку:

![&#x41F;&#x43E;&#x434;&#x440;&#x43E;&#x431;&#x43D;&#x43E;&#x441;&#x442;&#x438; &#x438;&#x441;&#x43A;&#x43B;&#x44E;&#x447;&#x435;&#x43D;&#x438;&#x44F;](../.gitbook/assets/image%20%2857%29.png)

В появившихся подробностях показана цепочка \([стек](https://ru.wikipedia.org/wiki/%D0%A1%D1%82%D0%B5%D0%BA_%D0%B2%D1%8B%D0%B7%D0%BE%D0%B2%D0%BE%D0%B2)\) вызовов методов, которая привела к ошибке:

1. **ValidateAllFields** - тут ошибка, он последний в цепочке
2. **TxtPassPasswordChanged** - по названию становится ясно, что этот метод вызывается, если содержимое поля ввода с паролем меняется
3. Уже не важно, что было тут...

Действительно, когда при старте программы идёт попытка записать расшифрованный пароль в поле ввода, то случаются **побочные эффекты**: срабатывает связанный код \(мы узнали о нём по факту, когда отлаживали программу\), отслеживающий все изменения в поле ввода. В нашем случае этот код вызывает метод **ValidateAllFields**, который далее "вываливается" с ошибкой. 

Посмотрим теперь предметно на проблемный код  \(попасть в код можно через дерево методов класса **LoginWpfView**\):

```csharp
// Aurora.ClientTerminal.Part.LoginControl.LoginWpfView
private void ValidateAllFields()
{
	LoginValidationRule loginValidationRule = new LoginValidationRule
	{
		CType = ContentType.NumbersAndLetters
	};
	ValidationResult validationResult = loginValidationRule.Validate(this.TxtAccount.Text, null);
	loginValidationRule.CType = ContentType.Anything;
	ValidationResult validationResult2 = loginValidationRule.Validate(this.TxtPass.Password, null);
	ValidationResult validationResult3 = loginValidationRule.Validate(this.TxtServerName.Text, null);
	loginValidationRule.CType = ContentType.OnlyNumbers;
	ValidationResult validationResult4 = loginValidationRule.Validate(this.TxtServerPort.Text, null);
	ValidationResult validationResult5 = new ValidationResult(true, null);
	if (this.model.IsTwoSteps)
	{
		validationResult5 = loginValidationRule.Validate(this.TxtCode.Text, null);
	}
	if (validationResult.IsValid && validationResult2.IsValid && validationResult3.IsValid && validationResult4.IsValid && validationResult5.IsValid)
	{
		this.OkButton.IsEnabled = true;
		return;
	}
	this.OkButton.IsEnabled = false;
}

```

С виду код абсолютно нормальный, ничто не предвещает беды. Снова обратимся за помощью к отладчику. Поставим точку останова на первую строчку метода и перезапустим терминал \(**Ctrl+Shift+F5**\). Далее, когда при запуске сработает наша первая точка останова, продолжим выполнение программы, нажав **F5** ещё один раз. Теперь сработает вторая точка останова:

![&#x412;&#x442;&#x43E;&#x440;&#x430;&#x44F; &#x442;&#x43E;&#x447;&#x43A;&#x430; &#x43E;&#x441;&#x442;&#x430;&#x43D;&#x43E;&#x432;&#x430; &#x441;&#x440;&#x430;&#x431;&#x43E;&#x442;&#x430;&#x43B;](../.gitbook/assets/image%20%2832%29.png)

Если вы будете нажимать **F10**, чтобы идти дальше по коду, то возбуждение исключения произойдёт на строке, где проверяется свойство **this.model.IsTwoSteps**. В области с локальными переменными на снимке экрана выше разверните узел **this** и найдите там свойство **model**:

![&#x424;&#x440;&#x430;&#x433;&#x43C;&#x435;&#x43D;&#x442; &#x434;&#x435;&#x440;&#x435;&#x432;&#x430; &#x434;&#x43B;&#x44F; &#x443;&#x437;&#x43B;&#x430; this](../.gitbook/assets/image%20%2831%29.png)

Вот и проблема. Модель **model** \(что бы это ни значило\) не имеет заданного значения \(и равна **null**\). Попытка прочитать свойство **IsTwoSteps** через отсутствующее значение приводит к краху программы. Вот этот момент мы и поправим.

### Решение найденной проблемы

Проблема локализована, решение лежит на поверхности. Нужно дополнить проблемное условие еще одной проверкой, чтобы не обращаться к вложенному свойству **IsTwoSteps**, если родительское **this.model** не задано.

Было:

```csharp
	if (this.model.IsTwoSteps)
	{
		validationResult5 = loginValidationRule.Validate(this.TxtCode.Text, null);
	}
```

Станет после исправления:

```csharp
	if (this.model != null && this.model.IsTwoSteps)
	{
		validationResult5 = loginValidationRule.Validate(this.TxtCode.Text, null);
	}
```

Выключаем отладку \(Shift+F5\), снимаем точки останова, приступаем к правке метода **ValidateAllFields**.

{% hint style="danger" %}
**Настоятельно рекомендую** сохранить оригинальный файл **Terminal.exe** в отдельное место перед любыми правками. В таком случае всегда можно будет легко и быстро откатиться на заводскую версию.
{% endhint %}

Для изменения кода метода нажимаем **Ctrl+Shift+E**, предварительно нажав на имя метода левой кнопкой \(либо сделать то же самое из контекстного меню\). Дописываем правки и нажимаем "**Компилировать**":

![&#x41F;&#x440;&#x430;&#x432;&#x43A;&#x430; &#x43C;&#x435;&#x442;&#x43E;&#x434;&#x430;](../.gitbook/assets/image%20%2830%29.png)

Давайте теперь посмотрим код, который вызывал метод **ValidateAllFields**. Мы знаем по цепочке вызовов, которая привела к возбуждению исключения, что это обработчик события изменения пароля в поле ввода **TxtPassPasswordChanged**, также принадлежащий классу **LoginWpfView** в ПИ **Aurora.ClientTerminal.Part.LoginControl**:

```csharp
// Aurora.ClientTerminal.Part.LoginControl.LoginWpfView
private void TxtPassPasswordChanged(object sender, RoutedEventArgs e)
{
  // Этот метод подправили, проблем быть не должно
	this.ValidateAllFields();
	// А тут что? Стоит посмотреть...
	this.CollapseCodePanel();
}

```

По инерции посмотрим метод **CollapseCodePanel**:

```csharp
private void CollapseCodePanel()
{
  // Снова обращение к вложенному свойству "модели", на этот раз Code!
	this.Model.Code = null;
	this.TxtCode.Visibility = Visibility.Collapsed;
	this.LblCode.Visibility = Visibility.Collapsed;
}
```

Видим аналогичную ситуацию, требующую правки. Нажимаем Ctrl+Shift+E, вводим исправленный код:

```csharp
private void CollapseCodePanel()
{
  // Защитная проверка не даст завершиться терминалу аварийно
	if (this.Model != null)
	{
		this.Model.Code = null;
	}
	this.TxtCode.Visibility = Visibility.Collapsed;
	this.LblCode.Visibility = Visibility.Collapsed;
}
```

Теперь сохраняем изменения в исполнимом файле терминала, выбрав пункт главного меню "**Файл**" **➡️** "**Сохранить модуль...**":

![&#x41C;&#x435;&#x43D;&#x44E; &quot;&#x424;&#x430;&#x439;&#x43B;&quot;](../.gitbook/assets/image%20%2844%29.png)

Нажимаем ОК:

![&#x421;&#x43E;&#x445;&#x440;&#x430;&#x43D;&#x435;&#x43D;&#x438;&#x435; &#x43F;&#x440;&#x430;&#x432;&#x43E;&#x43A;](../.gitbook/assets/image%20%2835%29.png)

Если сохранение не удалось, то проблема заключается в отсутствии прав администратора на запись в **Program Files \(x86\)**. Нужно перезапустить **dnSpy** с правами администратора либо записать файл в другое место.

Если всё сделано верно, то теперь Аврора будет запускаться с "вписанным" паролем:

![&#x41F;&#x430;&#x440;&#x43E;&#x43B;&#x44C; &#x443;&#x441;&#x43F;&#x435;&#x448;&#x43D;&#x43E; &#x43F;&#x43E;&#x434;&#x441;&#x442;&#x430;&#x432;&#x43B;&#x44F;&#x435;&#x442;&#x441;&#x44F; &#x432; &#x43F;&#x43E;&#x43B;&#x435; &#x43F;&#x440;&#x438; &#x437;&#x430;&#x43F;&#x443;&#x441;&#x43A;&#x435;](../.gitbook/assets/image%20%2828%29.png)

### Автоматический вход в терминал

Логин в Авроре сохраняется "из коробки". А теперь мы научились сохранять и пароль. А что значит автоматический вход? 

Это значит, что нужно программно каждый раз нажимать кнопку "**Войти**" при запуске программы после подстановки пароля.

Если мы посмотрим на дерево методов всё того же класса **LoginWpfView**, то обнаружим подходящий по названию: **OkButtonClick**:

![&#x41C;&#x435;&#x442;&#x43E;&#x434;, &#x43F;&#x43E;&#x442;&#x435;&#x43D;&#x446;&#x438;&#x430;&#x43B;&#x44C;&#x43D;&#x43E; &#x43E;&#x442;&#x432;&#x435;&#x447;&#x430;&#x44E;&#x449;&#x438;&#x439; &#x437;&#x430; &#x43A;&#x43E;&#x434; &#x43A;&#x43D;&#x43E;&#x43F;&#x43A;&#x438; &#x412;&#x43E;&#x439;&#x442;&#x438;](../.gitbook/assets/image%20%2837%29.png)

Можно сразу попробовать вызвать этот метод в нужный нам момент, проверить реакцию терминала, а далее смотреть по ситуации. Давайте посмотрим на код **OkButtonClick**:

```csharp
// Aurora.ClientTerminal.Part.LoginControl.LoginWpfView
private void OkButtonClick(object sender, RoutedEventArgs e)
{
	base.Cursor = Cursors.Wait;
	try
	{
		LoginResult loginResult = this.observer.ValidateData(this.TxtPass.Password, true);
		if (loginResult == LoginResult.Succeded)
		{
			Action loginClicked = this.LoginClicked;
			if (loginClicked != null)
			{
				loginClicked();
			}
		}
		else if (loginResult == LoginResult.TwoSteps)
		{
			this.ShowCodePanel();
		}
		else
		{
			this.CollapseCodePanel();
		}
	}
	finally
	{
		base.Cursor = Cursors.Arrow;
	}
}

```

Метод принимает два параметра: **sender** и **e**. Оба не используются в теле, а значит, при вызове их можно передавать **пустыми**.

Куда вставить вызов метода? Попробуем снова найти в дереве подходящего кандидата:

![&#x412;&#x44B;&#x431;&#x438;&#x440;&#x430;&#x435;&#x43C; &#x43C;&#x435;&#x442;&#x43E;&#x434;, &#x433;&#x434;&#x435; &#x43F;&#x440;&#x43E;&#x433;&#x440;&#x430;&#x43C;&#x43C;&#x43D;&#x43E; &#x43D;&#x430;&#x436;&#x43C;&#x451;&#x43C; &#x43A;&#x43D;&#x43E;&#x43F;&#x43A;&#x443; &quot;&#x412;&#x43E;&#x439;&#x442;&#x438;&quot;](../.gitbook/assets/image%20%2843%29.png)

Судя по названию, метод **OnShown** вызывается тогда, когда форма ввода логина и пароля "показывается" на экране. Открываем код:

```csharp
// Aurora.ClientTerminal.Part.LoginControl.LoginWpfView
public void OnShown()
{
	if (!string.IsNullOrEmpty(this.TxtAccount.Text))
	{
	  // Фокус ввода ставится на пароль, если Логин заполнен
		this.TxtPass.Focus();
		return;
	}
	// Если Логин не заполнен, он получает фокус ввода
	this.TxtAccount.Focus();
}
```

Обычная работа с полями ввода логина и пароля. То, что нам нужно. Нажимаем Ctrl+Shift+E, вносим правки:

```csharp
public void OnShown()
{
	if (!string.IsNullOrEmpty(this.TxtAccount.Text))
	{
		this.TxtPass.Focus();
		this.OkButtonClick(null, null);
		return;
	}
	this.TxtAccount.Focus();
}
```

В нашей версии автоматический вход возможен только при наличии сохранённого логина Авроры. Если же пароль не будет задан, попытка входа все равно произойдёт, но закончится ошибкой авторизации.

Сохраняем модуль, проверяем работу терминала.

✅ Если всё сделано верно, то теперь вход в Аврору будет полностью автоматическим.

P. S. Если после правок **Windows SmartScreen** ругается на сомнительное происхождение файла, выполните разблокировку, поставив флаг в свойствах EXE-файла \(не ярлыка!\) **Terminal.exe**:

![&#x420;&#x430;&#x437;&#x431;&#x43B;&#x43E;&#x43A;&#x438;&#x440;&#x43E;&#x432;&#x43A;&#x430; &#x438;&#x441;&#x43F;&#x43E;&#x43B;&#x43D;&#x438;&#x43C;&#x43E;&#x433;&#x43E; &#x43C;&#x43E;&#x434;&#x443;&#x43B;&#x44F;](../.gitbook/assets/image%20%2850%29.png)

