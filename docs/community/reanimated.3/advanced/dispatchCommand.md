---
description: Позволяет синхронно с потоком UI диспетчеризировать команду нативного компонента
---

# dispatchCommand

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Позволяет синхронно с потоком UI диспетчеризировать команду нативного компонента.

## Аргументы

### `animatedRef`

Продукт [`useAnimatedRef`](../core/useAnimatedRef.md), который является расширением Reanimated для стандартного React ref (доставляет тег view в потоке UI).

### `commandName` [String]

Имя команды, которую необходимо выполнить, например, `focus` или `scrollToEnd`.

### `args` [Array<any\>]

Массив аргументов команды. По умолчанию используется пустой массив.

## Возвращает

`void`

## Пример

```ts
import Animated, {
    dispatchCommand,
    useAnimatedRef,
} from 'react-native-reanimated';
import {
    Button,
    StyleSheet,
    TextInput,
    View,
} from 'react-native';
import {
    Gesture,
    GestureDetector,
} from 'react-native-gesture-handler';

import React from 'react';

const AnimatedTextInput = Animated.createAnimatedComponent(
    TextInput
);

export default function DispatchCommandExample() {
    const aref = useAnimatedRef<TextInput>();

    const gesture = Gesture.Tap().onStart(() => {
        dispatchCommand(aref, 'focus');
    });

    return (
        <View style={styles.container}>
            <AnimatedTextInput
                ref={aref}
                style={styles.input}
            />
            <GestureDetector gesture={gesture}>
                <Button title="Focus" />
            </GestureDetector>
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    },
    input: {
        borderWidth: 1,
        width: 200,
        padding: 5,
    },
});
```

## Ссылки

-   [dispatchCommand](https://docs.swmansion.com/react-native-reanimated/docs/advanced/dispatchCommand)
