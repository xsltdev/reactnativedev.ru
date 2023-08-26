---
description: Анимация входа/выхода позволяет анимировать элементы при их добавлении в иерархию представления или удалении из нее
---

# Анимации входа/выхода

Анимации входа/выхода позволяют анимировать элементы при их добавлении и удалении из иерархии представления.

Reanimated поставляется с кучей предопределенных анимаций, которые можно настраивать. Для более сложных случаев можно использовать [Keyframes](keyframe-animations.md) или создавать собственные [пользовательские анимации входа/выхода](custom-animations.md).

## Fade

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/fade_light.mov" />
</video>

`FadeX` позволяет создать анимацию затухания.

```js
import { FadeIn, FadeOut } from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={FadeIn}
            exiting={FadeOut}
        />
    );
}
```

Доступные анимации затухания:

-   Вход: `FadeIn`, `FadeInRight`, `FadeInLeft`, `FadeInUp`, `FadeInDown`.
-   Выход: `FadeOut`, `FadeOutRight`, `FadeOutLeft`, `FadeOutUp`, `FadeOutDown`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя            | Конфигурация                                     |
| -------------- | ------------------------------------------------ |
| `FadeIn`       | `{opacity: 0}`                                   |
| `FadeInDown`   | `{opacity: 0, transform: [{ translateY: 25 }]}`  |
| `FadeInLeft`   | `{opacity: 0, transform: [{ translateX: -25 }]}` |
| `FadeInRight`  | `{opacity: 0, transform: [{ translateX: 25 }]}`  |
| `FadeInUp`     | `{opacity: 0, transform: [{ translateY: -25 }]}` |
| `FadeOut`      | `{opacity: 1}`                                   |
| `FadeOutDown`  | `{opacity: 1, transform: [{ translateY: 0 }]}`   |
| `FadeOutLeft`  | `{opacity: 1, transform: [{ translateX: 0 }]}`   |
| `FadeOutRight` | `{opacity: 1, transform: [{ translateX: 0 }]}`   |
| `FadeOutUp`    | `{opacity: 1, transform: [{ translateY: 0 }]}`   |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```js
FadeOutLeft.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: число)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, используют функцию [`withSpring`](../animations/withSpring.md).

```js
FadeInUp.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```js
FadeInDown.delay(500)
    .randomDelay()
    .withInitialValues({ transform: [{ translateY: 420 }] })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Bounce

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/bounce_light.mov" />
</video>

`BounceX` позволяет создать анимацию прыжков.

```jsx
import {
    BounceIn,
    BounceOut,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={BounceIn}
            exiting={BounceOut}
        />
    );
}
```

Доступные анимации отскока:

-   Ввод: `BounceIn`, `BounceInRight`, `BounceInLeft`, `BounceInUp`, `BounceInDown`.
-   Выход: `BounceOut`, `BounceOutRight`, `BounceOutLeft`, `BounceOutUp`, `BounceOutDown`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя              | Конфигурация                                          |
| ---------------- | ----------------------------------------------------- |
| `BounceIn`       | `{transform: [{ scale: 0 }]}`                         |
| `BounceInRight`  | `{transform: [{ translateX: values.windowWidth }]}`   |
| `BounceInLeft`   | `{transform: [{ translateX: -values.windowWidth }]}`  |
| `BounceInUp`     | `{transform: [{ translateY: -values.windowHeight }]}` |
| `BounceInDown`   | `{transform: [{ translateY: values.windowHeight}]}`   |
| `BounceOut`      | `{transform: [{ scale: 1 }]}`                         |
| `BounceOutRight` | `{transform: [{ translateX: 0 }]}`                    |
| `BounceOutLeft`  | `{transform: [{ translateX: 0 }]}`                    |
| `BounceOutUp`    | `{transform: [{ translateY: 0 }]}`                    |
| `BounceOutDown`  | `{transform: [{ translateY: 0 }]}`                    |

### Модификаторы

```javascript
BounceInDown.duration(500)
    .delay(500)
    .randomDelay()
    .withInitialValues({
        transform: [{ translateY: -420 }],
    })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равно `600`.
-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и заданной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Flip

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/flip_light.mov" />
</video>

`FlipX` позволяет создавать анимацию, основанную на вращении по определенной оси.

```jsx
import {
    FlipInEasyX,
    FlipOutEasyX,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={FlipInEasyX}
            exiting={FlipOutEasyX}
        />
    );
}
```

Доступные анимации переворачивания:

