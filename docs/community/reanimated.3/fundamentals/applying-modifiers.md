---
description: Другим способом настройки анимации в Reanimated является использование модификаторов анимации
---

# Применение модификаторов

Другим способом [настройки анимации](customizing-animation.md) в Reanimated является использование модификаторов анимации. Reanimated поставляется с тремя встроенными модификаторами: [`withRepeat`](../animations/withRepeat.md), [`withSequence`](../animations/withSequence.md) и [`withDelay`](../animations/withDelay.md).

Давайте построим простую анимацию тряски, в которой используются все модификаторы, и попутно изучим их. Поехали!

## Начальная точка

В этом примере мы создадим анимированный бокс, который будет трястись при нажатии кнопки с небольшой задержкой.

Начнем с того, что у нас есть `Animated.View` и `Button`, которая перемещает вид вправо при нажатии. Для этого мы можем использовать `useAnimatedStyle` и функцию `withTiming`, чтобы плавно анимировать наш бокс на `40px` вправо.

Если для вас это звучит ново, не беспокойтесь! Начните с изучения основ в разделе [Your First Animation] (your-first-animation.md) и вернитесь сюда, когда будете готовы.

```js
import Animated, {
    useSharedValue,
    withTiming,
    useAnimatedStyle,
} from 'react-native-reanimated';
import { View, Button, StyleSheet } from 'react-native';
import React from 'react';

export default function App() {
    const offset = useSharedValue(0);

    const style = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const OFFSET = 40;

    const handlePress = () => {
        offset.value = withTiming(OFFSET);
    };

    return (
        <View style={styles.container}>
            <Animated.View style={[styles.box, style]} />
            <Button title="shake" onPress={handlePress} />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        flexDirection: 'column',
        alignItems: 'center',
    },
    box: {
        width: 100,
        height: 100,
        margin: 50,
        borderRadius: 15,
        backgroundColor: '#b58df1',
    },
});
```

## Повторение анимации

Для реализации желаемой анимации встряхивания мы можем использовать модификатор [`withRepeat`](../animations/withRepeat.md). Этот модификатор позволяет повторить заданную анимацию.

```js
import { withRepeat } from 'react-native-reanimated';

function App() {
    sv.value = withRepeat(withTiming(50), 5);
    // ...
}
```

Передайте число во второй параметр функции, чтобы она повторялась заданное количество раз. Можно сделать так, чтобы анимация повторялась вечно, передав неположительное значение (например, `0` или `-1`). Анимацию можно заставить повторяться вперед-назад, передав третьему аргументу `true` (т.е. `reverse`).

Подробнее об этом можно узнать из полного справочника [`withRepeat` API](../animations/withRepeat.md).

Используем эту функцию в нашем примере:

```js
import Animated, {
    useSharedValue,
    withTiming,
    useAnimatedStyle,
    withRepeat,
} from 'react-native-reanimated';
import { View, Button, StyleSheet } from 'react-native';
import React from 'react';

export default function App() {
    const offset = useSharedValue(0);

    const style = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const OFFSET = 40;

    const handlePress = () => {
        offset.value = withRepeat(
            withTiming(OFFSET),
            5,
            true
        );
    };

    return (
        <View style={styles.container}>
            <Animated.View style={[styles.box, style]} />
            <Button title="shake" onPress={handlePress} />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        flexDirection: 'column',
        alignItems: 'center',
    },
    box: {
        width: 100,
        height: 100,
        margin: 50,
        borderRadius: 15,
        backgroundColor: '#b58df1',
    },
});
```

Запустив этот код, мы видим, что коробка покачивается влево-вправо между заданным смещением и начальной позицией. После завершения анимации коробка не возвращается в исходное положение. Это не совсем то, как мы представляли себе анимацию тряски, но мы к этому еще вернемся!

## Последовательный запуск анимации

Один из способов улучшить нашу анимацию - запустить ее со смещением влево и вернуть в исходное положение после окончания анимации. Это идеальный вариант использования модификатора `withSequence`.

```js
import { withSequence } from 'react-native-reanimated';

function App() {
    sv.value = withSequence(withTiming(50), withTiming(0));
    // ...
}
```

