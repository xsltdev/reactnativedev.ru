---
description: withSequence - это модификатор анимации, позволяющий запускать анимацию в последовательности
---

# withSequence

**`withSequence`** - это [модификатор анимации](../fundamentals/glossary.md#animation-modifier), позволяющий запускать анимацию в последовательности.

```js
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withTiming,
    withSequence,
    Easing,
    withRepeat,
} from 'react-native-reanimated';

const initialOffset = 200;
const duration = 800;

export default function App() {
    const offset = useSharedValue(initialOffset);

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    React.useEffect(() => {
        offset.value = withRepeat(
            // highlight-start
            withSequence(
                withTiming(-initialOffset, {
                    duration,
                    easing: Easing.cubic,
                }),
                withTiming(0, {
                    duration,
                    easing: Easing.cubic,
                }),
                withTiming(initialOffset, {
                    duration,
                    easing: Easing.cubic,
                })
            ),
            // highlight-end
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

## Определение

```js
import { withSequence } from 'react-native-reanimated';

function App() {
    sv.value = withSequence(withTiming(50), withTiming(0));
    // ...
}
```

!!!note "Типизация"

    ```ts
    type AnimatableValue = number | string | number[];

    function withSequence<T extends AnimatableValue>(
    	...animations: [T, ...T[]]
    ): T;
    ```

### Аргументы

#### `...анимации`

Любое количество [объектов анимации](../fundamentals/glossary.md#animation-object) для последовательного запуска.

### Возвращает

`withSequence` возвращает объект [анимации](../fundamentals/glossary.md#animation-object). Он может быть либо напрямую присвоен [shared value](../fundamentals/glossary.md#shared-value), либо использован в качестве значения для объекта стиля, возвращаемого из [useAnimatedStyle](../core/useAnimatedStyle.md).

## Пример

```js
import React from 'react';
import { View, Button, StyleSheet } from 'react-native';
import Animated, {
    useSharedValue,
    withTiming,
    Easing,
    useAnimatedStyle,
    withRepeat,
    withSequence,
} from 'react-native-reanimated';

const ANGLE = 10;
const TIME = 100;
const EASING = Easing.elastic(1.5);

export default function App() {
    const rotation = useSharedValue(0);

    const animatedStyle = useAnimatedStyle(() => ({
        transform: [{ rotateZ: `${rotation.value}deg` }],
    }));

    const handlePress = () => {
        // highlight-next-line
        rotation.value = withSequence(
            // deviate left to start from -ANGLE
            withTiming(-ANGLE, {
                duration: TIME / 2,
                easing: EASING,
            }),
            // wobble between -ANGLE and ANGLE 7 times
            withRepeat(
                withTiming(ANGLE, {
                    duration: TIME,
                    easing: EASING,
                }),
                7,
                true
            ),
            // go back to 0 at the end
            withTiming(0, {
                duration: TIME / 2,
                easing: EASING,
            })
            // highlight-next-line
        );
    };

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedStyle]}
            />
            <Button title="wobble" onPress={handlePress} />
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
        marginBottom: 30,
    },
});
```

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [withSequence](https://docs.swmansion.com/react-native-reanimated/docs/animations/withSequence/)
