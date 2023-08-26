---
description: Иногда требуется перевести значение из одного диапазона в другой. В этом случае следует использовать функцию interpolate, которая аппроксимирует значения между точками выходного диапазона и позволяет сопоставить значение внутри входного диапазона с соответствующим приближением в выходном диапазоне
---

# interpolate

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Иногда требуется перевести значение из одного диапазона в другой. В этом случае следует использовать функцию `interpolate`, которая аппроксимирует значения между точками выходного диапазона и позволяет сопоставить значение внутри входного диапазона с соответствующим приближением в выходном диапазоне. Она также поддерживает несколько типов экстраполяции для отображения за пределы диапазона.

## Аргументы

### `value` [Float]

Значение из входного диапазона, которое должно быть отображено на значение из выходного диапазона.

### `input range` [Float[]]

Массив Floats, содержащий точки, указывающие на диапазон входного значения. Значения в диапазоне ввода должны возрастать.

### `output range` [Float[]]

Массив Floats, содержащий точки, указывающие на диапазон выходного значения. В нем должно быть как минимум столько же точек, сколько и в диапазоне входных значений.

### `extrapolation type` [Object | String]

Может быть как объектом, так и строкой. Если передается объект, то он должен явно указывать экстраполяцию для правой и левой сторон. Если экстраполяция для какой-либо стороны не указана, то по умолчанию используется `Extrapolation.EXTEND`. Пример объекта с типом экстраполяции:

```js
const extrapolation = {
    extrapolateLeft: Extrapolation.CLAMP,
    extrapolateRight: Extrapolation.IDENTITY,
};
```

Если указана строка, то указанный тип экстраполяции применяется к обеим сторонам.

!!!info ""

    Доступные типы экстраполяции:

    -   `Extrapolation.CLAMP` - прижимает значение к краю выходного диапазона
    -   `Extrapolation.IDENTITY` - возвращает интерполируемое значение
    -   `Extrapolation.EXTEND` - аппроксимирует значение даже за пределами диапазона

    Доступные значения строк экстраполяции:

    -   `clamp`
    -   `identity`
    -   `extend`

## Возвращает

`interpolate` возвращает значение после интерполяции из диапазона вывода.

## Пример

```jsx
import React from 'react';
import { View, StyleSheet, Dimensions } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedScrollHandler,
    useAnimatedStyle,
    interpolate,
} from 'react-native-reanimated';

export const HEADER_IMAGE_HEIGHT =
    Dimensions.get('window').width / 3;

export default function Test() {
    const scrollY = useSharedValue(0);
    const scrollHandler = useAnimatedScrollHandler({
        onScroll: (e) => {
            scrollY.value = e.contentOffset.y;
        },
    });
    const animatedStyles = useAnimatedStyle(() => {
        const scale = interpolate(
            scrollY.value,
            [-100, 0],
            [2, 1],
            {
                extrapolateRight: Extrapolation.CLAMP,
            }
        );

        return {
            transform: [{ scale: scale }],
        };
    });

    return (
        <View style={{ flex: 1, alignItems: 'center' }}>
            <Animated.View
                style={[
                    {
                        position: 'absolute',
                        top: 20,
                        left: 0,
                        width: 20,
                        height: 20,
                        backgroundColor: 'blue',
                    },
                    animatedStyles,
                ]}
            />

            <Animated.ScrollView
                style={StyleSheet.absoluteFill}
                onScroll={scrollHandler}
            ></Animated.ScrollView>
        </View>
    );
}
```

## Ссылки

-   [interpolate](https://docs.swmansion.com/react-native-reanimated/docs/utilities/interpolate)
