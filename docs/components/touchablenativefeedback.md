# TouchableNativeFeedback

!!!note ""

    Если вы ищете более широкий и перспективный способ обработки сенсорного ввода, обратите внимание на API [Pressable](pressable.md).

Обертка для обеспечения правильной реакции представлений на прикосновения (только для Android). На Android этот компонент использует родное состояние `drawable` для отображения обратной связи при касании.

На данный момент он поддерживает только один экземпляр `View` в качестве дочернего узла, так как это реализовано путем замены этого `View` на другой экземпляр узла `RCTView` с некоторыми дополнительными свойствами.

Фоновый `drawable` нативного `touchable` обратной связи может быть настроен с помощью свойства `background`.

## Пример

<div data-snack-id="@bndby/touchablenativefeedback-android-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы

### [TouchableWithoutFeedback Props](touchablewithoutfeedback.md#props)

Наследует [TouchableWithoutFeedback Props](touchablewithoutfeedback.md#props).

### background

Определяет тип фонового рисуемого объекта, который будет использоваться для отображения обратной связи. Он принимает объект со свойством `type` и дополнительные данные, зависящие от `type`. Рекомендуется использовать один из статических методов для создания этого словаря.

| Type               |
| ------------------ |
| backgroundPropType |

### useForeground

Установите значение `true`, чтобы добавить эффект пульсации на передний план представления, а не на фон. Это полезно, если одно из ваших дочерних представлений имеет свой собственный фон, или вы, например, отображаете изображения и не хотите, чтобы рябь была закрыта ими.

Сначала проверьте `TouchableNativeFeedback.canUseNativeForeground()`, так как эта функция доступна только на Android 6.0 и выше. Если вы попытаетесь использовать эту функцию на более старых версиях, вы получите предупреждение и вернетесь к фону.

| Type |
| ---- |
| bool |

### hasTVPreferredFocus :simple-android:

Предпочтительный фокус телевизора (см. документацию для компонента `View`).

| Type |
| ---- |
| bool |

### nextFocusDown :simple-android:

ТВ следующий фокус вниз (см. документацию для компонента `View`).

| Тип   |
| ----- |
| номер |

### nextFocusForward :simple-android:

TV следующий фокус вперед (см. документацию для компонента `View`).

| Тип   |
| ----- |
| номер |

### nextFocusLeft :simple-android:

Следующий фокус телевизора слева (см. документацию для компонента `View`).

| Тип   |
| ----- |
| номер |

### nextFocusRight :simple-android:

Следующий фокус телевизора вправо (см. документацию для компонента `View`).

| Тип   |
| ----- |
| номер |

### nextFocusUp :simple-android:

TV следующий фокус вверх (см. документацию для компонента `View`).

| Тип   |
| ----- |
| число |

## Методы

### SelectableBackground()

```ts
static SelectableBackground(
  rippleRadius: number | null,
): ThemeAttributeBackgroundPropType;
```

Создает объект, представляющий фон темы android по умолчанию для выбираемых элементов (`?android:attr/selectableItemBackground`). Параметр `rippleRadius` управляет радиусом эффекта пульсации.

### SelectableBackgroundBorderless()

```ts
static SelectableBackgroundBorderless(
  rippleRadius: number | null,
): ThemeAttributeBackgroundPropType;
```

Создает объект, представляющий фон темы android по умолчанию для выбираемых элементов без границ (`?android:attr/selectableItemBackgroundBorderless`). Доступен на уровне android API 21+. Параметр `rippleRadius` управляет радиусом эффекта пульсации.

### Ripple()

```ts
static Ripple(
  color: ColorValue,
  borderless: boolean,
  rippleRadius?: number | null,
): RippleBackgroundPropType;
```

Создает объект, представляющий собой отрисовываемую рябь с заданным цветом (в виде строки). Если свойство `borderless` имеет значение true, рябь будет выводиться за пределы границ представления (см. пример такого поведения нативных кнопок панели действий). Этот тип фона доступен на уровне Android API 21+.

**Параметры:**

| Имя          | Тип     | Требуемый | Описание                                                   |
| ------------ | ------- | --------- | ---------------------------------------------------------- |
| color        | string  | Yes       | Цвет пульсации                                             |
| borderless   | boolean | Yes       | Если пульсация может рендериться за пределами своих границ |
| rippleRadius | ?number | No        | управляет радиусом эффекта пульсации                       |

### canUseNativeForeground()

```ts
static canUseNativeForeground(): boolean;
```
