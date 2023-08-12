---
descripion: useDerivedValue позволяет создавать новые shared values на основе существующих, сохраняя их реактивность
---

# useDerivedValue

**`useDerivedValue`** позволяет создавать новые [shared values](../fundamentals/glossary.md#shared-value) на основе существующих, сохраняя их реактивность.

## Описание

```js
import { useDerivedValue } from 'react-native-reanimated';

function App() {
    const derivedValue = useDerivedValue(() => {
        return sv.value * 50;
    });
}
```

!!!note "Типизация"

    ```typescript
    type SharedValue<T> = { value: T };
    type DerivedValue<T> = Readonly<SharedValue<T>>;

    function useDerivedValue<T>(
    	updater: () => T,
    	dependencies?: DependencyList
    ): DerivedValue<T>;
    ```

### Аргументы

#### `updater`

Функция, которая должна возвращать значение, построенное из общих значений, состояния React или любого другого значения JavaScript. Эта функция вызывается всякий раз, когда изменяется хотя бы одно из общих значений или состояний, используемых в теле функции.

#### `dependencies`

Необязательный массив зависимостей.

Актуально только при использовании Reanimated [без плагина Babel на Web](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/web-support#web-without-a-babel-plugin).

### Возвращает

`useDerivedValue` возвращает новое readonly [shared value](../fundamentals/glossary.md#shared-value) на основе значения, полученного из функции [`updater`](../core/useDerivedValue.md#updater).

## Пример

```js
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withTiming,
    withRepeat,
    useDerivedValue,
} from 'react-native-reanimated';

export default function App() {
    const scale = useSharedValue(1);

    // highlight-start
    const rotate = useDerivedValue(() => {
        return `${scale.value * 2}rad`;
    });
    // highlight-end

    const scaleStyles = useAnimatedStyle(() => ({
        transform: [{ scale: scale.value }],
    }));

    const rotateStyles = useAnimatedStyle(() => ({
        transform: [{ rotate: rotate.value }],
    }));

    React.useEffect(() => {
        scale.value = withRepeat(
            withTiming(scale.value * 2, { duration: 1000 }),
            -1,
            true
        );
    }, []);

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.ball, scaleStyles]}
            />
            <Animated.View
                style={[styles.box, rotateStyles]}
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
        flexDirection: 'row',
    },
    ball: {
        height: 50,
        width: 50,
        backgroundColor: '#b58df1',
        borderRadius: 50,
        marginRight: 80,
    },
    box: {
        height: 100,
        width: 100,
        backgroundColor: '#b58df1',
        borderRadius: 15,
    },
});
```

## Замечания

-   Обратный вызов, переданный в качестве первого аргумента, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и запускается на [UI thread](../fundamentals/glossary.md#ui-thread).

-   Для реакции на изменение общего значения можно также использовать функцию `useDerivedValue` без возврата значения в функции `updater`. Если необходимо получить доступ к предыдущему значению, хранящемуся в общем значении, используйте вместо этого функцию [`useAnimatedReaction`](../advanced/useAnimatedReaction.md).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [useDerivedValue](https://docs.swmansion.com/react-native-reanimated/docs/core/useDerivedValue/)
