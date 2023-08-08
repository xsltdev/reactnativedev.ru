---
description: Конечной целью создания ворклетов было определение небольших фрагментов JavaScript-кода, которые мы запускаем при обновлении свойств представления или реагировании на события в потоке UI
---

# Ворклеты

Конечной целью создания ворклетов было определение небольших фрагментов JavaScript-кода, которые мы запускаем при обновлении свойств представления или реагировании на события в потоке UI. Естественной конструкцией в JavaScript для такой цели стал простой метод. В Reanimated мы порождаем вторичный JS-контекст в потоке UI, который затем может выполнять функции JavaScript. Единственное, что требуется, - это наличие в верхней части функции директивы "worklet":

```js
function someWorklet(greeting) {
    'worklet';
    console.log("Hey I'm running on the UI thread");
}
```

Функции - это отличная конструкция для наших нужд, поскольку с запускаемым ими кодом можно взаимодействовать, передавая аргументы. Каждая функция worklet может быть запущена либо в основном потоке React Native, если вы просто вызываете эту функцию в своем коде, либо в потоке UI с помощью `runOnUI`. Обратите внимание, что выполнение UI является асинхронным с точки зрения вызывающей стороны. При передаче аргументов они будут скопированы в JS-контекст UI.

```js
function someWorklet(greeting) {
    'worklet';
    console.log(greeting, 'From the UI thread');
}

function onPress() {
    runOnUI(someWorklet)('Howdy');
}
```

Помимо аргументов, функции также захватывают контекст, в котором они определены. Таким образом, если у вас есть переменная, которая определена вне области видимости ворклета, но используется внутри ворклета, она также будет скопирована вместе с аргументами, и вы сможете ее использовать (мы называем это захватом заданных параметров):

```js
const width = 135.5;

function otherWorklet() {
    'worklet';
    console.log('Captured width is', width);
}
```

Ворклеты могут перехватывать (или принимать в качестве аргументов) аргументы от других ворклетов, в этом случае при вызове они будут выполняться синхронно в потоке UI:

```js
function returningWorklet() {
    'worklet';
    return "I'm back";
}

function someWorklet() {
    'worklet';
    let what = returningWorklet();
    console.log(
        'On the UI thread, other worklet says',
        what
    );
}
```

Причем это работает и для обычных методов. На самом деле `console.log` не определен в JS-контексте UI, а является ссылкой на метод, который предоставляет JS-контекст React Native. Поэтому, когда мы использовали `console.log` в предыдущих примерах, он действительно выполнялся в потоке React Native.

```js
function callback(text) {
    console.log('Running on the RN thread', text);
}

function someWorklet() {
    'worklet';
    console.log(
        "I'm on UI but can call methods from the RN thread"
    );
    runOnJS(callback)('can pass arguments too');
}
```

## Использование хуков

На практике при написании анимаций и взаимодействий с Reanimated вам редко придется создавать заготовки с помощью директивы `'worklet'` (достаточно взглянуть на папку `Example/`, чтобы увидеть, что у нас не так уж много вхождений этой директивы).

Чаще всего вы будете использовать заготовки, которые могут быть созданы с помощью одного из хуков Reanimated API, например, `useAnimatedStyle`, `useDerivedValue`, `useAnimatedGestureHandler` и т.д.

При использовании одного из хуков, перечисленных в Reanimated API Reference, мы автоматически определяем, что предоставленный метод является worklet'ом, и не требуем указания директивы. Метод, переданный хуку, будет превращен в worklet и автоматически выполнен в потоке UI.

```js
const style = useAnimatedStyle(() => {
    console.log('Running on the UI thread');
    return {
        opacity: 0.5,
    };
});
```

## Ссылки

-   [Worklets](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/worklets)
