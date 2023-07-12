# Dimensions

!!!warning ""

    [`useWindowDimensions`](usewindowdimensions.md) - это предпочтительный API для компонентов React. В отличие от `Dimensions`, он обновляется по мере обновления размеров окна. Это хорошо сочетается с парадигмой React.

```ts
import { Dimensions } from 'react-native';
```

Вы можете получить ширину и высоту окна приложения с помощью следующего кода:

```ts
const windowWidth = Dimensions.get('window').width;
const windowHeight = Dimensions.get('window').height;
```

!!!warning ""

    Хотя размеры доступны сразу, они могут меняться (например, из-за поворота устройства, складывания устройства и т.д.), поэтому любая логика рендеринга или стили, которые зависят от этих констант, должны пытаться вызывать эту функцию при каждом рендеринге, а не кэшировать значение (например, использовать встроенные стили, а не устанавливать значение в `StyleSheet`).

Если вы нацелены на складные устройства или устройства, которые могут изменять размер экрана или размер окна приложения, вы можете использовать слушатель событий, доступный в модуле Dimensions, как показано в примере ниже.

## Пример

<div data-snack-id="@bndby/dimensions" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `addEventListener()`

```ts
static addEventListener(
  type: 'change',
  handler: ({
    window,
    screen,
  }: DimensionsValue) => void,
): EmitterSubscription;
```

Добавьте обработчик события. Поддерживаемые события:

-   `change`: Срабатывает при изменении свойства в объекте `Dimensions`. Аргументом обработчика события является объект типа `DimensionsValue`.

### `get()`

```ts
static get(dim: 'window' | 'screen'): ScaledSize;
```

Начальные размеры устанавливаются до вызова `runApplication`, поэтому они должны быть доступны до запуска любых других require'ов, но могут быть обновлены позже.

Пример: `const {height, width} = Dimensions.get('window');`

**Параметры:**

| Name           | Type   | Description                                                                       |
| -------------- | ------ | --------------------------------------------------------------------------------- |
| dim (Required) | string | Name of dimension as defined when calling `set`. Returns value for the dimension. |

!!!warning ""

    Для Android размер `window` будет исключать размер, используемый `status bar` (если он не полупрозрачный) и `bottom navigation bar`.

## Определения типа

### DimensionsValue

**Свойства:**

| Name   | Type         | Description                             |
| ------ | ------------ | --------------------------------------- |
| window | `ScaledSize` | Size of the visible Application window. |
| screen | `ScaledSize` | Size of the device's screen.            |

### ScaledSize

| Type   |
| ------ |
| object |

**Свойства:**

| Name      | Type   |
| --------- | ------ |
| width     | number |
| height    | number |
| scale     | number |
| fontScale | number |
