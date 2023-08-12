---
description: withRepeat - это модификатор анимации, позволяющий повторять анимацию заданное количество раз или запускать ее бесконечно
---

# withRepeat

**`withRepeat`** - это [модификатор анимации](../fundamentals/glossary.md#animation-modifier), позволяющий повторять анимацию заданное количество раз или запускать ее бесконечно.

```js
import React from 'react';
import Animated, {
    Easing,
    useAnimatedStyle,
    useSharedValue,
    withRepeat,
    withTiming,
} from 'react-native-reanimated';
import { StyleSheet, View } from 'react-native';

const duration = 2000;
const easing = Easing.bezier(0.25, -0.5, 0.25, 1);

export default function App() {
    const sv = useSharedValue(0);

    React.useEffect(() => {
        // highlight-next-line
        sv.value = withRepeat(
            withTiming(1, { duration, easing }),
            -1
        );
    }, []);

    const animatedStyle = useAnimatedStyle(() => ({
        transform: [{ rotate: `${sv.value * 360}deg` }],
    }));

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedStyle]}
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

## Описаие

```js
import { withRepeat } from 'react-native-reanimated';

function App() {
    sv.value = withRepeat(withSpring(0), 5);
    // ...
}
```

!!!note "Типизация"

    ```ts
    type AnimatableValue = number | string | number[];

    function withRepeat<T extends AnimatableValue>(
    	animation: T,
    	numberOfReps?: number,
    	reverse?: boolean,
    	callback?: (
    		finished?: boolean,
    		current?: AnimatableValue
    	) => void
    ): T;
    ```

### Аргументы

#### `animation`

Анимация, которую вы хотите повторить.

#### `numberOfReps`

Количество повторов анимации. По умолчанию равно `2`.

Неположительное значение (например, `0` или `-1`) приведет к тому, что анимация будет повторяться бесконечно, пока не будет отменена или снесена. Например, если компонент размонтирован или была вызвана функция [`cancelAnimation`](../core/cancelAnimation.md).

#### `reverse`

Должна ли анимация запускаться в обратном направлении при каждом втором повторении. По умолчанию имеет значение `false`.

Эта опция поддерживает только [функции анимации](../fundamentals/glossary.md#animation-function) (например, [`withSpring`](withSpring.md)) и не работает с [модификаторами анимации](../fundamentals/glossary.md#animation-modifier) (например, [`withSequence`](withSequence.md)).

#### `callback`

Функция, вызываемая по завершении анимации. В случае отмены анимации обратный вызов получит в качестве аргумента `false`, в противном случае - `true`.

### Возвращает

`withRepeat` возвращает [объект анимации](../fundamentals/glossary.md#animation-object). Он может быть либо напрямую присвоен [shared value](../fundamentals/glossary.md#shared-value), либо использован в качестве значения для объекта стиля, возвращаемого из [useAnimatedStyle](../core/useAnimatedStyle.md).

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

## Замечания

-   Обратный вызов, переданный в 4-м аргументе, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и запускается на [UI thread](../fundamentals/glossary.md#ui-thread).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [withRepeat](https://docs.swmansion.com/react-native-reanimated/docs/animations/withRepeat/)
