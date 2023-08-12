---
description: Функция useSharedValue позволяет определять общие значения в ваших компонентах
---

# useSharedValue

Функция **`useSharedValue`** позволяет определять [общие значения](../fundamentals/glossary.md#shared-value) в ваших компонентах.

## Описание

```js
import { useSharedValue } from 'react-native-reanimated';

function App() {
    const sv = useSharedValue(100);

    // read a shared value
    console.log(sv.value);

    // and modify it
    sv.value += 50;
}
```

!!!tip "Типизация"

    ```ts
    type SharedValue<T> = { value: T };

    function useSharedValue<T>(initialValue: T): SharedValue<T>;
    ```

### Аргументы

#### `initialValue`

Значение, которое вы хотите изначально сохранить в свойстве `.value`. Это может быть любое значение JavaScript, например, `number`, `string` или `boolean`, а также структуры данных, такие как `array` и `object`.

### Возвращает

Функция `useSharedValue` возвращает разделяемое значение с единственным свойством `value`, изначально установленным в значение `initialValue`.

Доступ к значениям, хранящимся в общих значениях, и их модификация возможны по свойству `.value`.

## Пример

```js
import React from 'react';
import { Button, StyleSheet, View } from 'react-native';
import Animated, {
    useSharedValue,
} from 'react-native-reanimated';

export default function App() {
    // highlight-next-line
    const width = useSharedValue(100);

    const handlePress = () => {
        // highlight-next-line
        width.value += 50;
    };

    return (
        <View style={styles.container}>
            <Animated.View
                style={{ ...styles.box, width }}
            />
            <Button
                onPress={handlePress}
                title="Click me"
            />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
    },
    box: {
        height: 100,
        backgroundColor: '#b58df1',
        borderRadius: 20,
        marginVertical: 64,
    },
});
```

## Замечания

-   При изменении `sv.value` Reanimated будет обновлять стили и поддерживать синхронизацию общего значения между потоками. Однако это не вызовет типичного React-рендеринга, поскольку общее значение - это обычный JavaScript-объект.

-   При изменении значения `sv.value` обновление будет происходить синхронно на потоке [UI thread](../fundamentals/glossary.md#ui-thread). С другой стороны, на потоке [JavaScript](../fundamentals/glossary.md#javascript-thread) обновление происходит асинхронно. Это означает, что при попытке немедленно записать в журнал `value` после изменения будет записано ранее сохраненное значение.

    ```js
    function App() {
        const sv = useSharedValue(100); // initially set 100

        sv.value += 50; // changing value stored in a shared value

        console.log(sv.value); // will still log 100
    }
    ```

-   Не используйте [destructuring assignment](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) при работе с общими значениями. Хотя это вполне допустимый JavaScript-код, он приведет к тому, что Reanimated не сможет сохранить реактивность разделяемого значения.

    ```js
    function App() {
        let { value } = sv; // don't do this

        console.log(value); // you can read the value just fine

        value += 50; // but this won't update the styles
    }
    ```

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [useSharedValue](https://docs.swmansion.com/react-native-reanimated/docs/core/useSharedValue/)
