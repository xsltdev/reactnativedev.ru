---
description: Если нашего набора предопределенных анимаций вам недостаточно, то эта вкладка - то, что вы ищете
---

# Пользовательские анимации

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Если нашего набора предопределенных анимаций вам недостаточно, то эта вкладка - то, что вы ищете.

## Пользовательская анимация выхода

Наши конструкторы анимации выхода из игры делают следующее: генерируют функцию worklet, которая возвращает данные, необходимые для запуска определенной анимации. Шаблон высокого уровня выглядит следующим образом:

```js
function CustomExitingAnimation(values) {
    'worklet';
    const animations = {
        // your animations
    };
    const initialValues = {
        // initial values for animations
    };
    const callback = (finished: boolean) => {
        // optional callback that will fire when layout animation ends
    };
    return {
        initialValues,
        animations,
        callback,
    };
}
```

-   `values` - содержит информацию о том, где был отображен вид и каковы его размеры
    -   `values.currentOriginX` - координата X левого верхнего угла в родительской системе координат
    -   `values.currentOriginY` - координата Y левого верхнего угла в родительской системе координат
    -   `values.currentWidth` - ширина вида
    -   `values.currentHeight` - высота представления
    -   `values.currentGlobalOriginX` - координата X левого верхнего угла в глобальной системе координат
    -   `values.currentGlobalOriginY` - координата Y левого верхнего угла в глобальной системе координат

### Пример

```js
function CardView() {
    const exiting = (values) => {
        'worklet';
        const animations = {
            originX: withTiming(width, { duration: 3000 }),
            opacity: withTiming(0.5, { duration: 2000 }),
        };
        const initialValues = {
            originX: values.currentOriginX,
            opacity: 1,
        };
        return {
            initialValues,
            animations,
        };
    };

    return (
        <Animated.View
            style={[styles.animatedView]}
            exiting={exiting}
        >
            <Text> Card Example </Text>
        </Animated.View>
    );
}
```

## Пользовательская анимация входа

Наш конструктор входной анимации делает "под капотом" генерацию функции worklet, которая возвращает данные, необходимые для запуска конкретной анимации. Шаблон высокого уровня выглядит следующим образом:

```js
function CustomEnteringAnimation(values) {
    'worklet';
    const animations = {
        // your animations
    };
    const initialValues = {
        // initial values for animations
    };
    const callback = (finished: boolean) => {
        // optional callback that will fire when layout animation ends
    };
    return {
        initialValues,
        animations,
        callback,
    };
}
```

-   `values` - содержит информацию о том, где должен отображаться вид и каковы его размеры
    -   `values.targetOriginX` - координата X левого верхнего угла в родительской системе координат
    -   `values.targetOriginY` - координата Y левого верхнего угла в родительской системе координат
    -   `values.targetWidth` - ширина вида
    -   `values.targetHeight` - высота представления
    -   `values.targetGlobalOriginX` - координата X левого верхнего угла в глобальной системе координат
    -   `values.targetGlobalOriginY` - координата Y левого верхнего угла в глобальной системе координат

### Пример

```js
function CardView() {
    const entering = (targetValues) => {
        'worklet';
        const animations = {
            originX: withTiming(targetValues.originX, {
                duration: 3000,
            }),
            opacity: withTiming(1, { duration: 2000 }),
            borderRadius: withDelay(
                4000,
                withTiming(30, { duration: 3000 })
            ),
            transform: [
                {
                    rotate: withTiming('0deg', {
                        duration: 4000,
                    }),
                },
                {
                    scale: withTiming(1, {
                        duration: 3500,
                    }),
                },
            ],
        };
        const initialValues = {
            originX: -width,
            opacity: 0,
            borderRadius: 10,
            transform: [
                { rotate: '90deg' },
                { scale: 0.5 },
            ],
        };
        return {
            initialValues,
            animations,
        };
    };

    return (
        <Animated.View
            style={[styles.animatedView]}
            entering={entering}
        >
            <Text> Card Example </Text>
        </Animated.View>
    );
}
```

## Пользовательский переход макета

