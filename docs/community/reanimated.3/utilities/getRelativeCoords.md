---
description: Определяет местоположение на экране относительно заданного представления
---

# getRelativeCoords

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Определяет местоположение на экране относительно заданного представления. Это может быть полезно, когда доступны только абсолютные координаты, а нужны координаты относительно родителя.

## Аргументы

### animatedRef

Продукт [`useAnimatedRef`](../core/useAnimatedRef.md) - это расширение Reanimated стандартного React `ref` (доставляет тег `view` в потоке UI). Этот `ref` должен быть передан в качестве свойства представлению, относительно которого мы хотим узнать координаты.

### x

Абсолютная координата `x`.

### y

Абсолютная координата `y`

## Возвращает

Объект, содержащий относительные координаты

-   `x`
-   `y`

## Пример

```js
const Comp = () => {
    const aref = useAnimatedRef();
    // ...

    const gestureHandler = useAnimatedGestureHandler({
        onEnd: (event) => {
            getRelativeCoords(
                aref,
                event.absoluteX,
                event.absoluteY
            );
        },
    });

    return (
        <View ref={aref}>
            <PanGestureHandler
                onGestureEvent={gestureHandler}
            >
                <Animated.View style={[styles.box]} />
            </PanGestureHandler>
        </View>
    );
};
```

## Ссылки

-   [getRelativeCoords](https://docs.swmansion.com/react-native-reanimated/docs/utilities/getRelativeCoords)
