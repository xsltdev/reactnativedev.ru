---
description: Помимо событий жестов и прокрутки, Reanimated предоставляет низкоуровневый API для создания собственных обработчиков анимированных событий для пользовательских анимированных компонентов
---

# Пользовательские события

Помимо событий жестов и прокрутки, Reanimated предоставляет низкоуровневый API для создания собственных обработчиков анимированных событий для пользовательских анимированных компонентов. С помощью этого API можно создавать хуки, аналогичные `useAnimatedGestureHandler` и `useAnimatedScrollHandler`.

## Обработка событий от пользовательского анимированного компонента

Допустим, вы хотите анимировать точки пагинации на основе пользовательского компонента, который раскрывает свое значение прокрутки - в данном примере мы будем использовать компонент [pager](https://github.com/callstack/react-native-pager-view).

```js
const AnimatedPagerView = Animated.createAnimatedComponent(
    PagerView
);

const PagerExample = () => {
    const scrollPosition = useSharedValue(0);
    return (
        <AnimatedPagerView
            initialPage={0}
            onPageScroll={scrollHandler}
        >
            <View collapsable={false}>
                <Text>{`Page ${1}`}</Text>
            </View>
            <View collapsable={false}>
                <Text>{`Page ${2}`}</Text>
            </View>
        </AnimatedPagerView>
    );
};
```

Здесь мы создаем анимированный пейджер с общим значением прокрутки, которое будет сохранять текущее значение прокрутки пейджера.

Далее мы создаем пользовательский хук обработчика для прослушивания собственных событий от компонента pager и их обработки внутри воркета.

```js
const scrollHandler = useAnimatedPagerScrollHandler({
    onPageScroll: (e) => {
        'worklet';
        scrollPosition.value = e.offset + e.position;
    },
});
```

`useAnimatedPagerScrollHandler` - это наш пользовательский хук - в ворклете `onPageScroll` мы получаем доступ к текущей позиции страницы и смещению пейджера. В совокупности они дают нам позицию прокрутки, которую мы можем использовать для анимирования компонентов или вычисления того, на сколько смещено содержимое пейджера.

Для реализации нашего пользовательского хука мы будем использовать два низкоуровневых метода - `useHandler` и `useEvent`.

```js
function useAnimatedPagerScrollHandler(
    handlers,
    dependencies
) {
    const { context, doDependenciesDiffer } = useHandler(
        handlers,
        dependencies
    );

    return useEvent(
        (event) => {
            'worklet';
            const { onPageScroll } = handlers;

            if (
                onPageScroll &&
                event.eventName.endsWith('onPageScroll')
            ) {
                onPageScroll(event, context);
            }
        },
        ['onPageScroll'],
        doDependenciesDiffer
    );
}
```

`useHandler` отвечает за предоставление контекстного объекта для нашего обработчика и информации о том, нужно ли его перестраивать.

`useEvent` возвращает обработчик события worklet, который, будучи переданным анимированному компоненту, обеспечит нативные события, которые могут быть обработаны в потоке UI.

Для получения полного примера ознакомьтесь с нашим примером приложения (ищите _Custom Handler Example - Pager_)

## Ссылки

-   [Custom Events](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/custom_events/)
