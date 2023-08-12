---
description: useAnimatedStyle позволяет создать объект стилей, аналогичный стилям StyleSheet, который может быть анимирован с помощью shared values
---

# useAnimatedStyle

**`useAnimatedStyle`** позволяет создать объект стилей, аналогичный стилям `StyleSheet`, который может быть анимирован с помощью [shared values](../fundamentals/glossary.md#shared-value).

Стили, определенные с помощью `useAnimatedStyle`, должны быть переданы в свойство `style` компонента [Animated](../fundamentals/glossary.md#animated-component). Стили автоматически обновляются при изменении связанного с ними общего значения или состояния React.

Для анимирования свойств вместо этого используйте [`useAnimatedProps`](../core/useAnimatedProps.md).

## Описание

```js
import { useAnimatedStyle } from "react-native-reanimated";

function App() {
  // highlight-next-line
  const animatedStyles = useAnimatedStyle(() => {
    return {
      opacity: sv.value ? 1 : 0;
    };
  // highlight-next-line
  });

  // highlight-next-line
  return <Animated.View style={[styles.box, animatedStyles]} />
}
```

!!!note "Типизация"

    ```typescript
    type AnimatedStyleProp<T> =
    	| AnimateStyle<T>
    	| RegisteredStyle<AnimateStyle<T>>;

    function useAnimatedStyle<
    	T extends AnimatedStyleProp<
    		ViewStyle | ImageStyle | TextStyle
    	>
    >(
    	updater: () => T,
    	dependencies?: DependencyList | null
    ): T;
    ```

### Аргументы

#### `updater`

Функция, возвращающая объект со свойствами стиля, которые необходимо анимировать. Вы можете анимировать любое свойство стиля, доступное в React Native.

#### `dependencies`

Необязательный массив зависимостей.

Актуально только при использовании Reanimated [без плагина Babel в Web](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/web-support#web-without-a-babel-plugin).

## Возвращает

`useAnimatedStyle` возвращает объект анимированного стиля, который должен быть передан в свойство `style` компонента [Animated component](../fundamentals/glossary.md#animated-component), который вы хотите анимировать.

## Пример

```js
import React from 'react';
import { Button, View, StyleSheet } from 'react-native';
import Animated, {
    useSharedValue,
    withSpring,
    useAnimatedStyle,
} from 'react-native-reanimated';

export default function App() {
    const translateX = useSharedValue(0);

    const handlePress = () => {
        translateX.value += 50;
    };

    // highlight-start
    const animatedStyles = useAnimatedStyle(() => ({
        transform: [
            {
                translateX: withSpring(
                    translateX.value * 2
                ),
            },
        ],
    }));
    // highlight-end

    return (
        <>
            {/* highlight-next-line */}
            <Animated.View
                style={[styles.box, animatedStyles]}
            />
            <View style={styles.container}>
                <Button
                    onPress={handlePress}
                    title="Click me"
                />
            </View>
        </>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    },
    box: {
        height: 120,
        width: 120,
        backgroundColor: '#b58df1',
        borderRadius: 20,
        marginVertical: 50,
    },
});
```

## Замечания

-   Обратный вызов, переданный функции `useAnimatedStyle`, сначала выполняется в потоке [JavaScript](../fundamentals/glossary.md#javascript-thread), а сразу после этого - в потоке [UI](../fundamentals/glossary.md#ui-thread). Некоторые функции, такие как [`measure`](../advanced/measure.md) и [`scrollTo`](../scroll/scrollTo.md), имеют реализацию только в потоке UI. Чтобы безопасно использовать их внутри обратного вызова `useAnimatedStyle`, добавьте в код проверку:

    ```jsx
    function App() {
        const animatedStyles = useAnimatedStyle(() => {
            // highlight-next-line
            if (!_WORKLET) {
                return { offset: 0 };
            }

            // safely use measure or scrollTo functions
        });
    }
    ```

-   Следите за тем, чтобы не мутировать общие значения в обратном вызове `useAnimatedStyle`. Во многих случаях это может привести к бесконечному циклу.

-   Определяйте динамическую часть стилей только с помощью `useAnimatedStyle`, а статическую храните отдельно, используя API `StyleSheet` или (если это действительно необходимо) с помощью встроенных стилей. Таким образом, вы избежите множества ненужных пересчетов стилей. Статические и динамические стили могут быть легко объединены с помощью синтаксиса `[]`:

    ```jsx
    function App() {
        const animatedStyles = useAnimatedStyle(() => ({
            offset: sv.value,
        }));

        // highlight-next-line
        return (
            <Animated.View
                style={[styles.box, animatedStyles]}
            />
        );
    }

    const styles = StyleSheet.create({
        box: {
            height: 120,
            width: 120,
            backgroundColor: '#b58df1',
        },
    });
    ```

-   Чтобы избежать дублирования кода, можно совместно использовать анимированные свойства компонентов.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [useAnimatedStyle](https://docs.swmansion.com/react-native-reanimated/docs/core/useAnimatedStyle/)
