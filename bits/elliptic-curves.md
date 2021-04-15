# 🖍 Эллиптические кривые

Эллиптические кривые используются повсеместно в современных криптосистемах. Например, в криптографии Bitcoin используется кривая [secp256k1](https://en.bitcoin.it/wiki/Secp256k1).

Нас будет интересовать применение эллиптических кривых в конкурсах формата [CTF](https://en.wikipedia.org/wiki/Capture_the_flag#Computer_security), где криптосистема содержит уязвимости, а успех участника соревнования зависит от того, сможет ли он найти и правильно эксплуатировать эту брешь в защите.

{% hint style="info" %}
Криптосистема — это реализация криптографических методов и сопутствующей им инфраструктуры для предоставления услуг информационной безопасности. Криптосистема также называется системой **шифрования**.
{% endhint %}

## Теория, потребная для понимания и решения задач

### Конечные поля

🖍

### Модульная арифметика

🖍

### Эллиптические кривые

🖍

### Форматы ключей

🖍

## Атаки на криптосистемы с эллиптическими кривыми

### Подготовка рабочего места

Для работы понадобится платформа [Node.js](../wastelands/getting-started.md#setting-up-node-js) и язык [Python](../marshes/tg-button-autoclicking.md#installing-python).

Полезно будет иметь под рукой математическое программное обеспечение [SageMath](https://www.sagemath.org/download.html). Если нет возможности установить SageMath, можно пользоваться онлайн-ячейкой для вычислений:

{% embed url="https://sagecell.sagemath.org/" %}

## Задача №1. Invalid curve attack

Invalid curve attack - атака с использованием неправильной кривой. Может применяться в том случае, если сервер не проверяет, что точки принадлежат настоящей кривой, заявленной в криптосистеме.

Для демонстрации атаки попробуем воспроизвести \(с упрощениями\) 3-ю задачу из NeoQuest 2021. Вот условия:

```python
Encryption scheme: ECIES.
Elliptic Curve: secp256r1.

p = 0xffffffff00000001000000000000000000000000ffffffffffffffffffffffff
a = p - 3
b = 0x5ac635d8aa3a93e7b3ebbd55769886bc651d06b0cc53b0f63bce3c3e27d2604b
n = 0xffffffff00000000ffffffffffffffffbce6faada7179e84f3b9cac2fc632551
P = (0x6b17d1f2e12c4247f8bce6e563a440f277037d812deb33a0f4a13945d898c296, 
    0x4fe342e2fe1a7f9b8ee7eb4a7c0f9e162bce33576b315ececbb6406837bf51f5)
    
MAC for tag computation: HMAC-SHA256.
Block Cipher: AES-CTR.
Function for symmetric keys deriving (aes_key, mac_key): 
  SHA512(str(K.x).encode("utf-8")),where K.x - x-coordinate of point K
Public Key: 
Q = (0x58cd232129f77e18a4e0ae06c3f298d41ca35abb40a03997b1ba226104fb6722, 
    0x0d60be09cb87d89d3e486c3119589825160c59f037c52c106b4d23e7d0e8edab)
    
/encrypt
    Encrypt your message with ECIES scheme using server's public key.
		
		POST PARAMETERS
		*file: file to encrypt 
		
		RETURN encrypted file in format: 
		    * R (point in uncompressed form): 0x04+x+y  #1+32*2 bytes
		    * tag: HMAC-SHA256 (ciphertext)  # 32 bytes
		    * ciphertext: 8 prefix bytes + encrypted data

/decrypt
		Decrypt your message with ECIES scheme using server's secret key.
		
		POST PARAMETERS
		*file: file to decrypt in format: R (point in uncompressed form) + tag + 
		    ciphertext (8 prefix bytes + encrypted data)
		
		RETURN: decrypted file				
```

Нам предоставлен сервер с двумя методами `encrypt` и `decrypt`. Нужно отыскать долговременный секретный ключ сервера. Шифрование происходит с помощью алгоритма [ECIES](https://ru.wikipedia.org/wiki/ECIES), используется надёжная эллиптическая кривая **`secp256r1`**, также известная как **`prime256v1`**. Все характеристики кривой можно посмотреть по ссылке:

{% embed url="https://neuromancer.sk/std/secg/secp256r1" %}

Нам заранее дан порядок кривой `n`, но мы можем вычислить его динамически с помощью SageMath:

{% code title="https://sagecell.sagemath.org/" %}
```python
p = 0xffffffff00000001000000000000000000000000ffffffffffffffffffffffff
a = p - 3
b = 0x5ac635d8aa3a93e7b3ebbd55769886bc651d06b0cc53b0f63bce3c3e27d2604b
n = 0xffffffff00000000ffffffffffffffffbce6faada7179e84f3b9cac2fc632551
E = EllipticCurve(GF(p), [a, b])

print (E.order().hex())
print (E.order() == n)

# Будет выведено:

# ffffffff00000000ffffffffffffffffbce6faada7179e84f3b9cac2fc632551
# True
```
{% endcode %}

Код сервера неизвестен, поэтому напишем подобный на Node.js. Создаём файл `ecies.mjs`,  алгоритм шифрования следующий:

#### Генерация  пары ключей ECDH и общего секрета `K`

Пара ключей ECDH \(Elliptic Curve Diffie-Hellman\) - эфемерный \(потому что меняется каждый раз при новом шифровании\) публичный ключ `R` \(точка на кривой **`secp256r1`**\) и секретный ключ \("большое" число\). Первый передается второй стороне \(в нашем случае в ответе сервера в методе `encrypt`\), а последний нужен для вычисления общего секрета `K`:

Общий секретный ключ `K` нужен для того, чтобы непосредственно зашифровывать и расшифровывать сообщения, передающиеся между сторонами по незащищенному каналу связи. Для генерации общего секрета используется протокол Диффи — Хеллмана, [реализованный в Node.js](https://nodejs.org/dist/latest-v15.x/docs/api/crypto.html#crypto_class_ecdh).

Общий секрет вычисляется как произведение секретного ключа на публичный ключ. Несмотря на то, что пары ключей у двух сторон обмена сообщениями разные, общий секрет будет одинаковым \(на то он и назван общим\).

#### Генерация ключей шифрования и имитовставки функцией KDF

KDF \(Key Derivation Function\) - функция формирования секретного ключа \(или нескольких ключей\). В нашем случае общий секрет `K`, полученный на предыдущем этапе по протоколу Диффи — Хеллмана, преобразовывается в секретный ключ `Kenc` для использования в алгоритме симметричного блочного шифрования AES-[CTR ](https://ru.wikipedia.org/wiki/%D0%A0%D0%B5%D0%B6%D0%B8%D0%BC_%D1%88%D0%B8%D1%84%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F#Counter_mode_%28CTR%29)\(см. условия задачи\):

$$
KDF(K.x) = K_{enc} || K_{mac}
$$

По условиям задачи используется только `x`-координата общего секрета, а в качестве KDF выступает хеш-функция [SHA-512](https://ru.wikipedia.org/wiki/SHA-2). Символ \|\| в формуле означает конкатенацию \(склейку\) из двух половинок. `Kenc` - ключ симметричного шифрования для алгоритма AES-CTR. `Kmac` - ключ для создания аутентификационных данных, которые называются [имитовставкой ](https://ru.wikipedia.org/wiki/%D0%98%D0%BC%D0%B8%D1%82%D0%BE%D0%B2%D1%81%D1%82%D0%B0%D0%B2%D0%BA%D0%B0)\(`tag`\). Имитовставка нужна для защиты передаваемых данных от фальсификации.

#### Шифрование сообщения ключом `Kenc`

На этом этапе мы получаем шифротекст по алгоритму AES-CTR. Шифротекст - это результат операции шифрования данных.

#### Генерация имитовставки `tag` ключом `Kmac`

Имитовставка создается через HMAC-SHA256 в соответствии с условием задачи.

#### Возврат значения

Сервер отдаёт результат в виде склейки эфемерного публичного ключа `R`, имитовставки `tag` и шифротекста \(который по условию задачи предваряется 8 случайными префиксными байтами\).

{% hint style="info" %}
В нашем примере префиксные байты использоваться не будут.
{% endhint %}

### Код сервера и метода `/encrypt`

{% hint style="info" %}
Метод шифрует переданные данные публичным ключом сервера `Q`.

Стоит сказать, что метод `encrypt` не требуется, чтобы выполнить атаку. Он приведен в образовательных целях. Также он может применяться для тестов, чтобы генерировать правильные шифры.
{% endhint %}

{% hint style="danger" %}
Сервер будет использовать следующий долговременный секретный ключ:

`993913066650857197989941547339524640`

Или в  шестнадцатеричной системе счисления:

`bf6bb29560b6db07ac53849526aa20`
{% endhint %}

Долговременный секретный ключ, умноженный на точку-генератор эллиптической кривой `P`, дает в результате публичный ключ `Q` \(также точка на кривой\):

{% code title="https://sagecell.sagemath.org/" %}
```python
p = 0xffffffff00000001000000000000000000000000ffffffffffffffffffffffff
a = p - 3
b = 0x5ac635d8aa3a93e7b3ebbd55769886bc651d06b0cc53b0f63bce3c3e27d2604b
n = 0xffffffff00000000ffffffffffffffffbce6faada7179e84f3b9cac2fc632551
E = EllipticCurve(GF(p), [a, b])

Px = 0x6b17d1f2e12c4247f8bce6e563a440f277037d812deb33a0f4a13945d898c296
Py = 0x4fe342e2fe1a7f9b8ee7eb4a7c0f9e162bce33576b315ececbb6406837bf51f5

Qx = 0x58cd232129f77e18a4e0ae06c3f298d41ca35abb40a03997b1ba226104fb6722
Qy = 0x0d60be09cb87d89d3e486c3119589825160c59f037c52c106b4d23e7d0e8edab

Q = E(Qx, Qy)
P = E(Px, Py)

print (Q == 993913066650857197989941547339524640 * P)

# Будет выведено:

# True
```
{% endcode %}

Реализация алгоритма шифрования:

{% code title="ecies.mjs" %}
```javascript
import http from 'http';
import {
  createECDH,
  createHash,
  createHmac,
  createCipheriv,
  createDecipheriv,
  randomBytes
} from 'crypto';

const hostname = '0.0.0.0';
const port     = 8080;

// KDF
function hashMessage(cipherName, message) {
  return createHash(cipherName).update(message).digest();
}

// MAC
function macMessage(cipherName, key, message) {
  return createHmac(cipherName, key).update(message).digest();
}

const serverECDH = createECDH('prime256v1');
const secretKey  = Buffer.from('bf6bb29560b6db07ac53849526aa20', 'hex');

// Задаём долговременный секретный ключ сервера
serverECDH.setPrivateKey(secretKey);

// Вектор инициализации для AES, не используется в задаче
const iv = Buffer.alloc(16, 0);

const encrypt = (message) => {
  // Создаём объект ECDH (Elliptic Curve Diffie-Hellman) для обмена ключами
  // Ключи - это точки на эллиптической кривой, в нашем случае это prime256v1
  const ecdh = createECDH('prime256v1');
  // Генерируем ключи. В R будет записан эфемерный публичный ключ в несжатом виде
  // Несжатая (uncompressed) форма записывается как последовательность 0x04+x+y
  // x,y - координаты ключа на эллиптической кривой prime256v1
  // Секретный ключ останется в памяти сервера в объекте ECDH
  const R = ecdh.generateKeys();
  // K - общий секрет. Найдём его умножением секретного ключа на публичный ключ Q
  // computeSecret вызывает функцию DH_compute_key из OpenSSL
  // Q - долговременный публичный ключ сервера, он есть в условиях задачи
  // Q == secretKey * P == serverECDH.getPublicKey()
  // В K будет помещена x-координата общего секрета
  const K      = ecdh.computeSecret(serverECDH.getPublicKey());
  // Вычисляем SHA-512 от общего секрета, чтобы получить ключи Kenc и Kmac
  const hash   = hashMessage('sha512', K);
  // Kenc - левая половинка, Kmac - правая
  const encKey = hash.slice(0, hash.length / 2);
  const macKey = hash.slice(hash.length / 2);

  // Шифруем сообщение через AES-CTR
  const cipher      = createCipheriv('aes-256-ctr', encKey, iv);
  const firstChunk  = cipher.update(message);
  const secondChunk = cipher.final();
  const cipherText  = Buffer.concat([firstChunk, secondChunk]);

  // Вычисляем имитовставку tag через sha256
  const tag = macMessage(
    'sha256',
    macKey,
    cipherText
  );

  // Возвращаем склейку R || tag || cipherText
  return Buffer.concat([R, tag, cipherText]);
};

const decrypt = (payload) => {
  // TODO
};

const error = (res, error) => {
  res.statusCode = 400;
  res.setHeader('Content-Type', 'application/json');
  res.end(JSON.stringify({error}));
};

const server = http.createServer(async (req, res) => {
  if (req.method === 'POST') {
    if (req.url === '/encrypt') {
      let data = '';

      for await (const chunk of req) {
        data += chunk;
      }

      if (!data) {
        error(res, 'Empty message');
      } else {
        try {
          res.statusCode = 200;
          res.setHeader('Content-Type', 'application/octet-stream');
          res.end(encrypt(data));
        } catch (e) {
          error(res, e.message);
        }
      }
    } else if (req.url === '/decrypt') {
      let data = Buffer.from([]);

      for await (const chunk of req) {
        data = Buffer.concat([data, chunk]);
      }

      if (!data) {
        error(res, 'Empty message');
      } else {
        const payload = Buffer.from(data);

        if (payload[0] !== 0x04) {
          error(res, 'Invalid R point format');
        } else {
          try {
            res.statusCode = 200;
            res.setHeader('Content-Type', 'text/plain');
            res.end(decrypt(payload));
          } catch (e) {
            error(res, e.message);
          }
        }
      }
    } else {
      error(res, 'Unknown URL');
    }
  } else {
    error(res, 'Unsupported HTTP method');
  }
});

server.listen(port, hostname, () => {
  console.log(`ECIES server running at http://${hostname}:${port}/`);
});

```
{% endcode %}

{% hint style="info" %}
В задаче данные для шифрования передаются через форму в поле **file**. В нашем варианте данные сразу передаются в теле POST-запроса, что несколько проще в реализации.
{% endhint %}

Запускаем сервер:

```javascript
$ node ecies.mjs
```

Проверяем работу через [Postman](../wastelands/getting-started.md#postman), передав для шифрования строку `flag`:

![&#x420;&#x435;&#x437;&#x443;&#x43B;&#x44C;&#x442;&#x430;&#x442; &#x432;&#x441;&#x435;&#x433;&#x434;&#x430; &#x431;&#x443;&#x434;&#x435;&#x442; &#x440;&#x430;&#x437;&#x43D;&#x44B;&#x43C; &#x438;&#x437;-&#x437;&#x430; &#x438;&#x441;&#x43F;&#x43E;&#x43B;&#x44C;&#x437;&#x43E;&#x432;&#x430;&#x43D;&#x438;&#x44F; &#x44D;&#x444;&#x435;&#x43C;&#x435;&#x440;&#x43D;&#x44B;&#x445; &#x43A;&#x43B;&#x44E;&#x447;&#x435;&#x439;](../.gitbook/assets/image%20%28217%29.png)

Сохраним ответ сервера в файл `encrypted.dat`, нажав на **Save Response** ➡️ **Save to a file**. Полученный файл понадобится для расшифровки, чтобы проверить работоспособность криптосистемы.

### Код метода `/decrypt`

Метод `decrypt` должен выполнить те же действия, что и `encrypt`, но в обратном порядке. Однако, метод [`ecdh.computeSecret()`](https://nodejs.org/dist/latest-v15.x/docs/api/crypto.html#crypto_ecdh_computesecret_otherpublickey_inputencoding_outputencoding), который мы использовали для вычисления общего секрета, надёжен - он всегда проверяет, что переданный ему в аргументе публичный ключ реально принадлежит эллиптической кривой. В противном случае будет ошибка  `ERR_CRYPTO_ECDH_INVALID_PUBLIC_KEY`.

{% code title="/decrypt - без возможности invalid curve attack \(secureDecrypt\)" %}
```javascript
// Сравнивает две строки (буфера) без уязвимости к атаке по времени
function equalConstTime(b1, b2) {
  if (b1.length !== b2.length) {
    return false;
  }

  let result = 0;

  for (let i = 0; i < b1.length; i++) {
    result |= b1[i] ^ b2[i];
  }

  return result === 0;
}

const decrypt = (payload) => {
  const publicKeyLength = serverECDH.getPublicKey().length;
  
  const R = payload.slice(0, publicKeyLength);
  // computeSecret проверит, что точка R лежит на эллиптической кривой
  const K = serverECDH.computeSecret(R);

  // 32=256/8 - длина MAC в байтах (SHA256)
  const tag        = payload.slice(publicKeyLength, publicKeyLength + 32);
  const cipherText = payload.slice(publicKeyLength + 32);

  const hash   = hashMessage('sha512', K);
  const encKey = hash.slice(0, hash.length / 2);
  const macKey = hash.slice(hash.length / 2);

  const computedTag = macMessage(
    'sha256',
    macKey,
    cipherText
  );

  // Теперь нужно проверить, что вычисленная имитовставка соответствует принятой
  if (!equalConstTime(tag, computedTag)) {
    throw new Error('Incorrect tag');
  }

  const cipher      = createDecipheriv('aes-256-ctr', encKey, iv);
  const firstChunk  = cipher.update(cipherText);
  const secondChunk = cipher.final();

  return Buffer.concat([firstChunk, secondChunk]).toString();
};
```
{% endcode %}

Проверка через [Postman](../wastelands/getting-started.md#postman):

![&#x41F;&#x43E;&#x43B;&#x443;&#x447;&#x438;&#x43B;&#x438; &#x438;&#x441;&#x445;&#x43E;&#x434;&#x43D;&#x443;&#x44E; &#x441;&#x442;&#x440;&#x43E;&#x43A;&#x443; flag](../.gitbook/assets/image%20%28218%29.png)

Чтобы сделать возможной атаку **Invalid curve attack**, мы намеренно реализуем метод `decrypt` с уязвимостью: не будем проверять принадлежность передаваемого ключа эллиптической кривой **`secp256r1`**.

{% hint style="info" %}
Для расшифровки самостоятельно реализуем сложение и умножение точек на эллиптической кривой.
{% endhint %}

Реализация `decrypt` с уязвимостью:

{% code title="/decrypt - c возможностью invalid curve attack" %}
```javascript
const p = 0xffffffff00000001000000000000000000000000ffffffffffffffffffffffffn;
const a = p - 3n;

function mod(a, b = p) {
  const result = a % b;

  return result >= 0 ? result : b + result;
}

// https://brilliant.org/wiki/extended-euclidean-algorithm/
function egcd(a, b) {
  let [x, y, u, v] = [0n, 1n, 1n, 0n];

  while (a !== 0n) {
    let [q, r] = [b / a, b % a];
    let [m, n] = [x - u * q, y - v * q];

    [b, a] = [a, r];
    [x, y] = [u, v];
    [u, v] = [m, n];
  }

  return [b, x, y];
}

function invert(number, modulo = p) {
  if (number === 0n || modulo <= 0n) {
    throw new Error('invert: expected positive integers');
  }

  let [gcd, x] = egcd(mod(number, modulo), modulo);

  if (gcd !== 1n) {
    throw new Error('invert: does not exist');
  }

  return mod(x, modulo);
}

// Точка на эллиптической кривой
class Point {
  static #ZERO = new Point(0n, 0n);

  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  add(other) {
    const [a, b]           = [this, other];
    const [X1, Y1, X2, Y2] = [a.x, a.y, b.x, b.y];

    if (X1 === 0n || Y1 === 0n)
      return b;

    if (X2 === 0n || Y2 === 0n)
      return a;

    if (X1 === X2 && Y1 === Y2)
      return this.double();

    if (X1 === X2 && Y1 === -Y2)
      return Point.#ZERO;

    const lam = mod((Y2 - Y1) * invert(X2 - X1, p));
    const X3  = mod(lam * lam - X1 - X2);
    const Y3  = mod(lam * (X1 - X3) - Y1);

    return new Point(X3, Y3);
  }

  double() {
    const X1  = this.x;
    const Y1  = this.y;
    const lam = mod((3n * X1 * X1 + a) * invert(2n * Y1, p));
    const X3  = mod(lam * lam - 2n * X1);
    const Y3  = mod(lam * (X1 - X3) - Y1);

    return new Point(X3, Y3);
  }

  multiply(n) {
    let dbl = new Point(this.x, this.y);
    let p   = Point.#ZERO;
    let f   = Point.#ZERO;

    for (let i = 0; i <= 256; i++) {
      if ((n & 1n) === 1n)
        p = p.add(dbl);
      else f = f.add(dbl);

      n >>= 1n;
      dbl = dbl.double();
    }

    return p;
  }
}

const decrypt = (payload) => {
  const publicKeyLength = serverECDH.getPublicKey().length;

  const R  = payload.slice(0, publicKeyLength);
  const Rx = R.slice(1, 33);
  const Ry = R.slice(33);

  const RPoint = new Point(BigInt('0x' + Rx.toString('hex')),
    BigInt('0x' + Ry.toString('hex')));

  const K = RPoint.multiply(BigInt('0x' + secretKey.toString('hex')));

  // 32=256/8 - длина MAC в байтах (SHA256)
  const tag        = payload.slice(publicKeyLength, publicKeyLength + 32);
  const cipherText = payload.slice(publicKeyLength + 32);

  const hash   = hashMessage('sha512', Buffer.from(K.x.toString(16), 'hex'));
  const encKey = hash.slice(0, hash.length / 2);
  const macKey = hash.slice(hash.length / 2);

  const computedTag = macMessage(
    'sha256',
    macKey,
    cipherText
  );

  // Теперь нужно проверить, что вычисленная имитовставка соответствует принятой
  if (!equalConstTime(tag, computedTag)) {
    throw new Error('Incorrect tag');
  }

  const cipher      = createDecipheriv('aes-256-ctr', encKey, iv);
  const firstChunk  = cipher.update(cipherText);
  const secondChunk = cipher.final();

  return Buffer.concat([firstChunk, secondChunk]).toString();
};
```
{% endcode %}

Теперь метод вычисляет общий секрет `K` без проверки принадлежности точки `R` эллиптической кривой. А это значит, что можно скомпрометировать шифрование и узнать секретный ключ сервера, передавая специально рассчитанные точки. 

### Атакуем сервер

В листинге выше умножение точки `R` на скаляр \(секретный ключ\) в 110-й строке не использует `b` в расчётах. Код умножения находится, начиная с 82-й строки.

{% code title="https://sagecell.sagemath.org/" %}
```python
b = 0x5ac635d8aa3a93e7b3ebbd55769886bc651d06b0cc53b0f63bce3c3e27d2604b
```
{% endcode %}

И как было ранее сказано, теперь сервер вовсе не проверяет, что точка `R` принадлежит кривой **`secp256r1`**.

#### Алгоритм работы

Это значит, что мы можем переслать на сервер точку `R'`, которая будет принадлежать другой кривой `E'` с отличающимся параметром `b`. При этом можно выбрать `b` не очень большим, чтобы порядок кривой `n'` стал составным числом с несколькими малыми простыми множителями. Далее, взяв небольшой простой множитель `l` из факторизации `n'`, можно рассчитать точку-генератор `G` малого порядка \(`l`\).  Вспомним, что общий секрет вычисляется по следующей формуле:

$$
K = secretKey\times R
$$

Сервер будет перемножать свой долговременный секретный ключ на переданную точку `R` и далее вычислять имитовставку. Если теперь передать точку `R'` с малым порядком `l`, то новый общий секрет `K'` будет иметь `l` разных значений, так как мы находимся в циклической подгруппе. Мы можем рассчитать все возможные варианты значения точки `R'` и передавать её на сервер до тех пор, пока он не примет наш запрос. Это произойдет только в том случае, если вычисленная имитовставка совпадёт с той, которая передаётся в запросе.

Итак, нам нужно сгенерировать несколько кривых `E'` с различными параметрами `b`, далее в каждой кривой найти порядок `l`, разложить на простые множители, выбрать любой небольшой множитель, по нему вычислить базовую \(генератор\) точку `G`, и путем перемножения всех чисел в диапазоне \[`1`, `l-1`\] на `G` отыскать такую точку `R'`, при передаче которой сервер примет данные без ошибок и выполнит расшифровку:

$$
R' = w\in[1, l-1]\times G
$$

Ошибки расшифровки с высокой вероятностью не будет в том случае, если секретный ключ сервера и скалярный множитель `w` перед `G` будут [сравнимы ](https://ru.wikipedia.org/wiki/%D0%A1%D1%80%D0%B0%D0%B2%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5_%D0%BF%D0%BE_%D0%BC%D0%BE%D0%B4%D1%83%D0%BB%D1%8E)по модулю `l`:

$$
w\equiv \pm secretKey\mod{l}
$$

Знак ± здесь означает тот факт, что существует 2 значения `w`, при которых будет работать расшифровка. Это обусловлено тем, что у точек эллиптической кривой `P` и -`P` одинаковая `x`-координата, а ведь именно она используется для вычисления ключей из общего секрета. Так, если `l = 17`, `w = 2` и в таком варианте расшифровка проходит без ошибок, то их также не будет, если `w` задать равным `17-2=15` или `-2`.

Чтобы избавиться от ±, будет оперировать значениями, возведёнными в квадраты:

$$
w^2\equiv  secretKey^2\mod{l}
$$

Найдя достаточное количество таких точек `R'`, у нас в наличии будут соответствующие пары значений `w` и `l`, которые, возведены в квадрат. При достаточном количестве таких пар можно найти секретный ключ, используя китайскую теорему об остатках:

$$
w^2=CRT([w_1,w_2,...,w_n],[l_1,l_2,...,l_n])
$$

Количество аргументов `n` в данном случае определяется исходя из примерной величины секрета. Мы также можем добавлять новые аргументы до тех пор, пока не будет корректно вычислен квадратный корень:

$$
secretKey=\sqrt{w^2}
$$

### Практическая реализация

## Дополнительные источники

{% embed url="http://the2702.com/2015/05/08/invalid-curve-attack.html" %}

{% embed url="https://paulmillr.com/posts/noble-secp256k1-fast-ecc/" %}

{% embed url="https://cryptobook.nakov.com/" caption="Книга Practical Cryptography for Developers" %}

{% file src="../.gitbook/assets/breaking-the-bluetooth-pairing-fixed-coordinate-invalid-curve-attack.pdf" %}

{% file src="../.gitbook/assets/practical-invalid-curve-attacks-on-tls-ecdh.pdf" %}

{% file src="../.gitbook/assets/validation-of-elliptic-curve-public-keys.pdf" %}