-   Ввод: `FlipInEasyX`, `FlipInEasyY`, `FlipInXDown`, `FlipInXUp`, `FlipInYLeft`, `FlipInYRight`.
-   Выход: `FlipOutEasyX`, `FlipOutEasyY`, `FlipOutXDown`, `FlipOutXUp`, `FlipOutYLeft`, `FlipOutYRight`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя             | Конфигурация                                                                                             |
| --------------- | -------------------------------------------------------------------------------------------------------- |
| `FlipInEasyX`   | `{transform: [{ perspective: 500 }, { rotateX: '90deg' }]}`                                              |
| `FlipInEasyY`   | `{transform: [{ perspective: 500 }, { rotateY: '90deg' }]}`                                              |
| `FlipInXDown`   | `{transform: [{ perspective: 500 }, { rotateX: '-90deg' }, { translateY: targetValues.targetHeight }]}`  |
| `FlipInXUp`     | `{transform: [{ perspective: 500 }, { rotateX: '90deg' }, { translateY: -targetValues.targetHeight }]}`  |
| `FlipInYLeft`   | `{transform: [{ perspective: 500 }, { rotateY: '-90deg' }, { translateX: -targetValues.targetWidth } ]}` |
| `FlipInYRight`  | `{transform: [{ perspective: 500 }, { rotateY: '90deg' }, { translateX: targetValues.targetWidth } ]}`   |
| `FlipOutEasyX`  | `{transform: [{ perspective: 500 }, { rotateX: '0deg' }]}`                                               |
| `FlipOutEasyY`  | `{transform: [{ perspective: 500 }, { rotateY: '0deg' }]}`                                               |
| `FlipOutXDown`  | `{transform: [{ perspective: 500 }, { rotateX: '0deg' }, { translateY: 0 }]}`                            |
| `FlipOutXUp`    | `{transform: [{ perspective: 500 }, { rotateX: '0deg' }, { translateY: 0 }]}`                            |
| `FlipOutYLeft`  | `{transform: [{ perspective: 500 }, { rotateY: '0deg' }, { translateX: 0 }]}`                            |
| `FlipOutYRight` | `{transform: [{ perspective: 500 }, { rotateY: '0deg' }, { translateX: 0 }]}`                            |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
FlipOutYLeft.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, используют функцию [`withSpring`](../animations/withSpring.md).

```javascript
FlipInXUp.springify()
    .damping(2)
    .mass(3)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
FlipInEasyY.delay(500)
    .randomDelay()
    .withInitialValues({
        transform: [
            { perspective: 100 },
            { rotateY: '123deg' },
        ],
    })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## LightSpeed

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/lightspeed_light.mov" />
</video>

`LightSpeedX` позволяет создать анимацию горизонтально движущегося объекта с изменением непрозрачности и перекоса.

```jsx
import {
    LightSpeedInRight,
    LightSpeedOutLeft,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={LightSpeedInRight}
            exiting={LightSpeedOutLeft}
        />
    );
}
```

Доступные анимации скорости света:

-   Вход: `LightSpeedInRight`, `LightSpeedInLeft`.
-   Выход: `LightSpeedOutRight`, `LightSpeedOutLeft`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя                  | Конфигурация                                                                         |
| -------------------- | ------------------------------------------------------------------------------------ |
| `LightSpeedInLeft`   | `{opacity: 0, transform: [{ translateX: -values.windowWidth }, { skewX: '45deg' }]}` |
| `LightSpeedInRight`  | `{opacity: 0, transform: [{ translateX: values.windowWidth }, { skewX: '-45deg' }]}` |
| `LightSpeedOutLeft`  | `{opacity: 1, transform: [{ translateX: 0 }, { skewX: '0deg' }]}`                    |
| `LightSpeedOutRight` | `{opacity: 1, transform: [{ translateX: 0 }, { skewX: '0deg' }]}`                    |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
LightSpeedOutLeft.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, используют функцию [`withSpring`](../animations/withSpring.md).

```javascript
LightSpeedInLeft.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
LightSpeedInRight.delay(500)
    .randomDelay()
    .withInitialValues({
        transform: [
            { translateX: -100 },
            { skewX: '-10deg' },
        ],
    })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Pinwheel

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/pinwheel_light.mov" />
</video>

`PinwheelX` позволяет создавать анимацию на основе вращения, масштаба и непрозрачности.

```jsx
import {
    PinwheelIn,
    PinwheelOut,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={PinwheelIn}
            exiting={PinwheelOut}
        />
    );
}
```

