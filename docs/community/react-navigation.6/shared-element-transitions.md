---
description: В этом руководстве рассказывается о том, как анимировать элементы между экранами
---

# Анимация элементов между экранами

В этом руководстве рассказывается о том, как анимировать элементы между экранами. Эта возможность известна как [Shared Element Transition](https://docs.swmansion.com/react-native-reanimated/docs/api/sharedElementTransitions) и реализована в [`@react-navigation/native-stack`](native-stack-navigator.md) с помощью [React Native Reanimated](https://docs.swmansion.com/react-native-reanimated/).

!!!note "Примечание"

    На момент написания данного руководства переходы между общими элементами считаются экспериментальной функцией, не рекомендуемой для использования в производстве.

![Анимация элементов между экранами](shared-element-transitions.apng)

## Предварительные условия

Прежде чем продолжить работу над этим руководством, убедитесь, что ваше приложение соответствует этим критериям:

-   Вы используете [`@react-navigation/native-stack`](native-stack-navigator.md). Функция Shared Element Transitions не поддерживается в JS-основе [`@react-navigation/stack`](stack-navigator.md).
-   У вас установлен и настроен [`react-native-reanimated`](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation) **v3.0.0 или выше**.

## Минимальный пример

Чтобы создать общий переход, выполните следующие действия:

1.  Используйте компоненты `Animated`, импортированные из `react-native-reanimated`.
2.  Присвойте один и тот же тег `sharedTransitionTag` элементам на разных экранах.
3.  Перемещайтесь между экранами. Переход начнется автоматически.

```js
import * as React from 'react';
import { View, Button, StyleSheet } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

import Animated from 'react-native-reanimated';

// highlight-next-line
const Stack = createNativeStackNavigator();

function HomeScreen({ navigation }) {
    return (
        <View style={styles.container}>
            <Button
                title="Go to Details"
                onPress={() =>
                    navigation.navigate('Details')
                }
            />
            <Animated.Image
                source={{
                    uri: 'https://picsum.photos/id/39/200',
                }}
                style={{ width: 300, height: 300 }}
                // highlight-next-line
                sharedTransitionTag="tag"
            />
        </View>
    );
}

function DetailsScreen({ navigation }) {
    return (
        <View style={styles.container}>
            <Button
                title="Go back"
                onPress={() => navigation.goBack()}
            />
            <Animated.Image
                source={{
                    uri: 'https://picsum.photos/id/39/200',
                }}
                style={{ width: 100, height: 100 }}
                // highlight-next-line
                sharedTransitionTag="tag"
            />
        </View>
    );
}

export default function App() {
    return (
        <NavigationContainer>
            <Stack.Navigator>
                <Stack.Screen
                    name="Home"
                    component={HomeScreen}
                />
                <Stack.Screen
                    name="Details"
                    component={DetailsScreen}
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
    },
});
```

`sharedTransitionTag` - это строка, которая должна быть уникальной в контексте одного экрана, но должна соответствовать элементам между экранами. Этот реквизит позволяет Reanimated идентифицировать и анимировать элементы, аналогично свойству [`key`](https://reactdev.ru/learn/rendering-lists#keeping-list-items-in-order-with-key), которое сообщает React, какой элемент в списке является таковым.

## Настройка перехода

По умолчанию переход анимирует свойства `width`, `height`, `originX`, `originY` и `transform` с помощью `withTiming` с длительностью 500 мс. Вы можете легко настроить реквизиты `width`, `height`, `originX` и `originY`. Также возможна настройка `transform`, но это выходит далеко за рамки данного руководства.

!!!note "Примечание"

    API пользовательского перехода SharedTransition не доработан и может измениться в будущем выпуске.

Для настройки перехода необходимо передать все свойства, кроме `transform`.

```js
import { SharedTransition } from 'react-native-reanimated';

const customTransition = SharedTransition.custom(
    (values) => {
        'worklet';
        return {
            height: withSpring(values.targetHeight),
            width: withSpring(values.targetWidth),
            originX: withSpring(values.targetOriginX),
            originY: withSpring(values.targetOriginY),
        };
    }
);

function HomeScreen() {
    return (
        <Animated.Image
            style={{ width: 300, height: 300 }}
            sharedTransitionTag="tag"
            // highlight-next-line
            // add this to both elements on both screens
            sharedTransitionStyle={customTransition}
        />
    );
}
```

## Ссылка

Полную справку по переходам между общими элементами можно найти в [документации по React Native Reanimated](https://docs.swmansion.com/react-native-reanimated/docs/api/sharedElementTransitions).

## Альтернативы

В качестве альтернативы можно использовать библиотеку [`react-native-shared-element`](https://github.com/IjzerenHein/react-native-shared-element) с привязкой [React Navigation](https://github.com/IjzerenHein/react-navigation-shared-element), которая реализует переходы между общими элементами в навигаторе `@react-navigation/stack` на основе JS. Однако это решение активно не поддерживается.

В [`react-native-navigation`](https://github.com/wix/react-native-navigation) также реализована поддержка Shared Element Transitions. Подробнее об этом можно прочитать [здесь](https://wix.github.io/react-native-navigation/docs/style-animations#shared-element-transitions).

## Ссылки

-   [Animating elements between screens](https://reactnavigation.org/docs/shared-element-transitions)
