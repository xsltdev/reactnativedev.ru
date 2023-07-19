# ActivityIndicator

Отображает круговой индикатор загрузки.

## Пример

<div data-snack-id="@bndby/activityindicator-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## пропсы

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `animating`

Показывать ли индикатор (`true`) или скрывать его (`false`).

| Type | Default |
| ---- | ------- |
| bool | `true`  |

### `color`

Цвет переднего плана волчка.

| Type                         | Default                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------ |
| [color](../guides/colors.md) | `null` (system accent default color) :simple-android: <hr/> `'#999999'` :simple-ios: |

### `hidesWhenStopped` :simple-ios:

Должен ли индикатор скрываться, когда он не анимирован.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

### `size`

Размер индикатора.

| Type                                                   | Default   |
| ------------------------------------------------------ | --------- |
| enum(`'small'`, `'large'`)<hr/>number :simple-android: | `'small'` |
