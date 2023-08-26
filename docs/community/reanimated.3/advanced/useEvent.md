---
description: Это низкоуровневый хук, возвращающий обработчик событий, который будет вызываться при нативных событиях, и который следует использовать для создания собственных хуков обработчиков событий, таких как useAnimatedGestureHandler или useAnimatedScrollHandler
---

# useEvent

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Это низкоуровневый хук, возвращающий обработчик событий, который будет вызываться при нативных событиях, и который следует использовать для создания собственных хуков обработчиков событий, таких как `useAnimatedGestureHandler` или `useAnimatedScrollHandler`.

## Аргументы

### `handler` [function]

Обработчик получит объект события с нативной полезной нагрузкой, которая может быть передана в рабочие элементы пользовательского хука обработчика.

-   `event` [object] - объект события.
    Полезная нагрузка может отличаться в зависимости от типа события.

### `eventNames` [Array]

Массив имен событий, которые будут обработаны обработчиком.

### `rebuilt` [boolean]

Значение, указывающее, следует ли перестраивать обработчик.

## Возвращает

Хук возвращает обработчик события, который будет вызван при диспетчеризации нативного события.

## Пример

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

## Ссылки

-   [useEvent](https://docs.swmansion.com/react-native-reanimated/docs/advanced/useEvent)
