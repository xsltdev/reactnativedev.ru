---
description: В этом разделе мы научимся работать с жестами в Reanimated. Для этого Reanimated тесно интегрируется с React Native Gesture Handler
---

# Работа с жестами

В этом разделе мы научимся работать с жестами в Reanimated. Для этого Reanimated тесно интегрируется с [React Native Gesture Handler](https://docs.swmansion.com/react-native-gesture-handler/docs/), другой библиотекой, созданной Software Mansion.

Gesture Handler поставляется с большим количеством жестов, таких как [`Pinch`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/pinch-gesture) или [`Fling`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/fling-gesture). Сейчас мы начнем с простого и познакомимся с жестами [`Tap`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/tap-gesture) и [`Pan`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/pan-gesture), а также с тем, как использовать функцию анимации [`withDecay`](/docs/animations/withDecay).

Только не забудьте сначала просмотреть [шаги по установке](https://docs.swmansion.com/react-native-gesture-handler/docs/installation) обработчика жестов и вернуться сюда, чтобы узнать, как использовать его в Reanimated.

## Работа с жестами касания

Начнем с самого простого жеста - постукивания. Жест касания определяет прикосновение пальцев к экрану в течение короткого промежутка времени. С их помощью можно реализовать пользовательские кнопки или нажимаемые элементы с нуля.

В данном примере мы создадим круг, который будет увеличиваться и менять цвет при касании.

Сначала обернем наше приложение с помощью [`GestureHandlerRootView`](https://docs.swmansion.com/react-native-gesture-handler/docs/installation/#js). Убедитесь, что `GestureHandlerRootView` находится как можно ближе к реальному корневому виду. Это гарантирует, что наши жесты будут работать друг с другом как положено.

```js
import { GestureHandlerRootView } from 'react-native-gesture-handler';

function App() {
    return (
        <GestureHandlerRootView style={{ flex: 1 }}>
            {/* rest of the app */}
        </GestureHandlerRootView>
    );
}
```

Новые жесты касания определяются с помощью `Gesture.Tap()` в теле вашего компонента. Вы можете определить поведение жеста, наложив на него цепочку методов типа [`onBegin`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/tap-gesture/#onbegincallback), [`onStart`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/tap-gesture/#onstartcallback), [`onEnd`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/tap-gesture/#onendcallback) или [`onFinalize`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/tap-gesture/#onfinalizecallback). Мы будем использовать их для обновления общего значения сразу после начала жеста и возврата к исходному значению по его завершении.

```js
import 'react-native-gesture-handler';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withTiming,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

export default function App() {
    const pressed = useSharedValue(false);

    const tap = Gesture.Tap()
        .onBegin(() => {
            pressed.value = true;
        })
        .onFinalize(() => {
            pressed.value = false;
        });

    const animatedStyles = useAnimatedStyle(() => ({
        backgroundColor: pressed.value
            ? '#FFE04B'
            : '#B58DF1',
        transform: [
            { scale: withTiming(pressed.value ? 1.2 : 1) },
        ],
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View style={styles.container}>
                <GestureDetector gesture={tap}>
                    <Animated.View
                        style={[
                            styles.circle,
                            animatedStyles,
                        ]}
                    />
                </GestureDetector>
            </View>
        </GestureHandlerRootView>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    circle: {
        height: 120,
        width: 120,
        borderRadius: 500,
    },
});
```

Вы можете спокойно обращаться к общим значениям, поскольку обратные вызовы, передаваемые жестам, автоматически [workletized](glossary.md#to-workletize) для вас.

Мы хотим, чтобы наш круг менял цвет с фиолетового на желтый и плавно масштабировался на 20% при касании. Определим логику анимации с помощью `withTiming` в `useAnimatedStyle`:

```js
import 'react-native-gesture-handler';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withTiming,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

export default function App() {
    const pressed = useSharedValue(false);

    const tap = Gesture.Tap()
        .onBegin(() => {
            pressed.value = true;
        })
        .onFinalize(() => {
            pressed.value = false;
        });

    const animatedStyles = useAnimatedStyle(() => ({
        backgroundColor: pressed.value
            ? '#FFE04B'
            : '#B58DF1',
        transform: [
            { scale: withTiming(pressed.value ? 1.2 : 1) },
        ],
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View style={styles.container}>
                <GestureDetector gesture={tap}>
                    <Animated.View
                        style={[
                            styles.circle,
                            animatedStyles,
                        ]}
                    />
                </GestureDetector>
            </View>
        </GestureHandlerRootView>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    circle: {
        height: 120,
        width: 120,
        borderRadius: 500,
    },
});
```

Определенный жест необходимо передать в пропсы `gesture` компонента [`GestureDetector`](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/gesture-detector). Этот компонент должен обернуть представление, для которого вы хотите обрабатывать жесты. Также не забудьте передать определенные `animatedStyles` в представление, которое вы хотите анимировать, следующим образом:

```js
import 'react-native-gesture-handler';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withTiming,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

export default function App() {
    const pressed = useSharedValue(false);

    const tap = Gesture.Tap()
        .onBegin(() => {
            pressed.value = true;
        })
        .onFinalize(() => {
            pressed.value = false;
        });

    const animatedStyles = useAnimatedStyle(() => ({
        backgroundColor: pressed.value
            ? '#FFE04B'
            : '#B58DF1',
        transform: [
            { scale: withTiming(pressed.value ? 1.2 : 1) },
        ],
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View style={styles.container}>
                <GestureDetector gesture={tap}>
                    <Animated.View
                        style={[
                            styles.circle,
                            animatedStyles,
                        ]}
                    />
                </GestureDetector>
            </View>
        </GestureHandlerRootView>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    circle: {
        height: 120,
        width: 120,
        borderRadius: 500,
    },
});
```

С помощью [композиционных жестов](https://docs.swmansion.com/react-native-gesture-handler/docs/gesture-composition/) можно реализовать более сложное поведение. Но что, если мы хотим создать что-то более интересное?

## Обработка жестов панорамирования

Сделаем круг перетаскиваемым, а при отпускании он будет возвращаться в исходное положение. Сохраним также эффект выделения цветом и масштабирования, добавленный в предыдущем примере. Реализовать такое поведение простым жестом касания невозможно. Вместо этого нам необходимо использовать жест панорамирования.

К счастью, все жесты имеют схожий API, поэтому реализовать это практически просто: переименуйте жест `Tap` в `Pan` и добавьте в цепочку дополнительный метод `onChange`.

```js
import 'react-native-gesture-handler';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withSpring,
    withTiming,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

export default function App() {
    const pressed = useSharedValue(false);
    const offset = useSharedValue(0);

    const pan = Gesture.Pan()
        .onBegin(() => {
            pressed.value = true;
        })
        .onChange((event) => {
            offset.value = event.translationX;
        })
        .onFinalize(() => {
            offset.value = withSpring(0);
            pressed.value = false;
        });

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [
            { translateX: offset.value },
            { scale: withTiming(pressed.value ? 1.2 : 1) },
        ],
        backgroundColor: pressed.value
            ? '#FFE04B'
            : '#b58df1',
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View style={styles.container}>
                <GestureDetector gesture={pan}>
                    <Animated.View
                        style={[
                            styles.circle,
                            animatedStyles,
                        ]}
                    />
                </GestureDetector>
            </View>
        </GestureHandlerRootView>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    circle: {
        height: 120,
        width: 120,
        backgroundColor: '#b58df1',
        borderRadius: 500,
        cursor: 'grab',
    },
});
```

Обратный вызов, переданный в `onChange`, поставляется с некоторыми [данными события](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/pan-gesture#event-data), которые имеют кучу удобных свойств. Одно из них - `translationX`, которое показывает, насколько объект переместился по оси X. Мы сохранили это значение в общем виде, чтобы соответствующим образом переместить окружность. Чтобы вернуть окружность в исходное положение, достаточно сбросить значение `offset.value` в методе `onFinalize`. Мы можем использовать функции `withSpring` или `withTiming` для анимации возврата.

Осталось только настроить логику в `useAnimatedStyle` для работы со смещением.

```js
import 'react-native-gesture-handler';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useAnimatedStyle,
    useSharedValue,
    withSpring,
    withTiming,
} from 'react-native-reanimated';
import {
    Gesture,
    GestureDetector,
    GestureHandlerRootView,
} from 'react-native-gesture-handler';

export default function App() {
    const pressed = useSharedValue(false);
    const offset = useSharedValue(0);

    const pan = Gesture.Pan()
        .onBegin(() => {
            pressed.value = true;
        })
        .onChange((event) => {
            offset.value = event.translationX;
        })
        .onFinalize(() => {
            offset.value = withSpring(0);
            pressed.value = false;
        });

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [
            { translateX: offset.value },
            { scale: withTiming(pressed.value ? 1.2 : 1) },
        ],
        backgroundColor: pressed.value
            ? '#FFE04B'
            : '#b58df1',
    }));

    return (
        <GestureHandlerRootView style={styles.container}>
            <View style={styles.container}>
                <GestureDetector gesture={pan}>
                    <Animated.View
                        style={[
                            styles.circle,
                            animatedStyles,
                        ]}
                    />
                </GestureDetector>
            </View>
        </GestureHandlerRootView>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    circle: {
        height: 120,
        width: 120,
        backgroundColor: '#b58df1',
        borderRadius: 500,
        cursor: 'grab',
    },
});
```

## Использование `withDecay`

Помните, некоторое время назад мы говорили, что еще вернемся к `withDecay`? Сейчас самое время!

`withDecay` позволяет сохранять скорость жеста и выполнять анимацию с некоторым замедлением. Это означает, что, отпустив захваченный объект с некоторой скоростью, можно медленно довести его до остановки. Звучит сложно, но на самом деле это не так!

Просто передайте конечную скорость в методе `onFinalize` свойству `velocity` функции `withDecay` и позвольте Reanimated сделать это за вас. Для сохранения нового положения объекта обновите изменение по оси X в методе `onChange` следующим образом:

```js
const pan = Gesture.Pan()
    .onChange((event) => {
        offset.value += event.changeX;
    })
    .onFinalize((event) => {
        offset.value = withDecay({
            velocity: event.velocityX,
            rubberBandEffect: true,
            clamp: [
                -(width.value / 2) + SIZE / 2,
                width.value / 2 - SIZE / 2,
            ],
        });
    });
```

Остальная часть кода нужна только для того, чтобы квадрат оставался внутри экрана.

Обязательно ознакомьтесь с полным справочником [`withDecay` API reference](../animations/withDecay.md), чтобы получить представление об остальных параметрах настройки.

## Резюме

В этом разделе мы рассмотрели основы работы с жестами с помощью Reanimated и Gesture Handler. Мы узнали о жестах `Tap` и `Pan`, а также о функции `withDecay`. Подведем итоги:

-   Reanimated интегрируется с другим пакетом под названием React Native Gesture Handler для обеспечения бесшовного взаимодействия.
-   Мы создаем новые жесты, такие как `Gesture.Pan()` или `Gesture.Tap()`, и передаем их в `GestureDetector`, который должен обернуть элемент, с которым мы хотим осуществлять взаимодействие.
-   Вы можете получать доступ к общим значениям и изменять их внутри обратных вызовов жестов без дополнительных шаблонов.
-   Функция `withDecay` позволяет создавать замедляющуюся анимацию на основе скорости, поступающей от жеста.

## Что дальше?

В этой статье мы лишь поверхностно рассмотрели возможности использования жестов в Reanimated. Помимо жестов [Tap](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/tap-gesture) и [Pan](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/pan-gesture), обработчик жестов содержит множество других, например, [Pinch](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/pinch-gesture) или [Fling](https://docs.swmansion.com/react-native-gesture-handler/docs/api/gestures/fling-gesture). Мы приглашаем вас погрузиться в раздел [Quick start](https://docs.swmansion.com/react-native-gesture-handler/docs/quickstart) документации по React Native Gesture Handler и изучить все возможности, которые предоставляет эта библиотека.

В [следующем разделе](customizing-animation.md) мы узнаем больше об анимации цветов.
