# Перемещение между экранами

В предыдущем разделе ["Hello React Navigation"](hello-react-navigation.md) мы определили стековый навигатор с двумя маршрутами (`Home` и `Details`), но мы не узнали, как позволить пользователю переходить от `Home` к `Details` (хотя мы узнали, как изменить _начальный_ маршрут в нашем коде, но заставлять пользователей клонировать наш репозиторий и менять маршрут в нашем коде, чтобы увидеть другой экран, - это, пожалуй, один из худших пользовательских опытов, который можно себе представить).

Если бы это был веб-браузер, мы могли бы написать что-то вроде этого:

```js
<a href="details.html">Go to Details</a>
```

По-другому это можно записать так:

```js
<a
    onClick={() => {
        window.location.href = 'details.html';
    }}
>
    Go to Details
</a>
```

Мы сделаем нечто похожее на последнее, но вместо глобального параметра `window.location` будем использовать параметр `navigation`, который передается нашим компонентам экрана.

## Переход к новому экрану

```js
import * as React from 'react';
import { Button, View, Text } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

function HomeScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Text>Home Screen</Text>
            <Button
                title="Go to Details"
                onPress={() =>
                    navigation.navigate('Details')
                }
            />
        </View>
    );
}

// ... other code from the previous section
```

Давайте разделим их на части:

-   `navigation` - реквизит `navigation` передается в каждый **экранный компонент** ([definition](glossary-of-terms.md#screen-component)) в родном стеке-навигаторе (подробнее об этом в ["The navigation prop in depth"](navigation-prop.md)).
-   `navigate('Details')` - мы вызываем функцию `navigate` (на реквизите `navigation` &mdash; именовать сложно!) с именем маршрута, на который мы хотим переместить пользователя.

!!!note ""

    Если вызвать `navigation.navigate` с именем маршрута, который мы не определили в навигаторе, то в сборках разработки будет выведена ошибка, а в сборках производства ничего не произойдет. Другими словами, мы можем переходить только к тем маршрутам, которые были определены в нашем навигаторе &mdash; мы не можем перейти к произвольному компоненту.

Итак, у нас есть стек с двумя маршрутами:

1.  маршрут `Home` и
2.  маршрут `Details`.

Что произойдет, если мы снова перейдем к маршруту `Details` из экрана `Details`?

## Переход к маршруту несколько раз

```js
function DetailsScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Text>Details Screen</Text>
            <Button
                title="Go to Details... again"
                onPress={() =>
                    navigation.navigate('Details')
                }
            />
        </View>
    );
}
```

Если вы запустите этот код, то заметите, что при нажатии кнопки "Перейти к деталям... снова" он ничего не делает! Это происходит потому, что мы уже находимся на маршруте "Подробности". Функция `navigate` примерно означает "перейти на этот экран", и если вы уже находитесь на этом экране, то вполне логично, что она ничего не сделает.

Предположим, что на самом деле мы хотим добавить еще один экран подробностей. Это довольно часто встречается в случаях, когда каждому маршруту передаются какие-то уникальные данные (подробнее об этом мы поговорим позже, когда будем обсуждать `params`!) Для этого мы можем изменить `navigate` на `push`. Это позволит нам выразить намерение добавить еще один маршрут независимо от существующей истории навигации.

```js
<Button
    title="Go to Details... again"
    onPress={() => navigation.push('Details')}
/>
```

![type:video](./stack-push.mp4)

Каждый раз при вызове `push` мы добавляем новый маршрут в стек навигации. Когда вы вызываете `navigate`, он сначала пытается найти существующий маршрут с таким именем и проталкивает новый маршрут только в том случае, если в стеке его еще нет.

## Возвращение назад

Заголовок, предоставляемый родным стековым навигатором, автоматически включает кнопку "Назад", если есть возможность вернуться с активного экрана (если в навигационном стеке только один экран, то вернуться не к чему, и кнопки "Назад" нет).

Иногда возникает необходимость в программном включении этого поведения, для чего можно использовать `navigation.goBack();`.

```js
function DetailsScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Text>Details Screen</Text>
            <Button
                title="Go to Details... again"
                onPress={() => navigation.push('Details')}
            />
            <Button
                title="Go to Home"
                onPress={() => navigation.navigate('Home')}
            />
            <Button
                title="Go back"
                onPress={() => navigation.goBack()}
            />
        </View>
    );
}
```

!!!note ""

    На Android функция React Navigation подключается к аппаратной кнопке "назад" и при ее нажатии вызывает функцию `goBack()`, так что пользователь ведет себя именно так, как ожидает.

Другим распространенным требованием является возможность возврата на _множество_ экранов назад - например, если вы находитесь на нескольких экранах в глубине стека и хотите отбросить их все, чтобы вернуться к первому экрану. В этом случае мы знаем, что хотим вернуться в `Home`, поэтому можем использовать `navigate('Home')` (а не `push`! попробуйте и увидите разницу). Другой альтернативой может быть `navigation.popToTop()`, которая возвращает к первому экрану в стеке.

```js
function DetailsScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Text>Details Screen</Text>
            <Button
                title="Go to Details... again"
                onPress={() => navigation.push('Details')}
            />
            <Button
                title="Go to Home"
                onPress={() => navigation.navigate('Home')}
            />
            <Button
                title="Go back"
                onPress={() => navigation.goBack()}
            />
            <Button
                title="Go back to first screen in stack"
                onPress={() => navigation.popToTop()}
            />
        </View>
    );
}
```

## Резюме

-   `navigation.navigate('RouteName')` проталкивает новый маршрут в нативный навигатор стека, если его еще нет в стеке, в противном случае он переходит на этот экран.
-   Мы можем вызывать `navigation.push('RouteName')` сколько угодно раз, и она будет продолжать проталкивать маршруты.
-   В строке заголовка автоматически отображается кнопка "Назад", но вы можете программно вернуться назад, вызвав `navigation.goBack()`. На Android аппаратная кнопка "назад" работает так, как и ожидалось.
-   Вы можете вернуться к существующему экрану в стеке с помощью `navigation.navigate('RouteName')`, а также вернуться к первому экрану в стеке с помощью `navigation.popToTop()`.
-   Свойство `navigation` доступно всем компонентам экрана (компонентам, определенным как экраны в конфигурации маршрута и отображаемым React Navigation как маршрут).

## Ссылки

-   [https://reactnavigation.org/docs/navigating](https://reactnavigation.org/docs/navigating)