Доступные анимации колес:

-   Ввод: `PinwheelIn`
-   Выход: `PinwheelOut`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя           | Конфигурация                                             |
| ------------- | -------------------------------------------------------- |
| `PinwheelIn`  | `{opacity: 0, transform: [{ scale: 0 }, {rotate: '5'}]}` |
| `PinwheelOut` | `{opacity: 1, transform: [{ scale: 1 }, {rotate: '0'}]}` |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
PinwheelOut.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, ретранслируют функцию [`withSpring`](../animations/withSpring.md).

```javascript
PinwheelIn.springify()
    .damping(2)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
PinwheelIn.delay(500)
    .randomDelay()
    .withInitialValues({
        transform: [{ scale: 0.8 }, { rotate: '3' }],
    })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Roll

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/roll_light.mov" />
</video>

`RollX` позволяет создать анимацию горизонтально движущегося объекта с вращением.

```jsx
import {
    RollInRight,
    RollOutLeft,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={RollInRight}
            exiting={RollOutLeft}
        />
    );
}
```

Доступные анимации роликов:

-   Вход: `RollInRight`, `RollInLeft`.
-   Выход: `RollOutRight`, `RollOutLeft`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя            | Конфигурация                                                                |
| -------------- | --------------------------------------------------------------------------- |
| `RollInLeft`   | `{transform: [{ translateX: -values.windowWidth }, { rotate: '-180deg' }]}` |
| `RollInRight`  | `{transform: [{ translateX: values.windowWidth }, { rotate: '180deg' }]}`   |
| `RollOutLeft`  | `{transform: [{ translateX: 0 }, { rotate: '0deg' }]}`                      |
| `RollOutRight` | `{transform: [{ translateX: 0 }, { rotate: '0deg' }]}`                      |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
RollOutLeft.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, ретранслируют функцию [`withSpring`](../animations/withSpring.md).

```javascript
RollInLeft.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
RollInRight.delay(500)
    .randomDelay()
    .withInitialValues({
        transform: [
            { translateX: 100 },
            { rotate: '-45deg' },
        ],
    })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Rotate

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/rotate_light.mov" />
</video>

`RotateX` позволяет создать анимацию вращения.

```jsx
import {
    RotateInDownLeft,
    RotateOutDownLeft,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={RotateInDownLeft}
            exiting={RotateOutDownLeft}
        />
    );
}
```

Доступные анимации поворота:

-   Ввод: `RotateInDownLeft`, `RotateInDownRight`, `RotateInUpLeft`, `RotateInUpRight`.
-   Выход: `RotateOutDownLeft`, `RotateOutDownRight`, `RotateOutUpLeft`, `RotateOutUpRight`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя                  | Конфигурация                                                                                                                                                                               |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `RotateInDownLeft`   | `{opacity: 0, transform: [{ rotate: '-90deg' }, { translateX: values.targetWidth / 2 - values.targetHeight / 2 }, { translateY: -(values.targetWidth / 2 - values.targetHeight / 2) }]}`   |
| `RotateInDownRight`  | `{opacity: 0, transform: [{ rotate: '90deg' }, { translateX: -(values.targetWidth / 2 - values.targetHeight / 2) }, { translateY: -(values.targetWidth / 2 - values.targetHeight / 2) }]}` |
| `RotateInUpLeft`     | `{opacity: 0, transform: [{ rotate: '90deg' }, { translateX: values.targetWidth / 2 - values.targetHeight / 2 }, { translateY: values.targetWidth / 2 - values.targetHeight / 2 }]}`       |
| `RotateInUpRight`    | `{opacity: 0, transform: [{ rotate: '-90deg' }, { translateX: -(values.targetWidth / 2 - values.targetHeight / 2) }, { translateY: values.targetWidth / 2 - values.targetHeight / 2 }]}`   |
| `RotateOutDownLeft`  | `{opacity: 1, transform: [{ rotate: '0deg' }, { translateX: 0 }, { translateY: 0 }]}`                                                                                                      |
| `RotateOutDownRight` | `{opacity: 1, transform: [{ rotate: '0deg' }, { translateX: 0 }, { translateY: 0 }]}`                                                                                                      |
| `RotateOutUpLeft`    | `{opacity: 1, transform: [{ rotate: '0deg' }, { translateX: 0 }, { translateY: 0 }]}`                                                                                                      |
| `RotateOutUpRight`   | `{opacity: 1, transform: [{ rotate: '0deg' }, { translateX: 0 }, { translateY: 0 }]}`                                                                                                      |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
RotateOutDownRight.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, ретранслируют функцию [`withSpring`](../animations/withSpring.md).

