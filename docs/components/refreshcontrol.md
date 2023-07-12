# RefreshControl

Этот компонент используется внутри `ScrollView` или `ListView` для добавления функции "pull to refresh". Когда `ScrollView` находится на `scrollY: 0`, пролистывание вниз вызывает событие `onRefresh`.

## Пример

<div data-snack-id="@bndby/refreshcontrol" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

!!!warning "Примечание"

    `refreshing` — это управляемый параметр, поэтому его нужно установить в `true` в функции `onRefresh`, иначе индикатор обновления остановится немедленно.

## Реквизиты

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `refreshing` (обязательно)

Должно ли представление указывать на активное обновление.

| Type    |
| ------- |
| boolean |

### `colors` :simple-android:

Цвета (хотя бы один), которые будут использоваться для рисования индикатора обновления.

| Type                                   |
| -------------------------------------- |
| array of [colors](../guides/colors.md) |

### `enabled` :simple-android:

Включена ли функция pull to refresh.

| Type    | Default |
| ------- | ------- |
| boolean | `true`  |

### `onRefresh`

Вызывается, когда представление начинает обновляться.

| Type     |
| -------- |
| function |

### `progressBackgroundColor` :simple-android:

Цвет фона индикатора обновления.

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |

### `progressViewOffset`

Вид прогресса верхнее смещение.

| Type   | Default |
| ------ | ------- |
| number | `0`     |

### `size` :simple-android:

Размер индикатора обновления.

| Type                         | Default     |
| ---------------------------- | ----------- |
| enum(`'default'`, `'large'`) | `'default'` |

### `tintColor` :simple-ios:

Цвет индикатора обновления.

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |

### `title` :simple-ios:

Заголовок, отображаемый под индикатором обновления.

| Type   |
| ------ |
| string |

### `titleColor` :simple-ios:

Цвет заголовка индикатора обновления.

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |
