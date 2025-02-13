---
title: Объекты в Javascript
draft: false
tags: 
date: 2025-02-06
updated: 2025-02-09T15:08
---
## Общая информация
> [!NOTE] Title
> Любая сущность в JS - это объект

Сам по себе **объект** - это некий набор свойств. А у свойств есть значения. Таким образом объекты - это наборы свойств с определенными значениями.
Пример **одного объекта**:
```js
{
    visible: true,
    colorDepth: 24,
    title: "My Image",
    orientation: {
        angle: 0,
        type: "landscape"
    }
}
```
`orientation` - это **вложенный объект** со своими личными свойствами
Другие примеры объектов:
- функции
- массивы
- числа и строки
	- вот эти товарищи лишь ведут себя как **объекты**

Другой пример:
```js
console.log("Hello World")
```
Вот, как выглядит схематически разбор этого выражения:
	![[Pasted image 20250209144022.png]]
	`console` - это объект, а объекты содержат, как мы уже знаем, свойства - ключи и значения. Так вот `log` - это метод. Метод - это функция, которая является **значением** свойства объекта.
Если мы введем в браузере или в терминале `console`, то увидим подтверждение тому, что `console` - это объект:
	![[Pasted image 20250209144056.png]]

## Действия над объектами
### Получение, изменение и добавление свойств объекта
- Пример обращения к объектам, а также изменения и добавления свойств объекта я приводил [[JavaScript#Ссылочные типы|тут]]
### Удаление свойств объекта
- Единственное, чего не было в указанном конспекте, это примера удаления свойства у объекта
```js
const objectA = {
  b: 10,
  c: 5,
};

delete objectA.b;
console.log(objectA.b); // undefined
```

Другой пример добавления новых свойств:
```js
const myCity = {
  city: "New York",
};

myCity["popular"] = true;
console.log(myCity);

const countryPropertyName = "country";
myCity[countryPropertyName] = "USA";

console.log(myCity);
```

Тут используется иной синтаксис - с квадратными скобками.
Он используется потому, что если мы захотим добавить новое свойство вот так:
```js
myCity.countryPropertyName = "country"
```
Тогда будет создано новое свойство с названием `countryPropertyName`. Поэтому мы используем:
```js
const countryPropertyName = "country";
myCity[countryPropertyName] = "USA";
```
## Вложенные свойства объекта
Пример использования вложенных свойств:
```js
const allIncomes = {
  salary: {
    first_value: {
      sum: 70000,
      day: 10,
    },
    second_value: {
      sum: 70000,
      day: 25,
    },
    pension: {
      sum: 14000,
      day: 14,
    },
  },
};

const allOutcomes = {
  rent: {
    sum: 40000,
    day: 21,
  },
  services: {
    sum: 8000,
    day: 10,
  },
  subscriptions: {
    yandexMusic: {
      sum: 400,
      day: 12,
    },
    sberPrime: {
      sum: 299,
      day: 6,
    },
  },
};
```

## Использование переменных в объектах
В примере выше - это мой проект по автоматизации бюджета, который изначально я задумывал на Python (сейчас я уже не уверен, стоит ли писать его на Python, потому что JS мне выглядит более визуально понятным)

Так вот допустим у меня будет несколько подписок `subscriptions` с одной суммой, тогда бы я сделал так:
```js
const someSubPrice = 200
subscriptions: {
    yandexMusic: {
      sum: 400,
      day: 12,
    },
    sberPrime: {
      sum: 299,
      day: 6,
    },
    someSub1: {
      sum: someSubPrice,
      day: 1,
    },
    someSub2: {
      sum: someSubPrice,
      day: 2,
    },
  },
```
У меня эти свойства размещены в конце моего объекта, но лучше размещать их в самом начале для большей наглядности

## Глобальные объекты
Мы знаем, что у JS есть встроенные объекты, такие как уже упомянутый `console`
Но Глобальные объекты - это также, как и обычные объекты, наборы свойств и значений к ним, но они доступны **везде** в нашем коде.

Существуют такие глобальные объекты:
- `window` - он существует в рамках веб-браузера
- `global` - он существует в nodejs

Для доступа к глобальным объектам мы используем, также как и с обычными объектами, запись вида `object.method`:
```js
window.innerHeight
685
```

```node
> global.clearInterval
[Function: clearInterval]
```

### Унифицированный глобальный объект globalThis
Существует унифицированный глобальный объект `globalThis`. Он выводит тоже самое, что и `window` в веб-браузере или `global` в nodejs:
```js
globalThis.innerHeight
685
```

```node
> globalThis.clearInterval
[Function: clearInterval]
```

### Свойства глобальных объектов