```javascript
RotateInUpLeft.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
RotateInDownLeft.delay(500)
    .randomDelay()
    .withInitialValues({
        transform: [
            { rotate: '-90deg' },
            { translateX: 100 },
            { translateY: 100 },
        ],
    })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Slide

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/slide_light.mov" />
</video>

`SlideX` позволяет создать анимацию горизонтально или вертикально движущегося объекта.

```jsx
import {
    SlideInRight,
    SlideOutLeft,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={SlideInRight}
            exiting={SlideOutLeft}
        />
    );
}
```

Available slide animations:

-   Entering: `SlideInRight`, `SlideInLeft`, `SlideInUp`, `SlideInDown`
-   Exiting: `SlideOutRight`, `SlideOutLeft`, `SlideOutUp`, `SlideOutDown`

These are the initial values for each animation that can be customized with the `withInitialValues` modifier.

| Name            | Config                                                  |
| --------------- | ------------------------------------------------------- |
| `SlideInDown`   | `{originY: values.targetOriginY + values.windowHeight}` |
| `SlideInLeft`   | `{originX: values.targetOriginX - values.windowWidth}`  |
| `SlideInRight`  | `{originX: values.targetOriginX + values.windowWidth}`  |
| `SlideInUp`     | `{originY: -values.windowHeight}`                       |
| `SlideOutDown`  | `{originY: values.currentOriginY}`                      |
| `SlideOutLeft`  | `{originX: values.currentOriginX}`                      |
| `SlideOutRight` | `{originX: values.currentOriginX}`                      |
| `SlideOutUp`    | `{originY: values.currentOriginY}`                      |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
SlideOutLeft.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, ретранслируют функцию [`withSpring`](../animations/withSpring.md).

```javascript
SlideInUp.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
SlideInDown.delay(500)
    .randomDelay()
    .withInitialValues({ transform: [{ translateY: 420 }] })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Stretch

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/stretch_light.mov" />
</video>

`StretchX` позволяет создать анимацию, основанную на масштабировании по оси `X` или `Y`.

```jsx
import {
    StretchInX,
    StretchOutY,
} from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={StretchInX}
            exiting={StretchOutY}
        />
    );
}
```

Доступные анимации растяжения:

-   Ввод: `StretchInX`, `StretchInY`.
-   Выход: `StretchOutX`, `StretchOutY`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя           | Конфигурация                   |
| ------------- | ------------------------------ |
| `StretchInX`  | `{transform: [{ scaleX: 0 }]}` |
| `StretchInY`  | `{transform: [{ scaleY: 0 }]}` |
| `StretchOutX` | `{transform: [{ scaleX: 1 }]}` |
| `StretchOutY` | `{transform: [{ scaleY: 1 }]}` |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
StretchOutX.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, ретранслируют функцию [`withSpring`](../animations/withSpring.md).

```javascript
StretchInX.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(5);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
StretchInY.delay(500)
    .randomDelay()
    .withInitialValues({ transform: [{ scaleY: 0.5 }] })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Zoom

<video playsInline autoPlay muted loop>
<source src="/community/reanimated.3/layout-animations/zoom_light.mov" />
</video>

`ZoomX` позволяет создать анимацию, основанную на масштабе.

```jsx
import { ZoomIn, ZoomOut } from 'react-native-reanimated';

