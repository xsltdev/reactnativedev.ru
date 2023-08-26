---
description: runOnJS позволяет асинхронно запускать не workletized функции, которые иначе не могли бы выполняться в UI thread
---

# runOnJS

`runOnJS` позволяет асинхронно запускать не [workletized](../fundamentals/glossary.md#to-workletize) функции, которые иначе не могли бы выполняться в [UI thread](../fundamentals/glossary.md#ui-thread). Это относится к большинству внешних библиотек, поскольку их функции не помечены директивой `"worklet";`.

Функция `runOnJS` обычно используется для обновления состояния React либо по завершении анимации, либо условно в рамках жеста.

## Справочник

```javascript
import { runOnJS } from 'react-native-reanimated';

function App() {
    // While on the UI thread
    runOnJS(navigation.goBack)();
}
```

Определения типов

```typescript
function runOnJS<A extends any[], R>(
    fn: (...args: A) => R
): (...args: Parameters<typeof fn>) => void;
```

### Аргументы

#### fn

Ссылка на функцию, которую необходимо выполнить в [JavaScript-потоке](../fundamentals/glossary.md#javascript-thread) из [UI-потока](../fundamentals/glossary.md#ui-thread). Аргументы вашей функции должны быть переданы в функцию, возвращаемую из `runOnJS`, т.е. `runOnJS(setValue)(10);`.

### Возвращает

`runOnJS` возвращает функцию, принимающую аргументы для функции, переданной в качестве первого аргумента. Эта функция может быть безопасно выполнена в потоке UI.

!!!info ""

    Не забудьте вызвать функцию, возвращаемую из `runOnJS`.

## Пример

```ts
import React from 'react';
import {
    Button,
    View,
    StyleSheet,
    Text,
} from 'react-native';
import Animated, {
    useSharedValue,
    withSpring,
    runOnJS,
} from 'react-native-reanimated';
import { useAnimatedStyle } from 'react-native-reanimated';

export default function App() {
    const scale = useSharedValue(1);
    const [finished, setFinished] = React.useState(false);

    const handlePress = () => {
        scale.value = withSpring(2, {}, () => {
            // highlight-next-line
            runOnJS(setFinished)(true);
        });
    };

    const animatedStyle = useAnimatedStyle(() => ({
        transform: [{ scale: scale.value }],
    }));

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedStyle]}
            />
            <Button
                onPress={handlePress}
                title="Click me"
                disabled={finished}
            />
            {finished && <Text>Finished! 🎉</Text>}
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
        width: 100,
        backgroundColor: '#b58df1',
        borderRadius: 20,
        marginVertical: 64,
        alignSelf: 'center',
    },
});
```

## Замечания

-   Функции, передаваемые в `runOnJS`, должны быть определены в области [JavaScript thread](../fundamentals/glossary.md#javascript-thread), т.е. в теле компонента или в глобальной области. Данный код не будет работать, поскольку `myFunction` определена в обратном вызове `withTiming`, который выполняется только в потоке [UI thread](../fundamentals/glossary.md#ui-thread):

    ```js
    withTiming(0, {}, () => {
        // myFunction is defined on the UI thread
        const myFunction = () => {
            // ...
        };
        runOnJS(myFunction)(); // 💥
    });
    ```

-   Распространенной ошибкой является выполнение функции внутри runOnJS следующим образом: ~~`runOnJS(setValue(10))()`~~. Здесь правильным будет `runOnJS(setValue)(10)`.

-   Безопасно запускать функции через `runOnJS` в [JavaScript-потоке](../fundamentals/glossary.md#javascript-thread), так как это не оказывает никакого влияния.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [runOnJS](https://docs.swmansion.com/react-native-reanimated/docs/threading/runOnJS)
