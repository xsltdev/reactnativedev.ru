---
description:
---

# withDecay

Запускает анимацию "прокрутки", основанную на скорости.

## Аргументы

### `options` [object]

Объект, содержащий конфигурацию анимации. Допустимые параметры перечислены ниже:

| Опции              | По умолчанию | Описание                                                               |
| ------------------ | ------------ | ---------------------------------------------------------------------- |
| `velocity`         | `0`          | Начальная скорость                                                     |
| `deceleration`     | `0.998`      | Скорость затухания                                                     |
| `clamp`            | `[]`         | Массив из двух границ анимации (необязательно)                         |
| `velocityFactor`   | `1`          | Коэффициент для изменения единицы скорости (необязательно)             |
| `rubberBandEffect` | `false`      | Эффект резиновой ленты после достижения границы зажима (необязательно) |
| `rubberBandFactor` | `0.6`        | Коэффициент для изменения силы эффекта резиновой ленты (необязательно) |

#### `velocityFactor`

По умолчанию единицей измерения скорости затухания является пиксель в секунду, но это может быть проблематично, если требуется анимировать величину, не связанную с пикселями, например, непрозрачность `[0, 1]` или прогресс-бар `[0, 1]`. В этом случае можно использовать свойство `velocityFactor` со значением `< 1`, чтобы изменить скорость изменения в соответствии с требуемой областью.

### `callback` [function] (опционально)

Предоставленная функция будет вызвана по завершении анимации. В случае отмены анимации обратный вызов получит в качестве аргумента `false`, в противном случае - `true`.

## Возвращает

Данный метод возвращает объект анимации. Он может быть присвоен непосредственно общему значению или использован в качестве значения для объекта стиля, возвращаемого из [`useAnimatedStyle`](../hooks/useAnimatedStyle.md).

## Пример

```js
import Animated, {
    useSharedValue,
    withSpring,
    useAnimatedStyle,
    useAnimatedGestureHandler,
} from 'react-native-reanimated';
import { PanGestureHandler } from 'react-native-gesture-handler';

function App() {
    const x = useSharedValue(0);

    const gestureHandler = useAnimatedGestureHandler({
        onStart: (_, ctx) => {
            ctx.startX = x.value;
        },
        onActive: (event, ctx) => {
            x.value = ctx.startX + event.translationX;
        },
        onEnd: (evt) => {
            x.value = withDecay({
                velocity: evt.velocityX,
                clamp: [0, 200], // optionally define boundaries for the animation
            });
        },
    });

    const animatedStyle = useAnimatedStyle(() => {
        return {
            transform: [
                {
                    translateX: x.value,
                },
            ],
        };
    });

    return (
        <PanGestureHandler onGestureEvent={gestureHandler}>
            <Animated.View
                style={[styles.box, animatedStyle]}
            />
        </PanGestureHandler>
    );
}
```

## Ссылки

-   [withDecay](https://docs.swmansion.com/react-native-reanimated/docs/api/animations/withDecay/)
