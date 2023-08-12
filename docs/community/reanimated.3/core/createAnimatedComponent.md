---
description: Функция createAnimatedComponent позволяет создать анимированную версию любого компонента React Native
---

# createAnimatedComponent

Функция **`createAnimatedComponent`** позволяет создать анимированную версию любого компонента React Native. Обертывание компонента с помощью `createAnimatedComponent` позволяет Reanimated анимировать любые свойства или стили, связанные с этим компонентом.

Reanimated поставляется с пятью встроенными анимированными компонентами:

-   `Animated.FlatList`
-   `Animated.Image`
-   `Animated.View`
-   `Animated.ScrollView`
-   `Animated.Text`

Остальные компоненты, которые вы захотите анимировать в React Native, должны быть обернуты функцией `createAnimatedComponent`.

## Описание

```js
import Animated from 'react-native-reanimated';
import { TextInput } from 'react-native';

const AnimatedTextInput = Animated.createAnimatedComponent(
    TextInput
);
```

!!!note "Типизация"

    ```typescript
    function createAnimatedComponent<P extends object>(
    	component: ComponentClass<P>
    ): ComponentClass<AnimateProps<P>>;

    function createAnimatedComponent<P extends object>(
    	component: FunctionComponent<P>
    ): FunctionComponent<AnimateProps<P>>;
    ```

### Аргументы

#### `component`

Компонент, который вы хотите сделать анимируемым. Функциональные компоненты должны быть обернуты с помощью `React.forwardRef()`.

### Возвращает

`createAnimatedComponent` возвращает компонент, который Reanimated способен анимировать.

## Пример

```js
import React from 'react';
import { StyleSheet, View, Button } from 'react-native';
import Animated, {
    useSharedValue,
    withSpring,
} from 'react-native-reanimated';

const MyView = React.forwardRef((props, ref) => {
    // some additional logic
    return <View ref={ref} {...props} />;
});

// highlight-next-line
const MyAnimatedView = Animated.createAnimatedComponent(
    MyView
);

export default function App() {
    const width = useSharedValue(100);

    const handlePress = () => {
        width.value = withSpring(width.value + 50);
    };

    return (
        <View style={styles.container}>
            <MyAnimatedView
                style={{ ...styles.box, width }}
            />
            <Button
                onPress={handlePress}
                title="Click me"
            />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
    },
    box: {
        height: 100,
        backgroundColor: '#b58df1',
        borderRadius: 20,
        marginVertical: 64,
    },
});
```

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [createAnimatedComponent](https://docs.swmansion.com/react-native-reanimated/docs/core/createAnimatedComponent/)
