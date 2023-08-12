---
description: withSpring позволяет создавать анимацию на основе пружин
---

# withSpring

**`withSpring`** позволяет создавать анимацию на основе пружин.

```js
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withSpring,
    withRepeat,
} from 'react-native-reanimated';

const initialOffset = 200;

export default function App() {
    const offset = useSharedValue(initialOffset);

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    React.useEffect(() => {
        // highlight-next-line
        offset.value = withRepeat(
            withSpring(-offset.value),
            -1,
            true
        );
    }, []);

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedStyles]}
            />
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
    box: {
        height: 120,
        width: 120,
        backgroundColor: '#b58df1',
        borderRadius: 20,
    },
});
```

## Описание

```js
import { withSpring } from 'react-native-reanimated';

function App() {
    sv.value = withSpring(0);
    // ...
}
```

!!!note "Типизация"

    ```ts
    type AnimatableValue = number | string | number[];

    interface WithSpringConfig {
    	damping?: number;
    	mass?: number;
    	stiffness?: number;
    	overshootClamping?: boolean;
    	restSpeedThreshold?: number;
    	restDisplacementThreshold?: number;
    	velocity?: number;
    }

    function withSpring<T extends AnimatableValue>(
    	toValue: T,
    	config?: WithSpringConfig,
    	callback?: (
    		finished?: boolean,
    		current?: AnimatableValue
    	) => void
    ): T;
    ```

### Аргументы

#### `toValue`

Значение, при котором анимация завершится.

#### `config`

Конфигурация весенней анимации.

Доступные свойства:

| Имя                         | Тип       | По умолчанию | Описание                                                                                                                                             |
| --------------------------- | --------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `mass`                      | `number`  | `1`          | Масса пружины. Уменьшение этого значения ускоряет анимацию.                                                                                          |
| `damping`                   | `number`  | `10`         | Насколько быстро пружина замедляется. Большее значение демпфирования означает, что пружина будет быстрее приходить в состояние покоя.                |
| `duration`                  | `number`  | `2000`       | Продолжительность анимации (в миллисекундах).                                                                                                        |
| `dampingRatio`              | `number`  | `0.5`        | Степень демпфирования пружины. Значение `1` означает, что пружина критически задемпфирована, а значение `>1` означает, что пружина передемпфирована. |
| `stiffness`                 | `number`  | `100`        | Насколько упруга пружина.                                                                                                                            |
| `velocity`                  | `number`  | `0`          | Начальная скорость, применяемая к уравнению пружины.                                                                                                 |
| `overshootClamping`         | `boolean` | `false`      | Может ли пружина перескочить через `toValue`.                                                                                                        |
| `restDisplacementThreshold` | `number`  | `0.01`       | Смещение, ниже которого пружина будет защелкиваться на `toValue` без дальнейших колебаний.                                                           |
| `restSpeedThreshold`        | `number`  | `2`          | Скорость в пикселях в секунду, начиная с которой пружина будет защелкиваться на `toValue` без дальнейших колебаний.                                  |

!!!info ""

    Свойства `mass` и `damping` (основанные на физике) не могут использоваться одновременно со свойствами `duration` и `dampingRatio` (основанные на длительности).

    При совместном использовании `duration` и `dampingRatio` переопределяют свойства `mass` и `damping`.

#### `callback`

Функция, вызываемая по завершении анимации. Если анимация отменена, то обратный вызов получит в качестве аргумента `false`, в противном случае - `true`.

### Возвращает

`withSpring` возвращает [объект анимации](../fundamentals/glossary.md#animation-object). Он может быть либо напрямую присвоен [shared value](../fundamentals/glossary.md#shared-value), либо использован в качестве значения для объекта стиля, возвращаемого из [useAnimatedStyle](../core/useAnimatedStyle.md).

## Пример

```js
import React from 'react';
import {
    Pressable,
    StyleSheet,
    View,
    Text,
} from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withSpring,
} from 'react-native-reanimated';

const INITIAL_OFFSET = 110;
const SIZE = 160;
const MARGIN = 30;
const LEFT_BOUNDARY = 330;
const RIGHT_BOUNDARY = -330;

const items = [
    { color: '#FFE780' },
    { color: '#87CCE8' },
    { color: '#FFA3A1' },
    { color: '#B1DFD0' },
];

export default function App() {
    const offset = useSharedValue(INITIAL_OFFSET);

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const advanceBy = (position) => {
        const previousOffset = offset.value;
        if (
            (previousOffset < LEFT_BOUNDARY &&
                position === -1) ||
            (previousOffset > RIGHT_BOUNDARY &&
                position === 1)
        ) {
            const newOffset =
                offset.value +
                (SIZE + 2 * MARGIN) * -position;
            // highlight-start
            offset.value = withSpring(newOffset, {
                restDisplacementThreshold: 5,
                restSpeedThreshold: 5,
            });
            // highlight-end
        }
    };

    return (
        <View style={styles.container}>
            <View style={styles.buttonWrapper}>
                <Pressable
                    style={[styles.button, styles.previous]}
                    onPress={() => advanceBy(-1)}
                >
                    <Text style={styles.buttonItem}>
                        {'<'}
                    </Text>
                </Pressable>
                <Pressable
                    style={[styles.button, styles.next]}
                    onPress={() => advanceBy(1)}
                >
                    <Text style={styles.buttonItem}>
                        {'>'}
                    </Text>
                </Pressable>
            </View>
            <Animated.View
                style={[styles.row, animatedStyles]}
            >
                {items.map((item) => (
                    <View
                        key={item.color}
                        style={{
                            ...styles.box,
                            backgroundColor: item.color,
                        }}
                    />
                ))}
            </Animated.View>
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        position: 'relative',
        marginVertical: 64,
        overflow: 'hidden',
    },
    buttonWrapper: {
        position: 'absolute',
        width: SIZE,
        zIndex: 1,
    },
    box: {
        height: SIZE,
        width: SIZE,
        borderRadius: 5,
        marginHorizontal: MARGIN,
    },
    row: {
        flexDirection: 'row',
    },
    button: {
        position: 'absolute',
        width: SIZE / 3,
        height: SIZE / 3,
        borderRadius: SIZE,
        backgroundColor: '#ccc',
        borderColor: '#fff',
        borderWidth: 4,
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
        userSelect: 'none',
        top: 58,
    },
    buttonItem: {
        color: '#666',
        fontSize: 30,
        fontWeight: 'bold',
        fontFamily: 'monospace',
        paddingBottom: 2,
    },
    previous: {
        left: -30,
    },
    next: {
        right: -30,
    },
});
```

## Замечания

-   Обратный вызов, переданный в 3-м аргументе, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и запускается на [UI thread](../fundamentals/glossary.md#ui-thread).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |
