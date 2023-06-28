# Vibration

Вибрация устройства.

## Пример

<div data-snack-id="@bndby/vibration-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

!!!warning "Android"

    Приложения Android должны запрашивать разрешение `android.permission.VIBRATE`, добавив `<uses-permission android:name="android.permission.VIBRATE"/>` в `AndroidManifest.xml`.

!!!warning "iOS"

    API вибрации реализован как вызов `AudioServicesPlaySystemSound(kSystemSoundID_Vibrate)` на iOS.

## Методы

### `cancel()`

```tsx
static cancel();
```

Вызовите эту функцию, чтобы остановить вибрацию после вызова `vibrate()` с включенным повторением.

### `vibrate()`

```tsx
static vibrate(
  pattern?: number | number[],
  repeat?: boolean
);
```

Запускает вибрацию с фиксированной продолжительностью.

**На Android,** длительность вибрации по умолчанию составляет 400 миллисекунд, а произвольную длительность вибрации можно задать, передав число в качестве значения аргумента `pattern`. **На iOS,** длительность вибрации фиксирована и составляет примерно 400 миллисекунд.

Метод `vibrate()` может принимать аргумент `pattern` с массивом чисел, представляющих время в миллисекундах. Вы можете установить `repeat` в true, чтобы повторять паттерн вибрации в цикле до вызова `cancel()`.

**На Android,** нечетные индексы массива `pattern` представляют длительность вибрации, а четные — время разделения. **На iOS,** числа в массиве `pattern` представляют время разделения, поскольку длительность вибрации фиксирована.

**Параметры:**

| Name    | Type                                         | Default | Description                                                                                       |
| ------- | -------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------- |
| pattern | number :simple-android:<hr/>array of numbers | `400`   | Vibration duration in milliseconds.<hr/>Vibration pattern as an array of numbers in milliseconds. |
| repeat  | boolean                                      | `false` | Repeat vibration pattern until `cancel()`.                                                        |
