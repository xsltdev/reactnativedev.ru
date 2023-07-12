# ToastAndroid

API `ToastAndroid` в React Native раскрывает модуль `ToastAndroid` платформы Android как JS-модуль. Он предоставляет метод `show(message, duration)`, который принимает следующие параметры:

-   _message_ Строка с текстом тоста.
-   _duration_ Продолжительность тоста - либо `ToastAndroid.SHORT`, либо `ToastAndroid.LONG`.

Вы можете использовать `showWithGravity(message, duration, gravity)`, чтобы указать место появления тоста в макете экрана. Это может быть `ToastAndroid.TOP`, `ToastAndroid.BOTTOM` или `ToastAndroid.CENTER`.

Метод 'showWithGravityAndOffset(message, duration, gravity, xOffset, yOffset)' добавляет возможность указать смещение в пикселях.

<div data-snack-id="@bndby/toast-android-api-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `show()`

```ts
static show(message: string, duration: number);
```

### `showWithGravity()`

```ts
static showWithGravity(message: string, duration: number, gravity: number);
```

### `showWithGravityAndOffset()`

```ts
static showWithGravityAndOffset(
  message: string,
  duration: number,
  gravity: number,
  xOffset: number,
  yOffset: number,
);
```

## Свойства

### `SHORT`

Указывает продолжительность на экране.

```ts
static SHORT: number;
```

### `LONG`

Указывает продолжительность на экране.

```ts
static LONG: number;
```

### `TOP`

Указывает положение на экране.

```ts
static TOP: number;
```

### `BOTTOM`

Указывает положение на экране.

```ts
static BOTTOM: number;
```

### `CENTER`

Указывает положение на экране.

```ts
static CENTER: number;
```
