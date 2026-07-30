### 1. Отримання **IP-адресу цільової машини**

[](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox_guide.md#1-%D0%BE%D1%82%D1%80%D0%B8%D0%BC%D0%B0%D0%BD%D0%BD%D1%8F-ip-%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D1%83-%D1%86%D1%96%D0%BB%D1%8C%5B...)

```shell
sudo netdiscover -r 10.0.2.0/24
```

***** Питання: яка з команд будк працювати швидше? sudo netdiscover або sudo netdiscover -r 10.0.2.0/24

### 2. Сканування **відкритих портів цільової машини**.

[](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox_guide.md#2-%D1%81%D0%BA%D0%B0%D0%BD%D1%83%D0%B2%D0%B0%D0%BD%D0%BD%D1%8F-%D0%B2%D1%96%D0%B4%D0%BA%D1%80%D0%B8%D1%82%D0%B8%D1%85-%D0%...)

Можна зробити різними шляхами:

```shell
sudo nmap -A --reason 10.0.2.12
```

або

```shell
sudo nmap -sV --reason 10.0.2.12
```

![Pasted image 20260730164926.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730164926.png)

***** Питання: в чому різниця між цима командами (декілька вірних варіантів)

Отримуємо наступне:


### 3. Дослідження вебсервера

[](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox_guide.md#3-%D0%B4%D0%BE%D1%81%D0%BB%D1%96%D0%B4%D0%B6%D0%B5%D0%BD%D0%BD%D1%8F-%D0%B2%D0%B5%D0%B1%D1%81%D0%B5%D1%80%D0%B2%D0%B5%5B...)

У браузері відкриваємо [http://10.0.2.12](http://10.0.2.12/), отримуємо:

![Pasted image 20260730164944.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730164944.png)


Скористуємось **login** та отримуємо типове запрошення від **wordpress**:

![Pasted image 20260730164951.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730164951.png)


Це може бути одним із варіантів входу в систему, але будуть потрібні креденшіали.

То проведемо дослідження структури вебсайту за допомогою **gobuster**:

gobuster dir -u [http://10.0.2.12/](http://10.0.2.12/) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

![Pasted image 20260730164957.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730164957.png)


Перевірка доступних посилань дає результат лише по останньому, то маємо такий результат:

![Pasted image 20260730165003.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165003.png)


Визначили трьох користувачів, далі потрібно більш детально дослідити вордпресс на наявність додаткової і�[...]

wpscan --url [http://10.0.2.12/](http://10.0.2.12/) -e

Знову знаходимо трьох користувачів, вся інша інформація не несе цінності.

![Pasted image 20260730165012.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165012.png)


Нвступний крок - брутфорс за словником. ***** Питання: чому ми обираємо саме користувача "c0ldd" для проведення б�[...]

```shell
wpscan --url http://10.0.2.12/ -U c0ldd -P /usr/share/wordlists/rockyou.txt
```

Після виконання маємо наступний результат:

![Pasted image 20260730165020.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165020.png)


Далі робимо логін в вордпрес та шукаємо можливості впровадження коду для створення реверс-шелу. Для цього пе�[...]

![Pasted image 20260730165031.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165031.png)


Наступним кроком маємо встановити, який з темплейтів ми зможемо модифікувти таким чином, щоб отримати реверс[...]

[![php-reverse-shell.php](https://github.com/online-marathon/AC-C0lddBox/raw/main/C0lddBox/image-10.png)](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox/image-10.png)

Зазвичай, код для реверс-шелу може бути доданий або в шаблон "Footer", або в "404". То як раз і почнемо з "Footer".

![Pasted image 20260730165048.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165048.png)


Замінюємо код у "footer.php" на код з "php-reverse-shell.php", та додаємо кастомізацію.

![Pasted image 20260730165042.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165042.png)


В терміналі Калі виконуємо наступну команду:

```shell
nc -lvnp 3421
```

-l - слухати -v - балакучість -n - не перевіряти DNS (швидше працює) -p - вказати номер порта

Далі робимо оновлення основної сторінки вебсервера та маємо наступний результат:

![Pasted image 20260730165106.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165106.png)


Команда **`pty.spawn("/bin/bash")` покращує таку оболонку**, перетворюючи її на більш «справжню» інтерактивну сесію Bash.

python3 -c 'import pty; pty.spawn("/bin/bash")' Як результат отримуємо повноцінний командний рядок bash:

[![alt text](https://github.com/online-marathon/AC-C0lddBox/raw/main/C0lddBox/image-14.png)](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox/image-14.png)

### 4. Підвищення привілеїв

[](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox_guide.md#4-%D0%BF%D1%96%D0%B4%D0%B2%D0%B8%D1%89%D0%B5%D0%BD%D0%BD%D1%8F-%D0%BF%D1%80%D0%B8%D0%B2%D1%96%D0%BB%D0%B5%D1%97%D0%B2...)

Зараз, коли ми знаходимося всередині сервера, давайте пошукаємо файли, які містять важливу інформацію. На веб[...]

![Pasted image 20260730165116.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165116.png)


Передивляємось зміст файлу **wp-config.php** за допомогою утіліти **cat**:

![Pasted image 20260730165124.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165124.png)


Спробуємо зайти в сисетму під користовачем "c0dd" зі знайденим паролем:

su c0ldd

отримуємо результат:

![Pasted image 20260730165132.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165132.png)


перейдемо до домашнього каталогу користувача та дослідемо його зміст, та виведемо на термінал зміст файлу[...]


Декодуємо за допомогою **[www.base64decode.org](http://www.base64decode.org/)** та отримуємо такий результат-привітання:

![Pasted image 20260730165150.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165150.png)


Визначимо, які саме повноваження є за допомогою команди:

![Pasted image 20260730165156.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165156.png)

Ми можемо підвищити свої привілеї до root, використовуючи будь-яку з цих трьох команд, і спробуємо всі три спос...[...]

[https://gtfobins.github.io/](https://gtfobins.github.io/)

#### 4.1. Використання **chmod**

[](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox_guide.md#41-%D0%B2%D0%B8%D0%BA%D0%BE%D1%80%D0%B8%D1%81%D1%82%D0%B0%D0%BD%D0%BD%D1%8F-chmod)

Небезпека полягає в тому, що якщо цьому бінарному файлу дозволено виконуватися від імені суперкористувача ч[...]

[![sudo chmod](https://github.com/online-marathon/AC-C0lddBox/raw/main/C0lddBox/image-22.png)](https://github.com/online-marathon/AC-C0lddBox/blob/main/C0lddBox/image-22.png)

Скористаємось цім методом, щоб змінити права доступу до файлу, який обмежений для користувача з низькими при...[...]

```shell
LFILE=root
```

Після цього виконаємо команду:

```shell
sudo chmod 6777 $LFILE
```

Пояснення:

sudo — запускає команду від імені root (superuser). chmod 6777: 6 — надає власнику файлу права на читання та запис (read, write). 777 [...]

Після виконання цієї пари команд маємо наступний результат:

![Pasted image 20260730165218.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165218.png)


Декодуємо base64 за допомогою командного рядку:

![Pasted image 20260730165237.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165237.png)
![Pasted image 20260730165246.png](https://github.com/crazer111/SoftServe-summer-practice-2026/raw/main/ColdBox/Pasted%20image%2020260730165246.png)
