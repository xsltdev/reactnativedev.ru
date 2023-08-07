# TouchableHighlight

!!!note ""

    Если вы ищете более широкий и перспективный способ обработки сенсорного ввода, обратите внимание на API [Pressable](pressable.md).

Обертка, позволяющая заставить представления правильно реагировать на прикосновения. При нажатии уменьшается непрозрачность обернутого представления, что позволяет цвету подложки проявиться, затемняя или оттеняя представление.

Подложка возникает при обертывании дочернего элемента в новый вид, что может влиять на компоновку и иногда вызывать нежелательные визуальные артефакты при неправильном использовании, например, если `backgroundColor` обернутого представления явно не установлен на непрозрачный цвет.

`TouchableHighlight` должен иметь один дочерний компонент (не ноль или более одного). Если вы хотите иметь несколько дочерних компонентов, оберните их в представление.

```ts
function MyComponent(props: MyComponentProps) {
    return (
        <View
            {...props}
            style={{ flex: 1, backgroundColor: '#fff' }}
        >
            <Text>My Component</Text>
        </View>
    );
}

<TouchableHighlight
    activeOpacity={0.6}
    underlayColor="#DDDDDD"
    onPress={() => alert('Pressed!')}
>
    <MyComponent />
</TouchableHighlight>;
```

## Пример

<div data-snack-id="@bndby/touchablehighlight-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы

### [TouchableWithoutFeedback Props](touchablewithoutfeedback.md#props)

Наследует [TouchableWithoutFeedback Props](touchablewithoutfeedback.md#props).

### activeOpacity

Определяет, какой должна быть непрозрачность обернутого представления при активном касании. Значение должно быть между `0` и `1`. По умолчанию `0.85`. Требуется, чтобы был установлен `underlayColor`.

| Тип   |
| ----- |
| число |

### onHideUnderlay

Вызывается сразу после скрытия подложки.

| Тип     |
| ------- |
| функция |

### onShowUnderlay

Вызывается сразу после показа подложки.

| Тип     |
| ------- |
| функция |

### style

| Type       |
| ---------- |
| View.style |

### underlayColor

Цвет подложки, который будет отображаться при активном прикосновении.

| Тип               |
| ----------------- |
| [цвет](colors.md) |

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

### testOnly_pressed

Удобно для тестов с моментальным снимком.

| Type |
| ---- |
| bool |