Этот модификатор позволяет объединять анимации в цепочки. Следующая анимация запускается после завершения предыдущей. Его можно использовать, передавая анимации в качестве аргументов в том порядке, в котором вы хотите их запустить.

Подробнее об этом можно узнать из полного справочника [`withSequence` API](../animations/withSequence.md).

Вернемся к нашему примеру - мы можем использовать `withSequence` для улучшения нашей анимации. Сначала мы качнем коробку влево, что займет половину времени одного качания. Затем встряхнем коробку 5 раз и завершим анимацию, вернув ее в исходное положение также за половину длительности анимации.

```js
import Animated, {
    useSharedValue,
    withTiming,
    Easing,
    useAnimatedStyle,
    withRepeat,
    withSequence,
} from 'react-native-reanimated';
import { View, Button, StyleSheet } from 'react-native';
import React from 'react';

export default function App() {
    const offset = useSharedValue(0);

    const style = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const OFFSET = 40;
    const TIME = 250;

    const handlePress = () => {
        offset.value = withSequence(
            // start from -OFFSET
            withTiming(-OFFSET, { duration: TIME / 2 }),
            // shake between -OFFSET and OFFSET 5 times
            withRepeat(
                withTiming(OFFSET, { duration: TIME }),
                5,
                true
            ),
            // go back to 0 at the end
            withTiming(0, { duration: TIME / 2 })
        );
    };

    return (
        <View style={styles.container}>
            <Animated.View style={[styles.box, style]} />
            <Button title="shake" onPress={handlePress} />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        flexDirection: 'column',
        alignItems: 'center',
    },
    box: {
        width: 100,
        height: 100,
        margin: 50,
        borderRadius: 15,
        backgroundColor: '#b58df1',
    },
});
```

## Запуск анимации с задержкой

В качестве вишенки мы добавим немного напряженности, добавив небольшую задержку перед началом анимации. Именно для этого в Reanimated предусмотрен модификатор `withDelay`.

```js
import { withDelay } from 'react-native-reanimated';

function App() {
    sv.value = withDelay(500, withTiming(0));
    // ...
}
```

Эта функция в качестве первого параметра принимает длительность в миллисекундах до начала анимации. Второй параметр задает задержку анимации.

Более подробно об этом можно узнать из полного справочника [`withDelay` API](../animations/withDelay.md).

```js
import Animated, {
    useSharedValue,
    withTiming,
    Easing,
    useAnimatedStyle,
    withRepeat,
    withSequence,
    withDelay,
} from 'react-native-reanimated';
import { View, Button, StyleSheet } from 'react-native';
import React from 'react';

export default function App() {
    const offset = useSharedValue(0);

    const style = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const OFFSET = 40;
    const TIME = 250;
    const DELAY = 400;

    const handlePress = () => {
        offset.value = withDelay(
            DELAY,
            withSequence(
                // start from -OFFSET
                withTiming(-OFFSET, { duration: TIME / 2 }),
                // shake between -OFFSET and OFFSET 5 times
                withRepeat(
                    withTiming(OFFSET, { duration: TIME }),
                    5,
                    true
                ),
                // go back to 0 at the end
                withTiming(0, { duration: TIME / 2 })
            )
        );
    };

    return (
        <View style={styles.container}>
            <Animated.View style={[styles.box, style]} />
            <Button title="shake" onPress={handlePress} />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        flexDirection: 'column',
        alignItems: 'center',
    },
    box: {
        width: 100,
        height: 100,
        margin: 50,
        borderRadius: 15,
        backgroundColor: '#b58df1',
    },
});
```

## Резюме

В этом разделе мы научились создавать сложные анимации с помощью модификаторов анимации. Подведем итоги:

-   Reanimated поставляется с тремя встроенными модификаторами анимации - `withRepeat`, `withSequence` и `withDelay`.
-   `withRepeat` позволяет повторять анимацию заданное количество раз или запускать ее бесконечно.
-   `withSequence` позволяет запускать анимацию последовательно.
-   `withDelay` позволяет запускать анимацию с задержкой.

## Что дальше?

В следующем разделе мы узнаем о работе с жестами `Tap` и `Pan`. Также мы познакомимся с функцией анимации `withDecay`.

## Ссылки

-   [Applying modifiers](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/applying-modifiers/)
