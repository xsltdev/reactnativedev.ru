---
description: Обеспечивает синхронную прокрутку в потоке UI до заданного смещения с использованием анимированной ссылки на представление прокрутки
---

# scrollTo

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Обеспечивает синхронную прокрутку в потоке UI до заданного смещения с использованием анимированной ссылки на представление прокрутки. Это позволяет выполнять плавную прокрутку без задержек (которые могли бы возникнуть при асинхронной прокрутке, основанной на множестве событий).

## Аргументы

### `animatedRef`

Продукт [`useAnimatedRef`](../core/useAnimatedRef.md), который является расширением Reanimated стандартного React ref (доставляет тег view в потоке UI).

### `x` [Float]

Соответствует пикселю вдоль горизонтальной оси элемента, который вы хотите отобразить в левом верхнем углу.

### `y` [Float]

Соответствует пикселю вдоль вертикальной оси элемента, который нужно отобразить в левом верхнем углу.

### `animated` [Boolean]

Указывает, должна ли прокрутка быть плавной (`true`) или мгновенной (`false`).

## Возвращает

`void`

## Пример

```jsx
import React from 'react';
import {
    TouchableOpacity,
    View,
    Text,
    ScrollView,
} from 'react-native';
import {
    useAnimatedRef,
    useDerivedValue,
    useSharedValue,
    scrollTo,
} from 'react-native-reanimated';

const ITEM_COUNT = 10;
const ITEM_SIZE = 100;
const ITEM_MARGIN = 10;

export const Comp = () => {
    const aref = useAnimatedRef();
    const scroll = useSharedValue(0);

    useDerivedValue(() => {
        scrollTo(
            aref,
            0,
            scroll.value * (ITEM_SIZE + 2 * ITEM_MARGIN),
            true
        );
    });

    const items = Array.from(Array(ITEM_COUNT).keys());

    const Incrementor = ({ increment }) => (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <TouchableOpacity
                onPress={() => {
                    scroll.value =
                        scroll.value + increment > 0
                            ? scroll.value + increment
                            : ITEM_COUNT - 1 + increment;

                    if (scroll.value >= ITEM_COUNT - 2)
                        scroll.value = 0;
                }}
            >
                <Text>{`Scroll ${Math.abs(increment)} ${
                    increment > 0 ? 'down' : 'up'
                }`}</Text>
            </TouchableOpacity>
        </View>
    );

    return (
        <View style={{ flex: 1, flexDirection: 'column' }}>
            <Incrementor increment={1} />
            <View
                style={{
                    width: '100%',
                    height:
                        (ITEM_SIZE + 2 * ITEM_MARGIN) * 2,
                }}
            >
                <ScrollView
                    ref={aref}
                    style={{ backgroundColor: 'orange' }}
                >
                    {items.map((_, i) => (
                        <View
                            key={i}
                            style={{
                                backgroundColor: 'white',
                                aspectRatio: 1,
                                width: ITEM_SIZE,
                                margin: ITEM_MARGIN,
                                justifyContent: 'center',
                                alignContent: 'center',
                            }}
                        >
                            <Text
                                style={{
                                    textAlign: 'center',
                                }}
                            >
                                {i}
                            </Text>
                        </View>
                    ))}
                </ScrollView>
            </View>

            <Incrementor increment={-1} />
        </View>
    );
};
```

## Ссылки

-   [scrollTo](https://docs.swmansion.com/react-native-reanimated/docs/scroll/scrollTo)
