# KeyboardAvoidingView

Этот компонент будет автоматически регулировать свою высоту, положение или нижнюю подкладку в зависимости от высоты клавиатуры, чтобы оставаться видимым при отображении виртуальной клавиатуры.

## Пример

<div data-snack-id="@bndby/keyboardavoidingview" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## пропсы

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `behavior`

Укажите, как реагировать на присутствие клавиатуры.

!!!warning ""

    Android и iOS по-разному взаимодействуют с этим пропсом. И на iOS, и на Android рекомендуется устанавливать `behavior`.

| Type                                        |
| ------------------------------------------- |
| enum(`'height'`, `'position'`, `'padding'`) |

### `contentContainerStyle`

Стиль контейнера содержимого (`View`), когда поведение имеет значение `'position'`.

| Type                              |
| --------------------------------- |
| [View Style](view-style-props.md) |

### `enabled`

Включено или отключено `KeyboardAvoidingView`.

| Type    | Default |
| ------- | ------- |
| boolean | `true`  |

### `keyboardVerticalOffset`

Это расстояние между верхней частью экрана пользователя и нативным представлением реактора, в некоторых случаях может быть ненулевым.

| Type   | Default |
| ------ | ------- |
| number | `0`     |
