# Alert

Запускает диалоговое окно предупреждения с указанным заголовком и сообщением.

Опционально предоставьте список кнопок. При нажатии на любую кнопку сработает соответствующий обратный вызов onPress и оповещение будет отключено. По умолчанию единственной кнопкой будет кнопка 'OK'.

Это API, который работает как на Android, так и на iOS и может показывать статические оповещения. Оповещения, предлагающие пользователю ввести некоторую информацию, доступны только на iOS.

## Пример

<div data-snack-id="@bndby/alert-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## iOS

На iOS вы можете указать любое количество кнопок. Каждая кнопка по желанию может задавать стиль или быть подчеркнутой, доступные опции представлены перечислением `AlertButtonStyle` и полем `isPreferred` в `AlertButton`.

## Android

В Android можно указать не более трех кнопок. В Android есть понятие нейтральной, отрицательной и положительной кнопки:

-   Если вы укажете одну кнопку, она будет 'положительной' (например, 'OK').
-   Две кнопки означают 'отрицательный', 'положительный' (например, 'Отмена', 'OK')
-   Три кнопки означают 'нейтральный', 'отрицательный', 'положительный' (например, 'Позже', 'Отмена', 'OK')

Оповещения на Android можно отменить, нажав на кнопку за пределами поля оповещения. По умолчанию эта функция отключена, но ее можно включить, предоставив дополнительный параметр `Options` со свойством `cancelable`, установленным в `true`, т. е. `{cancelable: true}`.

Событие отмены может быть обработано путем предоставления свойства обратного вызова `onDismiss` внутри параметра `options`.

### Пример :simple-android:

<div data-snack-id="@bndby/alert-android-dissmissable-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `alert()`

```tsx
static alert (
  title: string,
  message?: string,
  buttons?: AlertButton[],
  options?: AlertOptions,
);
```

**Параметры:**

| Name             | Type          | Description                                                             |
| ---------------- | ------------- | ----------------------------------------------------------------------- |
| title (Required) | string        | The dialog's title. Passing `null` or empty string will hide the title. |
| message          | string        | An optional message that appears below the dialog's title.              |
| buttons          | `AlertButton` | An optional array containing buttons configuration.                     |
| options          | `Options`     | An optional Alert configuration.                                        |

### `prompt()` :simple-ios:

```tsx
static prompt: (
  title: string,
  message?: string,
  callbackOrButtons?: ((text: string) => void) | AlertButton[],
  type?: AlertType,
  defaultValue?: string,
  keyboardType?: string,
);
```

Создайте и выведите на экран подсказку для ввода некоторого текста в форме `Alert`.

**Параметры:**

| Name              | Type                       | Description                                                                                                                                                                                           |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| title (Required)  | string                     | The dialog's title.                                                                                                                                                                                   |
| message           | string                     | An optional message that appears above the text input.                                                                                                                                                |
| callbackOrButtons | function<hr/>`AlertButton` | If passed a function, it will be called with the prompt's value<br/>`(text: string) => void`, when the user taps 'OK'.<hr/>If passed an array, buttons will be configured based on the array content. |
| type              | `AlertType`                | This configures the text input.                                                                                                                                                                       |
| defaultValue      | string                     | The default text in text input.                                                                                                                                                                       |
| keyboardType      | string                     | The keyboard type of first text field (if exists). One of TextInput [`keyboardTypes`](../components/textinput.md#keyboardtype).                                                                       |
| options           | `Options`                  | An optional Alert configuration.                                                                                                                                                                      |

## Определения типа

### AlertButtonStyle :simple-ios:

Стиль кнопки оповещения iOS.

| Type |
| ---- |
| enum |

**Константы:**

| Value           | Description               |
| --------------- | ------------------------- |
| `'default'`     | Default button style.     |
| `'cancel'`      | Cancel button style.      |
| `'destructive'` | Destructive button style. |

### AlertType :simple-ios:

Тип оповещения iOS.

| Type |
| ---- |
| enum |

**Константы:**

| Value              | Description                  |
| ------------------ | ---------------------------- |
| `'default'`        | Default alert with no inputs |
| `'plain-text'`     | Plain text input alert       |
| `'secure-text'`    | Secure text input alert      |
| `'login-password'` | Login and password alert     |

### AlertButton

Объект, описывающий конфигурацию кнопки в оповещении.

| Type             |
| ---------------- |
| array of objects |

**Свойства объектов:**

| Name                     | Type               | Description                                                                    |
| ------------------------ | ------------------ | ------------------------------------------------------------------------------ |
| text                     | string             | Button label.                                                                  |
| onPress                  | function           | Callback function when button is pressed.                                      |
| style :simple-ios:       | `AlertButtonStyle` | Button style, on Android this property will be ignored.                        |
| isPreferred :simple-ios: | boolean            | Whether button should be emphasized, on Android this property will be ignored. |

### AlertOptions

| Type   |
| ------ |
| object |

**Свойства:**

| Name                            | Type     | Description                                                                                                               |
| ------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------- |
| cancelable :simple-android:     | boolean  | Defines if alert can be dismissed by tapping outside of the alert box.                                                    |
| userInterfaceStyle :simple-ios: | string   | The interface style used for the alert, can be set to `light` or `dark`, otherwise the default system style will be used. |
| onDismiss :simple-android:      | function | Callback function fired when alert has been dismissed.                                                                    |
