# TouchableOpacity

!!!note ""

    Если вы ищете более широкий и перспективный способ обработки сенсорного ввода, обратите внимание на API [Pressable](pressable.md).

Обертка, позволяющая заставить представления правильно реагировать на прикосновения. При нажатии уменьшается непрозрачность обернутого представления, затемняя его.

Непрозрачность контролируется путем обертывания дочерних элементов в `Animated.View`, который добавляется в иерархию представлений. Имейте в виду, что это может повлиять на компоновку.

## Пример

<div data-snack-id="@bndby/touchableopacity-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы

### [TouchableWithoutFeedback Props](touchablewithoutfeedback.md#props)

Наследует [TouchableWithoutFeedback Props](touchablewithoutfeedback.md#props).

### style

| Тип                               |
| --------------------------------- |
| [View.style](view-style-props.md) |

### activeOpacity

Определяет, какой должна быть непрозрачность обернутого представления, когда прикосновение активно. По умолчанию `0.2`.

| Тип   |
| ----- |
| число |

### tvParallaxProperties :simple-ios:

_(Только для Apple TV)_ Объект со свойствами для управления эффектом параллакса Apple TV.

-   `enabled`: Если `true`, эффекты параллакса включены. По умолчанию `true`.
-   `shiftDistanceX`: По умолчанию `2.0`.
-   `shiftDistanceY`: По умолчанию `2.0`.
-   `tiltAngle`: По умолчанию `0.05`.
-   `magnification`: По умолчанию `1.0`.
-   `pressMagnification`: По умолчанию `1.0`.
-   `pressDuration`: По умолчанию `0.3`.
-   `pressDelay`: По умолчанию `0.0`.

| Type   |
| ------ |
| объект |

### hasTVPreferredFocus :simple-ios:

_(Только для Apple TV)_ Предпочтительный фокус телевизора (см. документацию для компонента `View`).

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
| номер |
