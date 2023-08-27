---
description: Функция withDecay позволяет создавать анимацию, имитирующую движение объектов. Анимация запускается с заданной скоростью и со временем замедляется в соответствии с заданным коэффициентом замедления, пока не остановится
---

# withDecay

Функция **`withDecay`** позволяет создавать анимацию, имитирующую движение объектов. Анимация запускается с заданной скоростью и со временем замедляется в соответствии с заданным коэффициентом замедления, пока не остановится.

```js
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withDecay,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

const SIZE = 120;

export default function App() {
    const offset = useSharedValue(0);
    const width = useSharedValue(0);

    const onLayout = (event) => {
        width.value = event.nativeEvent.layout.width;
    };

    const pan = Gesture.Pan()
        .onChange((event) => {
            // highlight-next-line
            offset.value += event.changeX;
        })
        .onFinalize((event) => {
            // highlight-start
            offset.value = withDecay({
                velocity: event.velocityX,
                rubberBandEffect: true,
                clamp: [
                    -(width.value / 2) + SIZE / 2,
                    width.value / 2 - SIZE / 2,
                ],
            });
            // highlight-end
        });

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View
                onLayout={onLayout}
                style={styles.wrapper}
            >
                <GestureDetector gesture={pan}>
                    <Animated.View
                        style={[styles.box, animatedStyles]}
                    />
                </GestureDetector>
            </View>
        </GestureHandlerRootView>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    wrapper: {
        flex: 1,
        width: '100%',
        alignItems: 'center',
        justifyContent: 'center',
    },
    box: {
        height: SIZE,
        width: SIZE,
        backgroundColor: '#b58df1',
        borderRadius: 20,
        cursor: 'grab',
        alignItems: 'center',
        justifyContent: 'center',
    },
});
```

## Описание

```js
import { withDecay } from 'react-native-reanimated';

function App() {
    sv.value = withDecay({ velocity: 1 });
    // ...
}
```

!!!note "Типизация"

    ```typescript
    type AnimatableValue = number | string | number[];

    interface WithDecayConfig {
    	deceleration?: number;
    	velocity?: number;
    	clamp?: [number, number];
    	velocityFactor?: number;
    	rubberBandEffect?: boolean;
    	rubberBandFactor?: number;
    }

    function withDecay(
    	userConfig: WithDecayConfig,
    	callback?: (
    		finished?: boolean,
    		current?: AnimatableValue
    	) => void
    ): number;
    ```

### Аргументы

#### `config`

Конфигурация анимации распада.

Доступные свойства:

| Имя                              | Тип                | По умолчанию | Описание                                                                                                                                                                            |
| -------------------------------- | ------------------ | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `velocity` (опционально)         | `number`           | `0`          | Начальная скорость анимации.                                                                                                                                                        |
| `deceleration` (опционально)     | `number`           | `0.998`      | Скорость, с которой скорость уменьшается с течением времени.                                                                                                                        |
| `clamp` (опционально)            | `[number, number]` | `[]`         | Массив из двух чисел, ограничивающий диапазон анимации. Анимация останавливается при достижении любого из этих чисел, если только опция `rubberBandEffect` не установлена в `true`. |
| `velocityFactor` (опционально)   | `number`           | `1`          | Множитель скорости.                                                                                                                                                                 |
| `rubberBandEffect` (опционально) | `boolean`          | `false`      | Заставляет анимацию отскакивать за пределы, указанные в `clamp`.                                                                                                                    |
| `rubberBandFactor` (опционально) | `number`           | `0.6`        | Сила эффекта резинки.                                                                                                                                                               |

#### `callback`

Функция, вызываемая по завершении анимации. В случае отмены анимации обратный вызов получит в качестве аргумента `false`, в противном случае - `true`.

### Возвращает

`withDecay` возвращает [объект анимации](../fundamentals/glossary.md#animation-object). Он может быть либо напрямую присвоен [shared value](../fundamentals/glossary.md#shared-value), либо использован в качестве значения для объекта стиля, возвращаемого из [useAnimatedStyle](../core/useAnimatedStyle.md).

## Пример

```js
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withDecay,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

const SIZE = 180;

export default function App() {
    const offset = useSharedValue(0);
    const width = useSharedValue(0);

    const onLayout = (event) => {
        width.value = event.nativeEvent.layout.width;
    };

    const pan = Gesture.Pan()
        .onChange((event) => {
            offset.value += event.changeX;
        })
        .onFinalize((event) => {
            offset.value = withDecay({
                velocity: event.velocityX,
                rubberBandEffect: true,
                clamp: [
                    -(width.value / 2) + SIZE / 2,
                    width.value / 2 - SIZE / 2,
                ],
            });
        });

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View
                onLayout={onLayout}
                style={styles.wrapper}
            >
                <GestureDetector gesture={pan}>
                    <Animated.View
                        style={[
                            styles.grab,
                            animatedStyles,
                        ]}
                    >
                        <Train />
                    </Animated.View>
                </GestureDetector>
                <TrainTracks />
            </View>
        </GestureHandlerRootView>
    );
}

function TrainTracks() {
    return (
        <View style={{ flexDirection: 'column' }}>
            <View style={styles.rail} />
            <View style={{ flexDirection: 'row' }}>
                {Array.from({ length: 20 }).map((_, i) => {
                    return (
                        <View
                            key={i}
                            style={styles.track}
                        />
                    );
                })}
            </View>
        </View>
    );
}

function Train() {
    return (
        <View style={styles.column}>
            <View style={styles.row}>
                <View style={styles.back} />
                <View style={styles.chimney} />
            </View>
            <View style={styles.row}>
                <View style={styles.body} />
                <View style={styles.front} />
            </View>
            <View style={styles.stripe} />
            <View style={styles.underbody} />
            <View style={styles.row}>
                <View style={styles.wheel} />
                <View style={styles.wheel} />
                <View style={styles.wheel} />
            </View>
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    wrapper: {
        flex: 1,
        width: '100%',
        alignItems: 'center',
        justifyContent: 'center',
    },
    grab: {
        cursor: 'grab',
    },
    text: {
        color: 'white',
        textTransform: 'uppercase',
        fontWeight: 'bold',
    },
    row: {
        flexDirection: 'row',
    },
    column: {
        flexDirection: 'column',
    },
    wheel: {
        height: 50,
        width: 50,
        backgroundColor: '#537FE7',
        borderRadius: 50,
        marginHorizontal: 5,
    },
    underbody: {
        width: SIZE,
        height: 30,
        backgroundColor: 'black',
        top: 30,
    },
    stripe: {
        width: SIZE,
        height: 10,
        backgroundColor: 'red',
        top: 30,
    },
    front: {
        width: 50,
        height: 50,
        backgroundColor: 'black',
        top: 30,
    },
    body: {
        width: 130,
        height: 50,
        backgroundColor: '#537FE7',
        top: 30,
    },
    chimney: {
        width: 20,
        height: 30,
        backgroundColor: 'black',
        top: 30,
        right: 15,
        marginLeft: 'auto',
    },
    back: {
        width: 50,
        height: 15,
        backgroundColor: '#537FE7',
        top: 30 + 15,
    },
    track: {
        height: 10,
        width: 20,
        backgroundColor: '#B8621B',
        marginHorizontal: 15,
    },
    rail: {
        width: '100%',
        height: 10,
        backgroundColor: 'gray',
    },
});
```

## Замечания

-   Обратный вызов, переданный во втором аргументе, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и запускается на [UI thread](../fundamentals/glossary.md#ui-thread).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [withDecay](https://docs.swmansion.com/react-native-reanimated/docs/animations/withDecay/)