То, что делают наши построители переходов макета под капотом, - это генерация функции worklet, которая возвращает необходимые данные для запуска конкретного перехода. Шаблон высокого уровня выглядит следующим образом:

```js
function CustomLayoutTransition(values) {
    'worklet';
    const animations = {
        // your animations
    };
    const initialValues = {
        // initial values for animations
    };
    const callback = (finished: boolean) => {
        // optional callback that will fire when layout animation ends
    };
    return {
        initialValues,
        animations,
        callback,
    };
}
```

-   `values` - содержит информацию о начале и размерах вида до и после него
    -   `values.targetOriginX` - координата X левого верхнего угла в родительской системе координат
    -   `values.targetOriginY` - координата Y левого верхнего угла в родительской системе координат
    -   `values.targetWidth` - ширина вида
    -   `values.targetHeight` - высота представления
    -   `values.targetGlobalOriginX` - координата X левого верхнего угла в глобальной системе координат
    -   `values.targetGlobalOriginY` - координата Y левого верхнего угла в глобальной системе координат
    -   `values.currentOriginX` - координата X левого верхнего угла в родительской системе координат (до)
    -   `values.currentOriginY` - координата Y левого верхнего угла в родительской системе координат (до)
    -   `values.currentWidth` - ширина вида (до)
    -   `values.currentHeight` - высота представления (до)
    -   `values.currentGlobalOriginX` - координата X левого верхнего угла в глобальной системе координат (до)
    -   `values.currentGlobalOriginY` - координата Y левого верхнего угла в глобальной системе координат (до)

### Пример

<video
  src="https://user-images.githubusercontent.com/12784455/120450759-09fa3980-c391-11eb-9b64-65ec8e6c2509.mp4"
  controls="controls"
  muted="muted"
  width="45%"></video>

```js
function CustomLayoutTransition(values) {
    'worklet';
    return {
        animations: {
            originX: withTiming(values.targetOriginX, {
                duration: 1000,
            }),
            originY: withDelay(
                1000,
                withTiming(values.targetOriginY, {
                    duration: 1000,
                })
            ),
            width: withSpring(values.targetWidth),
            height: withSpring(values.targetHeight),
        },
        initialValues: {
            originX: values.currentOriginX,
            originY: values.currentOriginY,
            width: values.currentWidth,
            height: values.currentHeight,
        },
    };
}

function Box({
    label,
    state,
}: {
    label: string,
    state: boolean,
}) {
    const ind = label.charCodeAt(0) - 'A'.charCodeAt(0);
    const delay = 300 * ind;
    return (
        <Animated.View
            layout={CustomLayoutTransition}
            style={[
                styles.box,
                {
                    flexDirection: state
                        ? 'row'
                        : 'row-reverse',
                    height: state ? 30 : 60,
                },
            ]}
        >
            <Text> {label} </Text>
        </Animated.View>
    );
}

export function CustomLayoutTransitionExample(): React.ReactElement {
    const [state, setState] = useState(true);
    return (
        <View style={{ marginTop: 30 }}>
            <View style={{ height: 300 }}>
                <View
                    style={{
                        flexDirection: state
                            ? 'row'
                            : 'column',
                    }}
                >
                    {state && (
                        <Box
                            key="a"
                            label="A"
                            state={state}
                        />
                    )}
                    <Box key="b" label="B" state={state} />
                    {!state && (
                        <Box
                            key="a"
                            label="A"
                            state={state}
                        />
                    )}
                    <Box key="c" label="C" state={state} />
                </View>
            </View>

            <Button
                onPress={() => {
                    setState(!state);
                }}
                title="toggle"
            />
        </View>
    );
}
```

## Другие факты

Каждый компонент Reanimated имеет свое общее значение, которое сохраняет текущие анимации, назначенные этому компоненту. Если вы хотите начать новую анимацию для определенного свойства и не указали начальное значение для него, то начальное значение будет взято из последней анимации, которая была назначена компоненту. Единственным исключением является анимация Entering, поскольку у нас нет возможности получить значения предыдущих анимаций.

## Ссылки

-   [Custom animations](https://docs.swmansion.com/react-native-reanimated/docs/layout-animations/custom-animations)
