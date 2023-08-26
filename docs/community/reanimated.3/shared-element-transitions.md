---
description: Функция Shared Element Transitions является экспериментальной и пока не рекомендуется для использования в производстве. Мы ждем ваших отзывов для улучшения реализации
---

# Переходы по общим элементам

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

!!!warning ""

    Функция Shared Element Transitions является экспериментальной и пока не рекомендуется для использования в производстве. Мы ждем ваших отзывов для улучшения реализации.

Shared Element Transition позволяет плавно трансформировать компонент с одного экрана в компонент на другом экране. Когда Reanimated обнаруживает, что компонент с тегом `sharedTransitionTag` монтируется или демонтируется, он пытается найти последнее зарегистрированное представление с тем же тегом `sharedTransitionTag`. Если найдены два совпадающих компонента, то делается снимок стилей для обоих компонентов, и оба общих представления отсоединяются от своего родителя и присоединяются к временному контейнеру переходов на время анимации. После завершения анимации они снова присоединяются к своему родителю. Если не создавать пользовательскую анимацию, то все свойства снимка, включая `width`, `height`, `originX`, `originY` и `transformMatrix`, по умолчанию анимируются с длительностью 500 мс с помощью анимации `withTiming`.

## Как его использовать?

Чтобы создать общую анимацию перехода между двумя компонентами на разных экранах, достаточно присвоить обоим компонентам один и тот же тег `sharedTransitionTag`. При переходе между экранами будет автоматически воспроизводиться анимация общего перехода. Функция общего перехода работает путем поиска двух компонентов, зарегистрированных с одним и тем же тегом `sharedTransitionTag`. Если вы хотите использовать более одного общего представления на одном экране, обязательно назначьте каждому компоненту уникальный тег общего перехода.

**Screen A**

```ts
<View
    sharedTransitionTag="sharedTag"
    style={{
        width: 150,
        height: 150,
        backgroundColor: 'green',
    }}
/>
```

**Screen B**

```ts
<View
    sharedTransitionTag="sharedTag"
    style={{
        width: 100,
        height: 100,
        backgroundColor: 'green',
    }}
/>
```

## Пользовательская анимация

1.  Необходимо создать пользовательский переход с помощью Reanimated API, например:

    ```js
    import { SharedTransition } from 'react-native-reanimated';
    const transition = SharedTransition.custom((values) => {
        'worklet';
        return {
            height: withSpring(values.targetHeight),
            width: withSpring(values.targetWidth),
        };
    });
    ```

2.  В оба компонента (на обоих экранах) необходимо добавить пользовательский переход в качестве свойства `sharedTransitionStyle`:

    ```ts
    <View
        sharedTransitionTag="reanimatedTransition"
        sharedTransitionStyle={transition}
        style={{
            backgroundColor: 'blue',
            width: 200,
            height: 100,
        }}
    />
    ```

## Примеры

Другие примеры использования вы можете найти в **[Реанимированный пример приложения](https://github.com/software-mansion/react-native-reanimated/tree/3.3.0/app/src/examples/SharedElementTransitions)**.

```ts
import * as React from 'react';
import { View, Button } from 'react-native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { NavigationContainer } from '@react-navigation/native';
import Animated from 'react-native-reanimated';

const Stack = createNativeStackNavigator();

function Screen1({ navigation }) {
    return (
        <View style={{ flex: 1 }}>
            <Animated.View
                style={{
                    width: 150,
                    height: 150,
                    backgroundColor: 'green',
                }}
                sharedTransitionTag="sharedTag"
            />
            <Button
                title="Screen2"
                onPress={() =>
                    navigation.navigate('Screen2')
                }
            />
        </View>
    );
}

function Screen2({ navigation }) {
    return (
        <View style={{ flex: 1, marginTop: 50 }}>
            <Animated.View
                style={{
                    width: 100,
                    height: 100,
                    backgroundColor: 'green',
                }}
                sharedTransitionTag="sharedTag"
            />
            <Button
                title="Screen1"
                onPress={() =>
                    navigation.navigate('Screen1')
                }
            />
        </View>
    );
}

export default function SharedElementExample() {
    return (
        <NavigationContainer>
            <Stack.Navigator
                screenOptions={{ headerShown: true }}
            >
                <Stack.Screen
                    name="Screen1"
                    component={Screen1}
                />
                <Stack.Screen
                    name="Screen2"
                    component={Screen2}
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
}
```

## Ограничения и известные проблемы

-   Поддерживается только родной стек.
-   При использовании общего перехода можно анимировать только свойства `width`, `height`, `originX`, `originY` и `transformMatrix`.
-   Макет для дочерних элементов общего представления не вычисляется во время перехода.
-   Текущая реализация поддерживает только старую архитектуру React Native (Paper)

## Планы на будущее

-   Включить анимацию всех возможных стилей при совместном переходе.
-   Вычисление макета для дочерних элементов общего представления во время перехода.
-   Добавить возможность реализовать собственный экранный переход с настраиваемой анимацией при смене экрана.
-   Обеспечить работу общего перехода для компонентов, расположенных в пределах одного экрана.
-   Создание анимации перехода на основе прогресса, управляемой пролистыванием назад (только для iOS).
-   Поддержка новой архитектуры React Native (Fabric)

## Ссылки

-   [Shared Element Transitions](https://docs.swmansion.com/react-native-reanimated/docs/shared-element-transitions/overview)
