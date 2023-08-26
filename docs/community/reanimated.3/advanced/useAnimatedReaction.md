---
description: Функция useAnimatedReaction позволяет реагировать на изменения в shared value
---

# useAnimatedReaction

Функция `useAnimatedReaction` позволяет реагировать на изменения в [shared value](../fundamentals/glossary.md#shared-value). Это особенно удобно при сравнении значений, ранее хранившихся в общем значении, с текущим значением.

## Справочник

```javascript
import { useAnimatedReaction } from 'react-native-reanimated';

function App() {
    useAnimatedReaction(
        () => {
            return sv.value;
        },
        (currentValue, previousValue) => {
            if (currentValue !== previousValue) {
                // do something ✨
            }
        }
    );

    // ...
}
```

Определения типов

```typescript
type DependencyList = ReadonlyArray<any>;

function useAnimatedReaction<T>(
    prepare: () => T,
    react: (
        prepareResult: T,
        preparePreviousResult: T | null
    ) => void,
    dependencies?: DependencyList
): void;
```

### Аргументы

#### `prepare`

Функция, которая должна возвращать значение, на которое вы хотите отреагировать. Значение, возвращаемое этой функцией, используется в качестве первого параметра аргумента `react`.

```jsx
function App() {
    useAnimatedReaction(
        // highlight-start
        () => {
            return Math.floor(sv.value);
        },
        // highlight-end
        (currentValue, previousValue) => {
            // ...
        }
    );
}
```

#### `react`

Функция, реагирующая на изменение значения, возвращаемого функцией `prepare`. Функция `react` имеет два параметра: текущее значение из функции `prepare` и предыдущее значение, которое первоначально устанавливается в `null`.

```jsx
function App() {
    useAnimatedReaction(
        () => {
            return Math.floor(sv.value);
        },
        // highlight-next-line
        (currentValue, previousValue) => {
            // ...
            // highlight-next-line
        }
    );
}
```

#### `dependencies`

Необязательный массив зависимостей.

Актуально только при использовании Reanimated [без плагина Babel в Web](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/web-support#web-without-a-babel-plugin).

### Возвраты

`useAnimatedReaction` возвращает `undefined`.

## Пример

```ts
import React from 'react';
import {
    Button,
    StyleSheet,
    View,
    Text,
} from 'react-native';
import Animated, {
    measure,
    runOnJS,
    useAnimatedReaction,
    useAnimatedRef,
    useSharedValue,
    withSpring,
} from 'react-native-reanimated';

export default function App() {
    const animatedRef = useAnimatedRef();
    const width = useSharedValue(100);
    const [text, setText] = React.useState(width.value);

    const handlePress = () => {
        width.value = withSpring(width.value + 50);
    };

    // highlight-start
    useAnimatedReaction(
        () => width.value,
        () => {
            const measurement = measure(animatedRef);

            if (measurement !== null) {
                const measuredWidth = parseInt(
                    measurement.width,
                    10
                );
                runOnJS(setText)(measuredWidth);
            }
        }
    );
    // highlight-end

    return (
        <View style={styles.container}>
            <Animated.View
                ref={animatedRef}
                style={{ ...styles.box, width }}
            />
            <Text style={styles.label}>width: {text}</Text>
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
    },
    label: {
        fontSize: 24,
        marginVertical: 16,
        color: '#b58df1',
    },
});
```

## Замечания

-   Убедитесь, что в функции `result` не мутирует то же самое общее значение, которое было использовано в функции `prepare`, так как это приведет к бесконечному циклу.

    ```jsx
    function App() {
        useAnimatedReaction(
            () => {
                // highlight-next-line
                return width.value;
            },
            (currentValue) => {
                // 🚨 An infinite loop!
                // highlight-next-line
                width.value += currentValue;
            }
        );
    }
    ```

-   Обратные вызовы, переданные в аргументах `prepare` и `result`, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и выполняются в [UI thread](../fundamentals/glossary.md#ui-thread).

-   Технически вы можете реагировать на любое значение React с состоянием, используя `useAnimatedReaction`, но вместо этого вам, вероятно, следует использовать `useEffect`.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [useAnimatedReaction](https://docs.swmansion.com/react-native-reanimated/docs/advanced/useAnimatedReaction#example)
