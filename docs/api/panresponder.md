# PanResponder

**`PanResponder`** объединяет несколько касаний в один жест. Он делает жесты с одним касанием устойчивыми к дополнительным касаниям и может использоваться для распознавания основных жестов с несколькими касаниями.

По умолчанию `PanResponder` содержит хэндл `InteractionManager`, чтобы блокировать длительные события JS от прерывания активных жестов.

Он представляет собой предсказуемую обертку обработчиков, предоставляемых [gesture responder system](gesture-responder-system.md). Для каждого обработчика он предоставляет новый объект `gestureState` наряду с собственным объектом события:

```ts
onPanResponderMove: (event, gestureState) => {};
```

Нативное событие — это синтетическое событие касания с формой [PressEvent](pressevent.md).

Объект `gestureState` имеет следующее:

-   `stateID` — идентификатор состояния жеста — сохраняется до тех пор, пока на экране есть хотя бы одно касание.
-   `moveX` — последние экранные координаты недавно перемещенного касания
-   `moveY` — последние экранные координаты недавно перемещенного касания
-   `x0` — координаты экрана отвечающего гранта
-   `y0` — экранные координаты отвечающего гранта
-   `dx` — накопленное расстояние жеста с момента начала касания
-   `dy` — накопленное расстояние жеста с момента начала касания
-   `vx` — текущая скорость жеста
-   `vy` — текущая скорость жеста
-   `numberActiveTouches` — количество касаний, находящихся в данный момент на экране

## Шаблон использования

```tsx
const ExampleComponent = () => {
    const panResponder = React.useRef(
        PanResponder.create({
            // Ask to be the responder:
            onStartShouldSetPanResponder: (
                evt,
                gestureState
            ) => true,
            onStartShouldSetPanResponderCapture: (
                evt,
                gestureState
            ) => true,
            onMoveShouldSetPanResponder: (
                evt,
                gestureState
            ) => true,
            onMoveShouldSetPanResponderCapture: (
                evt,
                gestureState
            ) => true,

            onPanResponderGrant: (evt, gestureState) => {
                // The gesture has started. Show visual feedback so the user knows
                // what is happening!
                // gestureState.d{x,y} will be set to zero now
            },
            onPanResponderMove: (evt, gestureState) => {
                // The most recent move distance is gestureState.move{X,Y}
                // The accumulated gesture distance since becoming responder is
                // gestureState.d{x,y}
            },
            onPanResponderTerminationRequest: (
                evt,
                gestureState
            ) => true,
            onPanResponderRelease: (evt, gestureState) => {
                // The user has released all touches while this view is the
                // responder. This typically means a gesture has succeeded
            },
            onPanResponderTerminate: (
                evt,
                gestureState
            ) => {
                // Another component has become the responder, so this gesture
                // should be cancelled
            },
            onShouldBlockNativeResponder: (
                evt,
                gestureState
            ) => {
                // Returns whether this component should block native components from becoming the JS
                // responder. Returns true by default. Is currently only supported on android.
                return true;
            },
        })
    ).current;

    return <View {...panResponder.panHandlers} />;
};
```

## Пример

`PanResponder` работает с API `Animated`, чтобы помочь создать сложные жесты в пользовательском интерфейсе. Следующий пример содержит анимированный компонент `View`, который можно свободно перетаскивать по экрану

<div data-snack-id="@bndby/panresponder" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Попробуйте использовать пример [PanResponder в RNTester](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/PanResponder/PanResponderExample.js).

## Методы

### `create()`

```tsx
static create(config: PanResponderCallbacks): PanResponderInstance;
```

**Parameters:**

| Name                 | Type   | Description |
| -------------------- | ------ | ----------- |
| config (обязательно) | object | Refer below |

Объект `config` предоставляет расширенные версии всех обратных вызовов ответчика, которые обеспечивают не только [`PressEvent`](pressevent.md), но и состояние жеста `PanResponder`, путем замены слова `Responder` на `PanResponder` в каждом из типичных обратных вызовов `onResponder*`. Например, объект `config` будет выглядеть следующим образом:

-   `onMoveShouldSetPanResponder: (e, gestureState) => {...}`
-   `onMoveShouldSetPanResponderCapture: (e, gestureState) => {...}`
-   `onStartShouldSetPanResponder: (e, gestureState) => {...}`
-   `onStartShouldSetPanResponderCapture: (e, gestureState) => {...}`
-   `onPanResponderReject: (e, gestureState) => {...}`
-   `onPanResponderGrant: (e, gestureState) => {...}`
-   `onPanResponderStart: (e, gestureState) => {...}`
-   `onPanResponderEnd: (e, gestureState) => {...}`
-   `onPanResponderRelease: (e, gestureState) => {...}`
-   `onPanResponderMove: (e, gestureState) => {...}`
-   `onPanResponderTerminate: (e, gestureState) => {...}`.
-   `onPanResponderTerminationRequest: (e, gestureState) => {...}`.
-   `onShouldBlockNativeResponder: (e, gestureState) => {...}`.

В общем, для событий, имеющих эквиваленты захвата, мы обновляем gestureState один раз в фазе захвата и можем использовать его также в фазе пузыря.

Будьте осторожны с обратными вызовами `onStartShould*`. Они отражают обновленное `gestureState` только для событий начала/окончания, которые передаются узлу в пузырьке/захвате. Как только узел станет ответчиком, вы можете рассчитывать на то, что каждое событие начала/окончания будет обработано жестом и `gestureState` будет обновлено соответствующим образом. (NumberActiveTouches) может быть не совсем точным, если только вы не являетесь ответчиком.
