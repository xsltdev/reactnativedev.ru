---
description: withTiming позволяет создать анимацию на основе длительности и easing
---

# withTiming

**`withTiming`** позволяет создать анимацию на основе длительности и [easing](https://easings.net/).

```js
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withTiming,
    withRepeat,
} from 'react-native-reanimated';

export default function App() {
    const offset = useSharedValue(200);

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    React.useEffect(() => {
        offset.value = withRepeat(
            // highlight-next-line
            withTiming(-offset.value, { duration: 1500 }),
            -1,
            true
        );
    }, []);

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedStyles]}
            />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    box: {
        height: 120,
        width: 120,
        backgroundColor: '#b58df1',
        borderRadius: 20,
    },
});
```

## Определение

```js
import { withTiming } from 'react-native-reanimated';

function App() {
    sv.value = withTiming(0);
    // ...
}
```

!!!note "Типизация"

    ```ts
    type AnimatableValue = number | string | number[];

    interface WithTimingConfig {
    	duration?: number;
    	easing?: EasingFunction;
    }

    function withTiming<T extends AnimatableValue>(
    	toValue: T,
    	config?: WithTimingConfig,
    	callback?: (
    		finished?: boolean,
    		current?: AnimatableValue
    	) => void
    ): T;
    ```

### Аргументы

#### `toValue`

Значение, при котором анимация придет в состояние покоя.

#### `config`

Конфигурация анимации синхронизации.

Доступные свойства:

| Имя                      | Тип      | По-умолчанию                | Описание                                      |
| ------------------------ | -------- | --------------------------- | --------------------------------------------- |
| `duration` (опционально) | `number` | `300`                       | Длина анимации (в миллисекундах).             |
| `easing` (опционально)   | `Easing` | `Easing.inOut(Easing.quad)` | Функция easing, определяющая кривую анимации. |

##### `Easing`

Параметр `easing` позволяет точно настроить анимацию на заданную длительность. Например, можно сделать так, чтобы анимация начиналась с быстрого ускорения, а затем замедлялась к концу, или начиналась медленно, затем набирала скорость и снова замедлялась к концу.

Все это становится понятным, если сравнить `линейное` ослабление с ослаблением по умолчанию `Easing.inOut(Easing.quad)`.

```js
import React from 'react';
import { StyleSheet, View, Text } from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withTiming,
    Easing,
    withRepeat,
} from 'react-native-reanimated';

const duration = 2000;

export default function App() {
    const defaultAnim = useSharedValue(200);
    const linear = useSharedValue(200);

    const animatedDefault = useAnimatedStyle(() => ({
        transform: [{ translateX: defaultAnim.value }],
    }));
    const animatedChanged = useAnimatedStyle(() => ({
        transform: [{ translateX: linear.value }],
    }));

    React.useEffect(() => {
        linear.value = withRepeat(
            // highlight-next-line
            withTiming(-linear.value, {
                duration,
                easing: Easing.linear,
            }),
            -1,
            true
        );
        defaultAnim.value = withRepeat(
            // highlight-next-line
            withTiming(-defaultAnim.value, {
                duration,
            }),
            -1,
            true
        );
    }, []);

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.box, animatedDefault]}
            >
                <Text style={styles.text}>inout</Text>
            </Animated.View>
            <Animated.View
                style={[styles.box, animatedChanged]}
            >
                <Text style={styles.text}>linear</Text>
            </Animated.View>
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    box: {
        height: 80,
        width: 80,
        margin: 20,
        borderWidth: 1,
        borderColor: '#b58df1',
        borderRadius: 20,
        alignItems: 'center',
        justifyContent: 'center',
    },
    text: {
        color: '#b58df1',
        textTransform: 'uppercase',
        fontWeight: 'bold',
    },
});
```

Reanimated предоставляет набор готовых к использованию функций смягчения в модуле `Easing`. Визуализацию некоторых распространенных функций смягчения можно найти на сайте [http://easings.net/](http://easings.net/).

Вы можете использовать наши встроенные функции смягчения, передавая их в качестве свойства `easing` в конфигурацию `withTiming`:

```js
import { Easing } from 'react-native-reanimated';

withTiming(sv.value, {
    easing: Easing.bounce,
});
```

Доступные функции:

-   `back` обеспечивает простую анимацию, при которой объект перед движением вперед немного отходит назад.
-   `bezier(x1: number, y1: number, x2: number, y2: number)` обеспечивает создание кубической кривой Безье
-   `bounce` обеспечивает анимацию подпрыгивания
-   `circle` предоставляет круговую функцию
-   `cubic` - кубическая функция
-   `ease` обеспечивает простую инерционную анимацию
-   `elastic(bounciness?: number)` обеспечивает простое пружинное взаимодействие
-   `exp` предоставляет экспоненциальную функцию
-   `linear` обеспечивает линейную функцию
-   `poly(n: number)` может быть использована для реализации квартовых, квинтовых и других функций высших степеней
-   `quad` - квадратичная функция
-   `sin` - синусоидальная функция

Следующие помощники используются для модификации других функций easing.

-   `in(easing: EasingFunction)` запускает функцию ослабления вперед
-   `inOut(easing: EasingFunction)` делает любую смягчающую функцию симметричной
-   `out(easing: EasingFunction)` запускает функцию ослабления в обратном направлении

#### `callback`

Функция, вызываемая по завершении анимации. Если анимация отменена, то в качестве аргумента обратного вызова будет получено `false`, в противном случае - `true`.

### Возвращает

`withTiming` возвращает объект [анимации](../fundamentals/glossary.md#animation-object). Он может быть либо напрямую присвоен [shared value](../fundamentals/glossary.md#shared-value), либо использован в качестве значения для объекта стиля, возвращаемого из [useAnimatedStyle](../core/useAnimatedStyle.md).

## Пример

```js
import React from 'react';
import {
    StyleSheet,
    View,
    Text,
    Pressable,
} from 'react-native';
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    withTiming,
} from 'react-native-reanimated';

const TAB_WIDTH = 150;
const TABS = ['Home', 'Search', 'Profile'];

export default function App() {
    const offset = useSharedValue(-TAB_WIDTH);

    const animatedStyles = useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
    }));

    const handlePress = (tab) => {
        const newOffset = (() => {
            switch (tab) {
                case 'Home':
                    return -TAB_WIDTH;
                case 'Search':
                    return 0;
                case 'Profile':
                    return TAB_WIDTH;
                default:
                    return -TAB_WIDTH;
            }
        })();

        offset.value = withTiming(newOffset);
    };

    return (
        <View style={styles.container}>
            <View style={styles.tabs}>
                {TABS.map((tab, i) => (
                    <Pressable
                        key={tab}
                        style={
                            i !== TABS.length - 1
                                ? [
                                      styles.tab,
                                      styles.divider,
                                  ]
                                : styles.tab
                        }
                        onPress={() => handlePress(tab)}
                    >
                        <Text style={styles.tabLabel}>
                            {tab}
                        </Text>
                    </Pressable>
                ))}
            </View>
            <Animated.View
                style={[
                    styles.animatedBorder,
                    animatedStyles,
                ]}
            />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: '100%',
    },
    tabs: {
        flexDirection: 'row',
    },
    tab: {
        paddingHorizontal: 20,
        paddingVertical: 10,
        width: TAB_WIDTH,
    },
    tabLabel: {
        fontSize: 20,
        textAlign: 'center',
        fontWeight: 'bold',
    },
    divider: {
        borderRightWidth: 1,
        borderRightColor: '#ddd',
    },
    animatedBorder: {
        height: 8,
        width: 64,
        backgroundColor: 'tomato',
        borderRadius: 20,
    },
});
```

## Замечания

-   Обратный вызов, переданный в 3-м аргументе, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и запускается на [UI thread](../fundamentals/glossary.md#ui-thread).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [withTiming](https://docs.swmansion.com/react-native-reanimated/docs/animations/withTiming/)
