---
description: Модуль Easing реализует общие функции перемещения. Этот модуль используется Animated.timing() для передачи физически правдоподобного движения в анимации
---

# Easing

Модуль **`Easing`** реализует общие функции перемещения. Этот модуль используется [`Animated.timing()`](animated.md#timing) для передачи физически правдоподобного движения в анимации.

Визуализацию некоторых распространенных функций перемещения можно найти на [сайте](https://easings.net/).

## Предопределенные анимации {#predefined-animations}

Модуль `Easing` предоставляет несколько предопределенных анимаций через следующие методы:

-   [`back`](easing.md#back) обеспечивает базовую анимацию, при которой объект слегка отходит назад перед движением вперед.
-   [`bounce`](easing.md#bounce) обеспечивает анимацию подпрыгивания
-   [`ease`](easing.md#ease) обеспечивает базовую инерционную анимацию
-   [`elastic`](easing.md#elastic) обеспечивает базовое взаимодействие пружины

## Стандартные функции {#standard-functions}

Предоставляются три стандартные функции перемещения:

-   [`linear`](easing.md#linear)
-   [`quad`](easing.md#quad)
-   [`cubic`](easing.md#cubic)

Функция [`poly`](easing.md#poly) может быть использована для реализации квартовых, квинтовых и других функций высшей силы.

## Дополнительные функции {#additional-functions}

Дополнительные математические функции предоставляются следующими методами:

-   [`bezier`](easing.md#bezier) обеспечивает кубическую кривую Безье
-   [`circle`](easing.md#circle) предоставляет круговую функцию
-   [`sin`](easing.md#sin) предоставляет синусоидальную функцию
-   [`exp`](easing.md#exp) предоставляет экспоненциальную функцию

Следующие помощники используются для модификации других функций easing.

-   [`in`](easing.md#in) запускает функцию перемещения вперед
-   [`inOut`](easing.md#inout) делает любую функцию перемещения симметричной
-   [`out`](easing.md#out) запускает функцию перемещения назад

## Пример {#example}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-easing-demo" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-easing-demo" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы {#methods}

### step0()

```ts
static step0(n: number);
```

Пошаговая функция, возвращает `1` для любого положительного значения `n`.

### step1()

```ts
static step1(n: number);
```

Пошаговая функция, возвращает `1`, если `n` больше или равно `1`.

### linear()

```ts
static linear(t: number);
```

Линейная функция, `f(t) = t`. Позиция коррелирует с прошедшим временем один к одному.

[https://cubic-bezier.com/#0,0,1,1](https://cubic-bezier.com/#0,0,1,1)

### ease()

```ts
static ease(t: number);
```

Базовое инерционное взаимодействие, подобное тому, как объект медленно разгоняется до скорости.

[https://cubic-bezier.com/#.42,0,1,1](https://cubic-bezier.com/#.42,0,1,1)

### quad()

```ts
static quad(t: number);
```

Квадратичная функция, `f(t) = t * t`. Позиция равна квадрату прошедшего времени.

[https://easings.net/#easeInQuad](https://easings.net/#easeInQuad)

### cubic()

```ts
static cubic(t: number);
```

Кубическая функция, `f(t) = t * t * t`. Позиция равна кубу прошедшего времени.

[https://easings.net/#easeInCubic](https://easings.net/#easeInCubic)

### poly()

```ts
static poly(n: number);
```

Функция мощности. Позиция равна N-ой мощности прошедшего времени.

n = 4: [https://easings.net/#easeInQuart](https://easings.net/#easeInQuart) n = 5: [https://easings.net/#easeInQuint](https://easings.net/#easeInQuint)

### sin()

```ts
static sin(t: number);
```

Синусоидальная функция.

[https://easings.net/#easeInSine](https://easings.net/#easeInSine)

### circle()

```ts
static circle(t: number);
```

Круговая функция.

[https://easings.net/#easeInCirc](https://easings.net/#easeInCirc)

### exp()

```ts
static exp(t: number);
```

Экспоненциальная функция.

[https://easings.net/#easeInExpo](https://easings.net/#easeInExpo)

### `elastic()`

```ts
static elastic(bounciness: number);
```

Базовое упругое взаимодействие, похожее на пружину, колеблющуюся вперед-назад.

Выпуклость по умолчанию равна `1`, что приводит к небольшому проскакиванию один раз. Выпуклость `0` не проскакивает вообще, а выпуклость `N > 1` проскакивает примерно `N` раз.

[https://easings.net/#easeInElastic](https://easings.net/#easeInElastic)

### back()

```ts
static back(s)
```

Используется с `Animated.parallel()` для создания базового эффекта, когда объект слегка отступает назад, когда начинается анимация.

### bounce()

```ts
static bounce(t: number);
```

Обеспечивает базовый эффект подпрыгивания.

[https://easings.net/#easeInBounce](https://easings.net/#easeInBounce)

### bezier()

```ts
static bezier(x1: number, y1: number, x2: number, y2: number);
```

Предоставляет кубическую кривую Безье, эквивалентную `transition-timing-function` в CSS Transitions.

Полезный инструмент для визуализации кубических кривых Безье можно найти на [сайте](https://cubic-bezier.com/).

### in()

```ts
static in(easing: number);
```

Запускает функцию перемещения вперед.

### out()

```ts
static out(easing: number);
```

Выполняет функцию перемещения в обратном направлении.

### inOut()

```ts
static inOut(easing: number);
```

Делает любую функцию перемещения симметричной. Функция перемещения будет работать вперед в течение половины времени, а затем назад в течение оставшейся части времени.
