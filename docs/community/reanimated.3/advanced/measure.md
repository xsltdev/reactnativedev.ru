---
description: measure позволяет синхронно получать размеры и положение представления на экране в потоке UI thread
---

# measure

`measure` позволяет синхронно получать размеры и положение представления на экране в потоке [UI thread](../fundamentals/glossary.md#ui-thread).

## Справочник

```jsx
import { measure } from 'react-native-reanimated';

function App() {
    const animatedRef = useAnimatedRef();

    const handlePress = () => {
        runOnUI(() => {
            // highlight-next-line
            const measurement = measure(animatedRef);
            if (measurement === null) {
                return;
            }
            // ...
        })();
    };

    return <Animated.View ref={animatedRef} />;
}
```

Определения типов

```typescript
interface MeasuredDimensions {
    x: number;
    y: number;
    width: number;
    height: number;
    pageX: number;
    pageY: number;
}

function measure<T extends Component>(
    animatedRef: AnimatedRef<T>
): MeasuredDimensions | null;
```

### Аргументы

#### `animatedRef`

[animated ref](../core/useAnimatedRef.md#returns), связанный с компонентом, от которого вы хотите получить измерения. Анимированная ссылка должна быть передана либо [Анимированному компоненту](../fundamentals/glossary.md#animated-component), либо встроенному компоненту React Native.

### Возвращает

`measure` возвращает объект, содержащий такие поля:

-   `x` число, представляющее координату X относительно родительского компонента,
-   `y` число, представляющее координату Y относительно родительского компонента,
-   `width` - число, представляющее ширину компонента,
-   `height` - число, обозначающее высоту компонента,
-   `pageX` - число, обозначающее координату X относительно экрана,
-   `pageY` - число, представляющее координату Y относительно экрана,

или возвращает `null`, если измерение выполнить не удалось.

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
    useAnimatedRef,
    useSharedValue,
    withTiming,
} from 'react-native-reanimated';

export default function App() {
    const animatedRef = useAnimatedRef();
    const width = useSharedValue(100);
    const [text, setText] = React.useState(width.value);

    const handlePress = () => {
        width.value = withTiming(
            width.value + 50,
            {},
            () => {
                // highlight-next-line
                const measurement = measure(animatedRef);
                if (measurement === null) {
                    return;
                }

                runOnJS(setText)(
                    Math.floor(measurement.width)
                );
            }
        );
    };

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

-   `measure` реализована только в потоке [UI thread](../fundamentals/glossary.md#ui-thread). При использовании `measure` внутри [обработчиков событий](https://reactdev.ru/learn/responding-to-events#adding-event-handlers) его необходимо обернуть функцией [`runOnUI`](../threading/runOnUI.md).

-   Функция `useAnimatedStyle` сначала оценивается в [JavaScript-потоке](../fundamentals/glossary.md#javascript-thread) непосредственно перед присоединением представлений к нативной стороне. Поэтому для безопасного использования меры внутри `useAnimatedStyle` необходимо добавить в код условие, аналогичное приведенному ниже:

    ```jsx
    function App() {
        const animatedStyles = useAnimatedStyle(() => {
            // highlight-next-line
            if (_WORKLET) {
                // safely use measure
                const measurement = measure(animatedRef);
            }
        });
    }
    ```

    Последовательные запуски `useAnimatedStyle` выполняются в потоке UI.

-   Если вам нужны только размеры компонента и вы не будете использовать измерения во время анимации, вместо этого используйте свойство [`onLayout`](../../../rn/view.md#onlayout).

-   Иногда `measure` возвращает `null` (например, когда `ref` еще не присоединен к представлению). Для большей безопасности лучше добавить проверку `null` после измерения.

    ```jsx
    const animatedRef = useAnimatedRef();

    const handlePress = () => {
        runOnUI(() => {
            const measurement = measure(animatedRef);

            // highlight-start
            if (measurement === null) {
                return;
            }
            // highlight-end
            // ...
        })();
    };
    ```

-   `measure` может использоваться только для рендерных компонентов. Например, попытка `измерить` внеэкранные элементы в `FlatList` вернет значение `null`.

-   Функция `measure` недоступна в удаленном JS-отладчике. Мы настоятельно рекомендуем использовать Chrome DevTools (также известный как `chrome://inspect`) для отладки приложений React Native.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [measure](https://docs.swmansion.com/react-native-reanimated/docs/advanced/measure)
