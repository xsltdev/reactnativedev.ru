---
description: cancelAnimation позволяет отменить запущенную анимацию, сопряженную с shared value
---

# cancelAnimation

**`cancelAnimation`** позволяет отменить запущенную анимацию, сопряженную с [shared value](../fundamentals/glossary.md#shared-value).

## Описание

```js
import { cancelAnimation } from 'react-native-reanimated';

function App() {
    const offset = useSharedValue(100);

    const handleCancel = () => {
        // highlight-next-line
        cancelAnimation(offset);
    };
}
```

!!!note "Типизация"

    ```ts
    type SharedValue<T> = { value: T };

    function cancelAnimation<T>(
    	sharedValue: SharedValue<T>
    ): void;
    ```

### Аргументы

#### `sharedValue`

Общее значение запущенной анимации, которую необходимо отменить. Если анимация не запущена, функция `cancelAnimation` ничего не делает.

### Возвращает

Функция `cancelAnimation` возвращает значение `undefined`.

## Пример

```js
import React from 'react';
import { StyleSheet, View, Button } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withTiming,
    withRepeat,
    cancelAnimation,
} from 'react-native-reanimated';

const OFFSET = 200;

export default function App() {
    const offset = useSharedValue(OFFSET);

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const startAnimation = () => {
        offset.value = withRepeat(
            withTiming(
                offset.value > 0 ? -OFFSET : OFFSET,
                { duration: 1500 }
            ),
            -1,
            true
        );
    };

    React.useEffect(() => {
        startAnimation();
    }, []);

    const handleCancelAnimation = () => {
        // highlight-next-line
        cancelAnimation(offset);
    };

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedStyles]}
            />
            <View style={styles.row}>
                <Button
                    title="Cancel animation"
                    onPress={handleCancelAnimation}
                />
                <Button
                    title="Start animation"
                    onPress={startAnimation}
                />
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
    box: {
        height: 120,
        width: 120,
        backgroundColor: '#b58df1',
        borderRadius: 20,
        marginBottom: 30,
    },
    row: {
        flexDirection: 'row',
        gap: 10,
    },
});
```

## Замечания

-   Вы можете возобновить анимацию, снова назначив ту же анимацию (например, `withSpring`, `withTiming`) общему значению.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [cancelAnimation](https://docs.swmansion.com/react-native-reanimated/docs/core/cancelAnimation/)
