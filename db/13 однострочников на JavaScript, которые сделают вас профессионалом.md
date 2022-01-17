---
tags: [js, javascript, программирование]
---
# 🤹 13 однострочников на JavaScript, которые сделают вас профессионалом

[eFusion](https://proglib.io/u/eFusion) 09 февраля 2021

Этот язык программирования настолько крут, что позволяет творить чудеса с помощью только одной строки кода. Рассмотрим 13 таких однострочников из разряда like a pro.

![](https://media.proglib.io/posts/2021/02/09/760901e8f0f7063d23e116b4fee7df60.png)

_Перевод публикуется с сокращениями, автор [оригинальной статьи](https://medium.com/dailyjs/13-javascript-one-liners-thatll-make-you-look-like-a-pro-29a27b6f51cb) Mohammed Ayar._

## 1. Получение случайного логического значения (true/false)

Эта функция вернет логическое значение (true или false) с помощью метода **Math.random()**. Метод генерирует случайное число от 0 до 1, после чего мы проверим, больше оно или меньше 0,5. Это означает, что вероятность получить истину или ложь составляет 50%.

![](https://media.proglib.io/posts/2021/02/09/8e8edcd725af2c36426d408f7f9f2d50.png)

```js
const randomBoolean = () => Math.random() >= 0.5;
console.log(randomBoolean());
// Результат: изменение 50/50 при возврате true из false
```
    

## 2. Проверка, является ли день будним

Используя этот метод, можно проверить, является ли указанная в функции дата рабочей или это выходной день.

![](https://media.proglib.io/posts/2021/02/09/3630e1d89029f26981414289c232ac4c.png)

```js
const isWeekday = (date) => date.getDay() % 6 !== 0;
console.log(isWeekday(new Date(2021, 0, 11)));
// Результат: true (понедельник)
console.log(isWeekday(new Date(2021, 0, 10)));
// Результат: false (воскресенье)
```
    

## 3. Реверс строки

Существует несколько различных способов перевернуть строку. Этот - один из самых простых, использующий **split()**, **reverse()** и **join()**.

![](https://media.proglib.io/posts/2021/02/09/5c5f190276bc9341e01d26343bf36476.png)

```js
const reverse = str => str.split('').reverse().join('');
reverse('hello world');     
// Результат: "dlrow olleh"
```
    

## 4. Проверка на активную/в фокусе закладку

Можно проверить, находится ли текущая вкладка в активном состоянии (в фокусе), используя свойство **document.hidden**.

![](https://media.proglib.io/posts/2021/02/09/90656480ee32639597ac7990f108c921.png)

```js
const isBrowserTabInView = () => document.hidden;
isBrowserTabInView();
// Результат: возвращает true или false в зависимости от того, 
    находится ли вкладка в фокусе
```
    

## 5. Четное/нечетное число

Элементарная задача, которую можно решить с помощью оператора «по модулю» (%). Если вы не слишком знакомы с ним - вот хорошее визуальное объяснение со [Stack Overflow](https://stackoverflow.com/questions/17524673/understanding-the-modulus-operator/17525046#17525046):

![](https://media.proglib.io/posts/2021/02/09/b8712857d7e083bd9c79ed5d49820556.png)

```js
const isEven = num => num % 2 === 0;
console.log(isEven(2));
// Результат: true
console.log(isEven(3));
// Результат: false
```
    

## 6. Получение времени из даты

Используя метод **.toTimeString()** и разрезая строку в нужном месте, можно получить время из даты, с которой мы работаем, или получить текущее время.

![](https://media.proglib.io/posts/2021/02/09/9094ea7ee53d3f89add5f86457ad61b4.png)

```js
const timeFromDate = date => date.toTimeString().slice(0, 8);
console.log(timeFromDate(new Date(2021, 0, 10, 17, 30, 0))); 
// Результат: "17:30:00"
console.log(timeFromDate(new Date()));
// Результат: будет логировать текущее время
```
    

## 7. Усечение числа до десятичной части

При помощи метода **Math.pow()** можно усечь число до определенной десятичной части, которую мы предоставляем в функции.

![](https://media.proglib.io/posts/2021/02/09/0176afa6fc8558a02552e612f681f10a.png)

```js
const toFixed = (n, fixed) => ~~(Math.pow(10, fixed) * n) / Math.pow(10, fixed);
// Примеры
toFixed(25.198726354, 1);       // 25.1
toFixed(25.198726354, 2);       // 25.19
toFixed(25.198726354, 3);       // 25.198
toFixed(25.198726354, 4);       // 25.1987
toFixed(25.198726354, 5);       // 25.19872
toFixed(25.198726354, 6);       // 25.198726
```
    

## 8. Проверка, находится ли элемент в фокусе

Чтобы проверить, находится ли элемент в данный момент в фокусе, используется свойство **document.activeElement**.

![](https://media.proglib.io/posts/2021/02/09/0fbf2f0e810c74f46109febde0fb82be.png)

```js
const elementIsInFocus = (el) => (el === document.activeElement);
elementIsInFocus(anyElement)
// Результат: вернет true, если в фокусе, false, если нет
```
    

## 9. Поддерживает ли текущий пользователь touch-события

![](https://media.proglib.io/posts/2021/02/09/56d9da12192376dfba6005466cfc29ed.png)

```js
const touchSupported = () => {
  ('ontouchstart' in window || window.DocumentTouch && document instanceof window.DocumentTouch);
}
console.log(touchSupported());
// Результат: вернет true, если поддерживаются 
      touch-события, false, если нет
```
    

## 10. Проверка, работает ли пользователь на устройстве Apple

Мы можем применить **navigator.platform**, чтобы проверить, используется ли клиентское устройство от Apple.

![](https://media.proglib.io/posts/2021/02/09/22d7a9632bba5051034db1bc58031093.png)

```js
const isAppleDevice = /Mac|iPod|iPhone|iPad/.test(navigator.platform);
console.log(isAppleDevice);
// Результат: вернет true, если пользователь находится на устройстве Apple
```
    

## 11. Прокрутка страницы до конкретной позиции

Метод **window.scrollTo()** принимает координаты x и y для прокрутки. Если установить их равными нулю, мы прокрутим страницу до самого верха.

Следует помнить, что метод **.scrollTo()** не поддерживается в Internet Explorer.

![](https://media.proglib.io/posts/2021/02/09/b08ddc63a292f1868fcadc670235c0a2.png)

```js
const goToTop = () => window.scrollTo(0, 0);
goToTop();
// Результат: браузер будет прокручиваться в верхнюю часть страницы
```
    

## 12. Получить среднее значение аргументов

Чтобы получить среднее значение аргументов, можно использовать метод **reduce**.

![](https://media.proglib.io/posts/2021/02/09/d2987f59a463c5ff6a13bd284b57821b.png)

```js
const average = (...args) => args.reduce((a, b) => a + b) / args.length;
average(1, 2, 3, 4);
// Результат: 2.5
```
    

## 13. Преобразование температуры Фаренгейт/Цельсий

Данные две функции помогут вам преобразовать градусы Фаренгейта в температуру по Цельсию и наоборот.

![](https://media.proglib.io/posts/2021/02/09/ab39af39f45c74a0c1b314b261a31b7a.png)

```js
const celsiusToFahrenheit = (celsius) => celsius * 9/5 + 32;
const fahrenheitToCelsius = (fahrenheit) => (fahrenheit - 32) * 5/9;
// Примеры
celsiusToFahrenheit(15);    // 59
celsiusToFahrenheit(0);     // 32
celsiusToFahrenheit(-20);   // -4
fahrenheitToCelsius(59);    // 15
fahrenheitToCelsius(32);    // 0
```
    

## Заключение

JavaScript - очень мощный инструмент, скрывающий массу полезных трюков и способный на магию. Не останавливайтесь на достигнутом, продолжайте постигать азы, совершенствуйтесь и да пребудет с вами сила. Удачи!

Дополнительные материалы:

-   [Создаем мобильное приложение для Android на JavaScript с помощью Apache Cordova](https://proglib.io/p/sozdaem-mobilnoe-prilozhenie-dlya-android-na-javascript-s-pomoshchyu-apache-cordova-2021-02-08)
-   [Альтернативные фреймворки JavaScript: есть ли жизнь за пределами большой тройки?](https://proglib.io/p/alternativnye-freymvorki-javascript-est-li-zhizn-za-predelami-bolshoy-troyki-2021-02-04)  
    
-   [Из джунов в мидлы: каверзные вопросы по JavaScript на собеседованиях](https://proglib.io/p/iz-dzhunov-v-midly-kaverznye-voprosy-po-javascript-na-sobesedovaniyah-2021-02-02)  
    
-   [7 советов изучающему Vue.js новичку](https://proglib.io/p/7-sovetov-novichku-izuchayushchemu-vue-js-2021-01-29)  
    
-   [Из джунов в мидлы: зачем фронтенд-разработчику информационная безопасность?](https://proglib.io/p/iz-dzhunov-v-midly-zachem-frontend-razrabotchiku-informacionnaya-bezopasnost-2021-01-29)  
    

## Источники

-   [https://medium.com/dailyjs/13-javascript-one-liners-thatll-make-you-look-like-a-pro-29a27b6f51cb](https://medium.com/dailyjs/13-javascript-one-liners-thatll-make-you-look-like-a-pro-29a27b6f51cb)
