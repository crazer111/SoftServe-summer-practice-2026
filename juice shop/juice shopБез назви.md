## WASP Juice Shop: Початковий посібник з веб-безпеки

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#owasp-juice-shop-%D0%BF%D0%BE%D1%87%D0%B0%D1%82%D0%BA%D0%BE%D0%B2%D0%B8%D0%B9-%D0%BF%D0%BE%D1%81%D1%96%D0%B1%D0%BD%D0%B8%D0%BA-%D0%B7-%D0%B2%D0%B5%D0%B1-%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D0%BA%D0%B8)

**OWASP Juice Shop** — це один із найкращих сучасних інструментів для вивчення веб-безпеки. Це навмисно вразливий додаток, написаний на Node.js, Express та Angular. Оскільки він містить понад 100 челенджів, цей посібник допоможе вам пройти перші ключові етапи та зрозуміти логіку гри.

### 1. Підготовка (Setup)

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#1-%D0%BF%D1%96%D0%B4%D0%B3%D0%BE%D1%82%D0%BE%D0%B2%D0%BA%D0%B0-setup)

Найкращий спосіб запустити додаток локально — через Docker. Це забезпечує ізоляцію та швидкий старт.

1. Встановіть Docker.
    
2. Виконайте команду в терміналі: `docker run --rm -p 3000:3000 bkimminich/juice-shop` Маємо наступний результат: [![dockerrun](https://github.com/dimdimuzun/CS-practice/raw/main/JuiceShop/JuiceShop_docker_run.png)](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShop_docker_run.png)
    
3. Відкрийте браузер за адресою: **[http://localhost:3000](http://localhost:3000/)** ![[Pasted image 20260730155854.png]]
    

### 2. Перший крок: Пошук дошки результатів (Score Board)

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#2-%D0%BF%D0%B5%D1%80%D1%88%D0%B8%D0%B9-%D0%BA%D1%80%D0%BE%D0%BA-%D0%BF%D0%BE%D1%88%D1%83%D0%BA-%D0%B4%D0%BE%D1%88%D0%BA%D0%B8-%D1%80%D0%B5%D0%B7%D1%83%D0%BB%D1%8C%D1%82%D0%B0%D1%82%D1%96%D0%B2-score-board)

У Juice Shop посилання на список завдань приховане. Ваше перше завдання — знайти його самостійно.

- **Завдання:** Знайти сторінку зі списком усіх челенджів.
- **Як пройти:** Спробуйте вгадати URL (наприклад, перевірте `/score-board` або `/scoreboard`). Розробники часто ховають такі технічні сторінки в коді або через роутинг. Зазвичай багато цікавого можна знайти у файлі `main.js`. Тобто натискаємо F12, відкривається інспектор і далі шукаємо в коді щось схоже на **scoreboard**. Отримуємо наступний результат:

- **Пряме посилання:** [http://localhost:3000/#/score-board](http://localhost:3000/#/score-board)

### 3. Рівень 1: Базові вразливості

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#3-%D1%80%D1%96%D0%B2%D0%B5%D0%BD%D1%8C-1-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%96-%D0%B2%D1%80%D0%B0%D0%B7%D0%BB%D0%B8%D0%B2%D0%BE%D1%81%D1%82%D1%96)

#### Атака на пошуковий рядок (DOM XSS)

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#%D0%B0%D1%82%D0%B0%D0%BA%D0%B0-%D0%BD%D0%B0-%D0%BF%D0%BE%D1%88%D1%83%D0%BA%D0%BE%D0%B2%D0%B8%D0%B9-%D1%80%D1%8F%D0%B4%D0%BE%D0%BA-dom-xss)

- **Завдання:** Виконати XSS-атаку. [![JuiceShop_DOMXSS_task.png](https://github.com/dimdimuzun/CS-practice/raw/main/JuiceShop/JuiceShop_DOMXSS_task.png)](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShop_DOMXSS_task.png)
- **Як пройти:** У полі пошуку (Search) вгорі сторінки введіть скрипт: `<iframe src="javascript:alert('xss')">`
- **Результат:** Має з'явитися вікно з повідомленням, після чого челендж буде зараховано. 
- ![[Pasted image 20260730160126.png]]
- **Додатково** робимо відправку пейлоада ![[Pasted image 20260730160136.png]]

#### Витік конфіденційних даних (Sensitive Data Exposure)

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#%D0%B2%D0%B8%D1%82%D1%96%D0%BA-%D0%BA%D0%BE%D0%BD%D1%84%D1%96%D0%B4%D0%B5%D0%BD%D1%86%D1%96%D0%B9%D0%BD%D0%B8%D1%85-%D0%B4%D0%B0%D0%BD%D0%B8%D1%85-sensitive-data-exposure)

- **Завдання:** Знайти файл, який не має бути публічним. Спочатку шукаємо відкриті директорії, але отримуємо помилку: 
- 
- Вносимо зміни, які нам рекомендовано, та знову запускаємо сканування: ![[Pasted image 20260730160157.png]]
- 
- Перезпускаємо контейнер та йдемо до знайденого FTP: ![[Pasted image 20260730160303.png]]
    
- **Як пройти:** Перейдіть до директорії **[http://localhost:3000/ftp](http://localhost:3000/ftp)**. Там зазвичай знаходяться бекапи або `.md` файли.
    
- **Підказка:** Спробуйте завантажити файли, які сервер намагається блокувати, використовуючи обхід фільтрів (наприклад, додаючи `%2500.md` до назви).
    

![[Pasted image 20260730160320.png]] 

Як результат - пройшли ще декілька завдань: [![[Pasted image 20260730160339.png]]

### 4. Рівень 2: Злам акаунтів (Authentication)

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#4-%D1%80%D1%96%D0%B2%D0%B5%D0%BD%D1%8C-2-%D0%B7%D0%BB%D0%B0%D0%BC-%D0%B0%D0%BA%D0%B0%D1%83%D0%BD%D1%82%D1%96%D0%B2-authentication)

#### Адмінська панель (SQL Injection)

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#%D0%B0%D0%B4%D0%BC%D1%96%D0%BD%D1%81%D1%8C%D0%BA%D0%B0-%D0%BF%D0%B0%D0%BD%D0%B5%D0%BB%D1%8C-sql-injection)

Це класична атака, яка дозволяє увійти в систему без знання пароля.

1. Перейдіть на сторінку **Login**.
2. Введіть у поле Email: `' or 1=1--`
3. Введіть будь-який пароль.

Має виглядати наступним чином: ![[Pasted image 20260730160352.png]]
![[Pasted image 20260730160359.png]]

**Чому це працює?** Запит до бази даних виглядає приблизно так: $SELECT * FROM Users WHERE email = '' OR 1=1--' AND password = '...'$

Оскільки умова `1=1` завжди істинна, а символи `--` закоментовують решту запиту (перевірку пароля), ви автоматично увійдете під першим користувачем у базі — зазвичай це адміністратор.

### 5. Інструментарій для проходження

[](https://github.com/dimdimuzun/CS-practice/blob/main/JuiceShop/JuiceShopStart.md#5-%D1%96%D0%BD%D1%81%D1%82%D1%80%D1%83%D0%BC%D0%B5%D0%BD%D1%82%D0%B0%D1%80%D1%96%D0%B9-%D0%B4%D0%BB%D1%8F-%D0%BF%D1%80%D0%BE%D1%85%D0%BE%D0%B4%D0%B6%D0%B5%D0%BD%D0%BD%D1%8F)

Для виконання складніших завдань вам знадобляться:

| Інструмент           | Для чого використовувати                                         |
| :------------------- | :--------------------------------------------------------------- |
| **Burp Suite**       | Перехоплення та модифікація HTTP-запитів.                        |
| **Browser DevTools** | Аналіз JavaScript-коду (файл `main.js` містить багато підказок). |
| **FFUF / Gobuster**  | Автоматизований пошук прихованих директорій та файлів.           |
| **CyberChef**        | Декодування Base64, MD5, SHA та інших форматів.                  |