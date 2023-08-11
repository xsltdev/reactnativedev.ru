---
description: Глоссарий терминов
---

# Глоссарий терминов

## Анимированный компонент {#animated-component}

Анимированные компоненты - это компоненты, которые Reanimated может анимировать. Reanimated поставляется с небольшим количеством встроенных компонентов, таких как `Animated.View`, `Animated.ScrollView` или `Animated.ScrollView`.

```js
import Animated from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            style={{
                width: 100,
                height: 100,
                backgroundColor: 'violet',
            }}
        />
    );
}
```

Для компонентов, не являющихся частью Reanimated, чтобы сделать их пропсы и стили анимируемыми, необходимо обернуть их с помощью [`createAnimatedComponent`](../core/createAnimatedComponent.md):

```js
import Animated from 'react-native-reanimated';
import { Circle } from 'react-native-svg';

const AnimatedCircle = Animated.createAnimatedComponent(
    Circle
);
```

## Общие значения {#shared-value}

Общие значения являются движущим фактором всех ваших анимаций в Reanimated.

Они определяются с помощью хука `useSharedValue`:

```js
import { useSharedValue } from 'react-native-reanimated';
```

и доступ к ним и их изменение осуществляется через их свойство `.value`:

```js
function App() {
    const sv = useSharedValue(0);

    const handlePress = () => {
        sv.value += 10;
    };

    // rest of your glamorous code ✨
}
```

Это может быть значение любого типа. При использовании общие значения автоматически синхронизируются между [JavaScript-потоком](glossary.md#javascript-thread) и [UI-потоком](glossary.md#ui-thread).

## Анимируемое значение {#animatable-value}

Анимируемое значение относится к типу значений, которые могут быть использованы для анимации. К таким значениям относятся числа, строки и массивы чисел.

Строковые значения могут быть анимированы, если они имеют определенный формат, например `10deg`, `21%` или даже цвет, например `ffaabb` или `rgba(100, 200, 100, 0.7)`.

## Функция анимации {#animation-function}

Анимационные функции - это функции, позволяющие создавать анимацию. Они представляют собой строительные блоки, описывающие поведение анимации.

Reanimated поставляется с 3 встроенными функциями анимации:

-   [`withSpring`](../animations/withSpring.md) позволяет создавать анимацию на основе пружин.
-   [`withTiming`](../animations/withTiming.md) позволяет создать анимацию, основанную на длительности и [easing](https://easings.net/)
-   [`withDecay`](../animations/withDecay.md) позволяет создавать анимацию, имитирующую движение объектов с заданной скоростью замедления.

Эти функции можно комбинировать с [модификаторами анимации](glossary.md#animation-modifier) для создания насыщенных и сложных анимаций.

## Модификатор анимации {#animation-modifier}

Модификаторы анимации - это функции, используемые для настройки анимации.

Reanimated поставляется с 3 встроенными модификаторами:

-   [`withDelay`](../animations/withDelay.md) позволяет добавить задержку перед началом анимации
-   [`withRepeat`](../animations/withRepeat.md) позволяет повторять анимацию определенное количество раз
-   [`withSequence`](../animations/withSequence.md) позволяет выстраивать анимацию в цепочку друг за другом

## Объект анимации {#animation-object}

Объект анимации - это значение, возвращаемое из [функций анимации](glossary.md#animation-function) и [модификаторов](glossary.md#animation-modifier), которое содержит текущее состояние анимации, включая условия ее начала и окончания, а также функцию `onFrame`. Эти значения позволяют вычислить состояние анимации для каждого кадра.

При передаче объекта анимации в [общее значение](glossary.md#shared-value) он автоматически рассматривается как [анимируемое значение](glossary.md#animatable-value).

Например, рассмотрим следующий код:

```js
sv.value = withSpring(100);
```

Несмотря на то, что `withSpring` возвращает объект анимации, конечный результат, хранящийся в общем значении, является просто числом.

## Анимации в инлайн-стилистике {#animations-in-inline-styling}

Передача общих значений непосредственно в свойство `style` без использования `useAnimatedStyle`.

Например:

```js
function App() {
    const width = useSharedValue(100);

    return <Animated.View style={{ width }} />;
}
```

## Worklet {#worklet}

Ворклеты - это короткие JavaScript-функции, которые могут выполняться в потоке [UI thread](glossary.md#ui-thread). Их также можно запускать в потоке JavaScript, как и функции в коде.

В большинстве случаев при работе с Reanimated код автоматически [workletized](glossary.md#to-workletize) и по умолчанию выполняется в потоке UI.

```js
const style = useAnimatedStyle(() => {
    console.log('Running on the UI thread');
    return { opacity: 0.5 };
});
```

Вы можете создавать собственные worklet'ы, используя директиву `"worklet";` в верхней части функции.

```js
function myWorklet() {
    'worklet';
    console.log('Running on the UI thread');
}
```

[`runOnUI`](../threading/runOnUI.md) позволяет вручную запускать ворклеты в потоке UI:

```js
function myWorklet(greeting) {
    'worklet';
    console.log(`${greeting} from the UI thread`);
}

function onPress() {
    // highlight-next-line
    runOnUI(myWorklet)('Howdy');
}
```

## to workletize {#to-workletize}

Преобразование JavaScript-функции в сериализуемый объект, который может быть скопирован и запущен на [UI thread](glossary.md#ui-thread).

Функции, помеченные директивой `"worklet";`, автоматически подхватываются и обрабатываются плагином Reanimated Babel.

## JavaScript-поток {#javascript-thread}

JavaScript-поток (или сокращенно JS-поток) отвечает за выполнение JavaScript-кода в приложении.

Это основное место, где выполняется код приложения React Native.

## UI thread {#ui-thread}

Поток UI отвечает за обработку обновлений пользовательского интерфейса. Также известен как основной поток.

Подробнее о нем можно узнать, прочитав статью [Threading model](../../../rn/threading-model.md) в официальной документации React Native.

## Реанимированный плагин Babel {#reanimated-babel-plugin}

Плагин выполняет автоматическую [workletization](glossary.md#to-workletize) некоторых функций, используемых в Reanimated, чтобы уменьшить количество кодового кода.

Подробности можно узнать, прочитав [Reanimated Babel plugin README](https://github.com/software-mansion/react-native-reanimated/blob/main/plugin/README.md).

## Ссылки

-   [Glossary of terms](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/glossary/)
