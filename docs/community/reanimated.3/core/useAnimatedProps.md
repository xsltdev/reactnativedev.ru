---
description: useAnimatedStyle позволяет создать анимированный объект свойств, который может быть анимирован с помощью shared values. Этот объект используется для анимирования свойств сторонних компонентов
---

# useAnimatedProps

`useAnimatedStyle` позволяет создать анимированный объект свойств, который может быть анимирован с помощью [shared values](../fundamentals/glossary.md#shared-value). Этот объект используется для анимирования свойств сторонних компонентов.

Для анимирования стиля вместо него можно использовать [`useAnimatedStyle`](useAnimatedStyle.md).

## Описание

```js
import { useAnimatedProps } from "react-native-reanimated";

function App() {
  // highlight-next-line
  const animatedProps = useAnimatedProps(() => {
    return {
      opacity: sv.value ? 1 : 0;
    };
  // highlight-next-line
  });

  // highlight-next-line
  return <Animated.View animatedProps={animatedProps} />
}
```

!!!note "Типизация"

    ```typescript
    function useAnimatedProps<T extends {}>(
    	updater: () => Partial<T>,
    	dependencies?: DependencyList | null,
    	adapters?:
    		| PropsAdapterFunction
    		| PropsAdapterFunction[]
    		| null
    ): Partial<T>;
    ```

### Аргументы

#### `updater`

Функция, возвращающая объект со свойствами, которые необходимо анимировать.

#### `dependencies`

Необязательный массив зависимостей.

Актуально только при использовании Reanimated [без плагина Babel в Web](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/web-support#web-without-a-babel-plugin).

#### `adapters`

Необязательная функция или массив функций.

Иногда при работе со сторонними библиотеками свойства на поверхности API могут называться не так, как они на самом деле представляют собой нативной стороне. Адаптеры позволяют справиться с такими ситуациями, определяя способ преобразования этих свойств.

Reanimated поставляется с двумя встроенными адаптерами:

-   [`SVGAdapter`](https://github.com/software-mansion/react-native-reanimated/blob/Reanimated2/src/reanimated2/PropAdapters.ts#L19) для работы со свойством `transform` в `react-native-svg`
-   [`TextInputAdapter`](https://github.com/software-mansion/react-native-reanimated/blob/Reanimated2/src/reanimated2/PropAdapters.ts#L57).

Вы можете создавать собственные адаптеры с помощью функции `createAnimatedPropAdapter`.

Приведем пример адаптации свойств `fill` и `stroke` из `react-native-svg` для их анимирования с помощью Reanimated.

```js
const adapter = createAnimatedPropAdapter(
    (props) => {
        if (Object.keys(props).includes('fill')) {
            props.fill = {
                type: 0,
                payload: processColor(props.fill),
            };
        }
        if (Object.keys(props).includes('stroke')) {
            props.stroke = {
                type: 0,
                payload: processColor(props.stroke),
            };
        }
    },
    ['fill', 'stroke']
);
```

## Возвращает

`useAnimatedProps` возвращает объект анимированных свойств, который должен быть передан в свойство `animatedProps` компонента [Animated component](../fundamentals/glossary.md#animated-component), который вы хотите анимировать.

## Пример

```js
import React from 'react';
import { Button, View, StyleSheet } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedProps,
    withTiming,
} from 'react-native-reanimated';
import { Svg, Circle } from 'react-native-svg';

const AnimatedCircle = Animated.createAnimatedComponent(
    Circle
);

export default function App() {
    const r = useSharedValue(20);

    const handlePress = () => {
        r.value += 10;
    };

    // highlight-start
    const animatedProps = useAnimatedProps(() => ({
        r: withTiming(r.value),
    }));
    // highlight-end

    return (
        <View style={styles.container}>
            <Svg style={styles.svg}>
                <AnimatedCircle
                    cx="50%"
                    cy="50%"
                    fill="#b58df1"
                    // highlight-next-line
                    animatedProps={animatedProps}
                />
            </Svg>
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
    svg: {
        height: 250,
        width: '100%',
    },
});
```

## Замечания

-   Чтобы избежать дублирования кода, можно совместно использовать анимированные свойства компонентов.
-   Мы рекомендуем создавать адаптеры вне тела компонента, чтобы избежать лишних пересчетов.

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [useAnimatedProps](https://docs.swmansion.com/react-native-reanimated/docs/core/useAnimatedProps/)
