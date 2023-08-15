---
description: Этот хук позволяет создавать анимацию на основе смещения ScrollView. Хук автоматически определяет, является ли ScrollView горизонтальным или вертикальным
---

# useScrollViewOffset

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Этот хук позволяет создавать анимацию на основе смещения `ScrollView`. Хук автоматически определяет, является ли `ScrollView` горизонтальным или вертикальным.

```ts
useScrollViewOffset(aref: RefObject<Animated.ScrollView>) => [SharedValue<number>]
```

## Аргументы

### `aref` [RefObject&lt;Animated.ScrollView&gt;]

Анимированная ссылка на `ScrollView`. Ссылка должна быть передана соответствующему `ScrollView` в свойстве `ref`.

## Возвращает

Общее значение, содержащее текущее смещение `ScrollView`.

## Пример

```ts
function ScrollViewOffsetExample() {
    const aref = useAnimatedRef<Animated.ScrollView>();
    const scrollHandler = useScrollViewOffset(aref);

    useAnimatedStyle(() => {
        console.log(scrollHandler.value);
        return {};
    });

    return (
        <>
            <View style={styles.positionView}>
                <Text>Test</Text>
            </View>
            <View style={styles.divider} />
            <Animated.ScrollView
                ref={aref}
                style={styles.scrollView}
            >
                {[...Array(100)].map((_, i) => (
                    <Text key={i} style={styles.text}>
                        {i}
                    </Text>
                ))}
            </Animated.ScrollView>
        </>
    );
}
```

## Ссылки

-   [useScrollViewOffset](https://docs.swmansion.com/react-native-reanimated/docs/scroll/useScrollViewOffset)
