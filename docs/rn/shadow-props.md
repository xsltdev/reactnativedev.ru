# Shadow Props

=== "Typescript"

    <div data-snack-id="@bndby/shadow-props-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "Javascript"

    <div data-snack-id="@bndby/shadow-props-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## пропсы

### `shadowColor`

Устанавливает цвет тени.

Это свойство работает только на Android API 28 и выше. Для получения аналогичной функциональности на более низких версиях Android API используйте свойство [`elevation`](view-style-props.md#elevation-android).

| Type               |
| ------------------ |
| [color](colors.md) |

### `shadowOffset` :simple-ios:

Устанавливает смещение падающей тени.

| Type                                     |
| ---------------------------------------- |
| `object: {width: number,height: number}` |

### `shadowOpacity` :simple-ios:

Устанавливает непрозрачность тени (умноженную на альфа-компоненту цвета).

| Type  |
| ----- |
| число |

### `shadowRadius` :simple-ios:

Устанавливает радиус размытия тени.

| Type  |
| ----- |
| Число |
