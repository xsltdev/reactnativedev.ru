---
description: runOnUI позволяет асинхронно запускать workletized функции на UI thread
---

# runOnUI

`runOnUI` позволяет асинхронно запускать [workletized](../fundamentals/glossary.md#to-workletize) функции на [UI thread](../fundamentals/glossary.md#ui-thread).

Чаще всего используется либо с `useEffect` для запуска анимации при монтировании/демонтировании компонента, либо с функциями [`measure`](../advanced/measure.md) и [`scrollTo`](../scroll/scrollTo.md), которые имеют реализацию только в потоке UI.

## Справочник

```javascript
import { runOnUI } from 'react-native-reanimated';

function App() {
    // E.g. in event handler or in an effect
    // highlight-next-line
    runOnUI((greeting) => {
        console.log(`${greeting} from the UI thread`);
        // highlight-next-line
    })('Howdy');

    // ...
}
```

Определения типов

```typescript
function runOnUI<A extends any[], R>(
    fn: (...args: A) => R
): (...args: Parameters<typeof fn>) => void;
```

### Аргументы

#### fn

Ссылка на функцию, которую необходимо выполнить в потоке [UI thread](../fundamentals/glossary.md#ui-thread) из потока [JavaScript thread](../fundamentals/glossary.md#javascript-thread). Аргументы вашей функции должны быть переданы в функцию, возвращаемую из `runOnUI`, т.е. `runOnUI(myWorklet)(10);`.

### Возвращает

`runOnUI` возвращает функцию, принимающую аргументы для функции, переданной в качестве первого аргумента.

!!!info ""

    Не забудьте вызвать функцию, возвращаемую из `runOnUI`.

## Example

```ts
import React from 'react';
import { View, StyleSheet, Text } from 'react-native';
import {
    runOnJS,
    measure,
    useAnimatedRef,
    runOnUI,
} from 'react-native-reanimated';

function MeasurableText(props) {
    const { children, onPress } = props;
    const animatedRef = useAnimatedRef();

    const handleMeasure = () => {
        // highlight-next-line
        runOnUI(() => {
            const measurements = measure(animatedRef);
            runOnJS(onPress)(measurements);
            // highlight-next-line
        })();
    };

    return (
        <Text
            style={styles.title}
            onPress={handleMeasure}
            ref={animatedRef}
        >
            {children}
        </Text>
    );
}

export default function App() {
    const [text, setText] = React.useState(0);

    const handlePress = (measurements) => {
        setText(Math.floor(measurements.width));
    };

    return (
        <View style={styles.container}>
            {['React', 'Native', 'Reanimated'].map(
                (word) => (
                    <MeasurableText
                        key={word}
                        onPress={handlePress}
                    >
                        {word}
                    </MeasurableText>
                )
            )}
            <Text style={styles.label}>width: {text}</Text>
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
    title: {
        fontSize: 42,
        textAlign: 'center',
        fontWeight: 'bold',
        marginRight: 8,
    },
    label: {
        fontSize: 24,
        marginVertical: 16,
        color: '#b58df1',
    },
});
```

## Замечания

-   При реализации анимации следует сначала обратиться к более общим решениям, таким как [`useDerivedValue`](../core/useDerivedValue.md), [`useAnimatedReaction`](../advanced/useAnimatedReaction.md) или выполнение кода в обратных вызовах жестов, и использовать `runOnUI` только после того, как вы попробовали другие методы.

-   Распространенной ошибкой является выполнение функции внутри runOnUI следующим образом: ~~`runOnUI(myWorklet(10))()`~~. Здесь правильным будет `runOnUI(myWorklet)(10)`.

-   Переданный в качестве аргумента обратный вызов автоматически [workletized](../fundamentals/glossary.md#to-workletize) и готов к запуску на [UI thread](../fundamentals/glossary.md#ui-thread).

-   Убедитесь, что не следует выполнять `runOnUI` в потоке UI, так как это приведет к ошибке.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [runOnUI](https://docs.swmansion.com/react-native-reanimated/docs/threading/runOnUI)