function App() {
    return (
        <Animated.View
            entering={ZoomIn}
            exiting={ZoomOut}
        />
    );
}
```

Доступные анимации масштабирования:

-   Вход: `ZoomIn`, `ZoomInDown`, `ZoomInEasyDown`, `ZoomInEasyUp`, `ZoomInLeft`, `ZoomInRight`, `ZoomInRotate`, `ZoomInUp`.
-   Выход: `ZoomOut`, `ZoomOutDown`, `ZoomOutEasyDown`, `ZoomOutEasyUp`, `ZoomOutLeft`, `ZoomOutRight`, `ZoomOutRotate`, `ZoomOutUp`.

Это начальные значения для каждой анимации, которые могут быть настроены с помощью модификатора `withInitialValues`.

| Имя               | Конфигурация                                                        |
| ----------------- | ------------------------------------------------------------------- |
| `ZoomIn`          | `{transform: [{ scale: 0 }]}`                                       |
| `ZoomInDown`      | `{transform: [{ translateY: values.windowHeight }, { scale: 0 }]}`  |
| `ZoomInEasyDown`  | `{transform: [{ translateY: values.targetHeight }, { scale: 0 }]}`  |
| `ZoomInEasyUp`    | `{transform: [{ translateY: -values.targetHeight }, { scale: 0 }]}` |
| `ZoomInLeft`      | `{transform: [{ translateX: -values.windowWidth }, { scale: 0 }]}`  |
| `ZoomInRight`     | `{transform: [{ translateX: values.windowWidth }, { scale: 0 }]}`   |
| `ZoomInRotate`    | `{transform: [{ scale: 0 }, { rotate: rotate }]}`                   |
| `ZoomInUp`        | `{transform: [{ translateY: -values.windowHeight }, { scale: 0 }]}` |
| `ZoomOut`         | `{transform: [{ scale: 1 }]}`                                       |
| `ZoomOutDown`     | `{transform: [{ translateY: 0 }, { scale: 1 }]}`                    |
| `ZoomOutEasyDown` | `{transform: [{ translateY: 0 }, { scale: 1 }]}`                    |
| `ZoomOutEasyUp`   | `{transform: [{ translateY: 0 }, { scale: 1 }]}`                    |
| `ZoomOutLeft`     | `{transform: [{ translateX: 0 }, { scale: 1 }]}`                    |
| `ZoomOutRight`    | `{transform: [{ translateX: 0 }, { scale: 1 }]}`                    |
| `ZoomOutRotate`   | `{transform: [{ scale: 1 }, { rotate: '0' }]}`                      |
| `ZoomOutUp`       | `{transform: [{ translateY: 0 }, { scale: 1 }]}`                    |

### Модификаторы

#### Time-based

Модификаторы, основанные на времени, ретранслируют функцию [`withTiming`](../animations/withTiming.md).

```javascript
ZoomOutLeft.duration(500).easing(Easing.ease);
```

-   `.duration(durationMs: number)` - продолжительность анимации (в миллисекундах). По умолчанию равна `300`.
-   `.easing(easingFunction: EasingFunction)` - функция easing, определяющая кривую анимации. По умолчанию `Easing.inOut(Easing.quad)`.

!!!note ""

    Временные модификаторы не имеют эффекта при использовании `.springify()`.

#### Spring-based

Модификаторы, основанные на пружинах, ретранслируют функцию [`withSpring`](../animations/withSpring.md).

```javascript
ZoomInRotate.springify()
    .damping(30)
    .mass(5)
    .stiffness(10)
    .overshootClamping(false)
    .restDisplacementThreshold(0.1)
    .restSpeedThreshold(0.1);
```

-   `.springify()` включает конфигурацию анимации на основе пружин.
-   `.damping(value: number)` определяет, как быстро пружина прекращает движение. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя. По умолчанию `10`.
-   `.mass( value: number)` - это масса пружины. Уменьшение этого значения ускоряет анимацию. По умолчанию `1`.
-   `.stiffness(value: number)` определяет, насколько пружина упруга. По умолчанию `100`.
-   `.overshootClamping(value: boolean)` определяет, может ли пружина перескочить через заданное положение. По умолчанию `false`.
-   `.restDisplacementThreshold( value: number)` - смещение, ниже которого пружина будет защелкиваться в заданном положении без дальнейших колебаний. По умолчанию `0.001`.
-   `.restSpeedThreshold( value: number)` - скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться в заданное положение без дальнейших колебаний. По умолчанию `2`.

#### Common

```javascript
ZoomIn.delay(500)
    .randomDelay()
    .withInitialValues({ transform: [{ scale: 0.5 }] })
    .withCallback((finished) => {
        console.log(
            `finished without interruptions: ${finished}`
        );
    });
```

-   `.delay(durationMs: number)` - задержка перед началом анимации (в миллисекундах). По умолчанию равна `0`.
-   `.randomDelay()` рандомизирует задержку анимации между `0` и указанной задержкой. Используется 1000 мс, если задержка не была указана.
-   `.withInitialValues(values: StyleProps)` позволяет переопределить начальную конфигурацию анимации.
-   `.withCallback(callback: (finished: boolean) => void)` - обратный вызов, который будет выполнен после завершения анимации. Устанавливает значение `finished` в `true`, если анимация завершается без прерываний, и `false` в противном случае.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ❌  |

## ССылки

-   [Entering/Exiting animations](https://docs.swmansion.com/react-native-reanimated/docs/layout-animations/entering-exiting-animations)
