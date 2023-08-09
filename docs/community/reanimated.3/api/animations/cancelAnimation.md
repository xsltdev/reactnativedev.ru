---
description: Отменяет анимацию, связанную с заданным shared value.
---

# cancelAnimation

Отменяет анимацию, связанную с заданным shared value.

## Аргументы

### `sharedValue` [SharedValueRef]

Значение, для которого мы хотим отменить ранее запущенную анимацию. Если для этого значения не было запущено анимации или она завершилась, то ошибка не возникнет.

## Пример

```js
const someValue = useSharedValue(0);

const gestureHandler = useAnimatedGestureHandler({
    onStart: (_, ctx) => {
        cancelAnimation(someValue);
    },
});
```

## Ссылки

-   [cancelAnimation](https://docs.swmansion.com/react-native-reanimated/docs/api/animations/cancelAnimation/)
