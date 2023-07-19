# StatusBar

Компонент для управления строкой состояния приложения. Строка состояния — это зона, обычно в верхней части экрана, где отображается текущее время, информация о сети Wi-Fi и сотовой связи, уровень заряда батареи и/или другие значки состояния.

## Использование с Навигатором

Возможно одновременное подключение нескольких компонентов `StatusBar`. пропсы будут объединены в том порядке, в котором компоненты `StatusBar` были смонтированы.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-statusbar-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-statusbar-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Imperative API

Для случаев, когда использование компонента не является идеальным, существует также императивный API, представленный в виде статических функций на компоненте. Однако не рекомендуется использовать статический API и компонент для одного и того же пропса, поскольку любое значение, установленное статическим API, будет переопределено значением, установленным компонентом при следующем рендеринге.

## Константы

### `currentHeight` :simple-android:

Высота строки состояния, которая включает высоту надписи, если она присутствует.

## пропсы

### `animated`

Если переход между изменениями свойств строки состояния должен быть анимированным. Поддерживается для свойств `backgroundColor`, `barStyle` и `hidden`.

| Type    | Required | Default |
| ------- | -------- | ------- |
| boolean | No       | `false` |

### `backgroundColor` :simple-android:

Цвет фона строки состояния.

| Type                         | Required | Default                                                                |
| ---------------------------- | -------- | ---------------------------------------------------------------------- |
| [color](../guides/colors.md) | No       | default system StatusBar background color, or `'black'` if not defined |

### `barStyle`

Устанавливает цвет текста строки состояния.

На Android это повлияет только на API версии 23 и выше.

| Type                                          | Required | Default     |
| --------------------------------------------- | -------- | ----------- |
| [StatusBarStyle](statusbar.md#statusbarstyle) | No       | `'default'` |

### `hidden`

Если строка состояния скрыта.

| Type    | Required | Default |
| ------- | -------- | ------- |
| boolean | No       | `false` |

### `networkActivityIndicatorVisible` :simple-ios:

Если индикатор сетевой активности должен быть виден.

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

### `showHideTransition` :simple-ios:

Эффект перехода при отображении и скрытии строки состояния с помощью свойства `hidden`.

| Type                                                  | Default  |
| ----------------------------------------------------- | -------- |
| [StatusBarAnimation](statusbar.md#statusbaranimation) | `'fade'` |

### `translucent` :simple-android:

Является ли строка состояния полупрозрачной. Если для параметра `translucent` установлено значение `true`, приложение будет рисовать под строкой состояния. Это полезно при использовании полупрозрачного цвета строки состояния.

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

## Методы

### `popStackEntry()`

```ts
static popStackEntry(entry: StatusBarProps);
```

Получение и удаление последней записи `StatusBar` из стека.

**Параметры:**

| Name             | Type | Description                           |
| ---------------- | ---- | ------------------------------------- |
| entry (Required) | any  | Entry returned from `pushStackEntry`. |

### `pushStackEntry()`

```ts
static pushStackEntry(props: StatusBarProps): StatusBarProps;
```

Переместить запись `StatusBar` в стек. Возвращаемое значение должно быть передано в `popStackEntry` после завершения.

**Параметры:**

| Name             | Type | Description                                                      |
| ---------------- | ---- | ---------------------------------------------------------------- |
| props (Required) | any  | Object containing the StatusBar props to use in the stack entry. |

### `replaceStackEntry()`

```ts
static replaceStackEntry(
  entry: StatusBarProps,
  props: StatusBarProps
): StatusBarProps;
```

Замените существующий элемент стека `StatusBar` новым пропсом.

**Параметры:**

| Name             | Type | Description                                                                  |
| ---------------- | ---- | ---------------------------------------------------------------------------- |
| entry (Required) | any  | Entry returned from `pushStackEntry` to replace.                             |
| props (Required) | any  | Object containing the StatusBar props to use in the replacement stack entry. |

### `setBackgroundColor()` :simple-android:

```ts
static setBackgroundColor(color: ColorValue, animated?: boolean);
```

Установите цвет фона для строки состояния.

**Параметры:**

| Name             | Type    | Description               |
| ---------------- | ------- | ------------------------- |
| color (Required) | string  | Background color.         |
| animated         | boolean | Animate the style change. |

### `setBarStyle()`

```ts
static setBarStyle(style: StatusBarStyle, animated?: boolean);
```

Установите стиль строки состояния.

**Параметры:**

| Name             | Type                                          | Description               |
| ---------------- | --------------------------------------------- | ------------------------- |
| style (Required) | [StatusBarStyle](statusbar.md#statusbarstyle) | Status bar style to set.  |
| animated         | boolean                                       | Animate the style change. |

### `setHidden()`

```ts
static setHidden(hidden: boolean, animation?: StatusBarAnimation);
```

Показать или скрыть строку состояния.

**Параметры:**

| Name                   | Type                                                  | Description                                             |
| ---------------------- | ----------------------------------------------------- | ------------------------------------------------------- |
| hidden (Required)      | boolean                                               | Hide the status bar.                                    |
| animation :simple-ios: | [StatusBarAnimation](statusbar.md#statusbaranimation) | Animation when changing the status bar hidden property. |

### `setNetworkActivityIndicatorVisible()` :simple-ios:

```ts
static setNetworkActivityIndicatorVisible(visible: boolean);
```

Управление видимостью индикатора сетевой активности.

**Параметры:**

| Name               | Type    | Description         |
| ------------------ | ------- | ------------------- |
| visible (Required) | boolean | Show the indicator. |

### `setTranslucent()` :simple-android:

```ts
static setTranslucent(translucent: boolean);
```

Управление полупрозрачностью строки состояния.

**Параметры:**

| Name                   | Type    | Description         |
| ---------------------- | ------- | ------------------- |
| translucent (Required) | boolean | Set as translucent. |

## Определения типа

### `StatusBarAnimation`

Тип анимации строки состояния для переходов на iOS.

| Type |
| ---- |
| enum |

**Константы:**

| Value     | Type   | Description     |
| --------- | ------ | --------------- |
| `'fade'`  | string | Fade animation  |
| `'slide'` | string | Slide animation |
| `'none'`  | string | No animation    |

### `StatusBarStyle`

Тип стиля строки состояния.

| Type |
| ---- |
| enum |

**Константы:**

| Value             | Type   | Description                                                          |
| ----------------- | ------ | -------------------------------------------------------------------- |
| `'default'`       | string | Default status bar style (dark for iOS, light for Android)           |
| `'light-content'` | string | Dark background, white texts and icons                               |
| `'dark-content'`  | string | Light background, dark texts and icons (requires API>=23 on Android) |
