---
description: В этом руководстве мы будем вызывать функцию или отображать что-либо при фокусировке экрана
---

# Вызов функции при смене фокуса экрана

В этом руководстве мы будем вызывать функцию или отображать что-либо при фокусировке экрана. Это полезно для выполнения дополнительных вызовов API, когда пользователь возвращается к определенному экрану в Tab Navigator, или для отслеживания событий пользователя при касании экрана в нашем приложении.

Существует несколько подходов:

1.  Прослушивание события `'focus'` с помощью слушателя событий.
2.  Использование хука `useFocusEffect`, предоставляемого react-navigation.
3.  Использование хука `useIsFocused`, предоставляемого react-navigation.

## Запуск действия с помощью слушателя события `'focus'`

Мы также можем прослушивать событие `'focus'` с помощью слушателя событий. После настройки слушателя мы должны также прекратить прослушивание события, когда экран будет размонтирован.

При таком подходе мы сможем вызывать действие только тогда, когда экран сфокусируется. Это удобно для выполнения такого действия, как запись в журнал вида экрана для аналитики.

Пример:

```js
import * as React from 'react';
import { View } from 'react-native';

function ProfileScreen({ navigation }) {
    React.useEffect(() => {
        const unsubscribe = navigation.addListener(
            'focus',
            () => {
                // The screen is focused
                // Call any action
            }
        );

        // Return the function to unsubscribe from the event so it gets removed on unmount
        return unsubscribe;
    }, [navigation]);

    return <View />;
}
```

Более подробная информация об API слушателей событий приведена в руководстве [navigation events guide](navigation-events.md).

В большинстве случаев рекомендуется использовать хук `useFocusEffect` вместо добавления слушателя вручную. Подробности см. ниже.

## Запуск действия с помощью хука `useFocusEffect`

React Navigation предоставляет [хук](https://reactdev.ru/reference/hooks/), который запускает эффект, когда экран входит в фокус, и очищает его, когда он выходит из фокуса. Это полезно для таких случаев, как добавление слушателей событий, получение данных с помощью вызова API, когда экран становится сфокусированным, или для любых других действий, которые должны происходить, как только экран становится видимым.

Это особенно удобно, когда мы пытаемся остановить какое-либо действие, когда страница расфокусирована, например, остановить воспроизведение видео- или аудиофайла или остановить отслеживание местоположения пользователя.

```js
import { useFocusEffect } from '@react-navigation/native';

function Profile({ userId }) {
    const [user, setUser] = React.useState(null);

    useFocusEffect(
        React.useCallback(() => {
            const unsubscribe = API.subscribe(
                userId,
                (user) => setUser(data)
            );

            return () => unsubscribe();
        }, [userId])
    );

    return <ProfileContent user={user} />;
}
```

Подробнее см. документацию по [`useFocusEffect`](use-focus-effect.md).

## Рендеринг экрана с помощью хука `useIsFocused`.

React Navigation предоставляет [хук](https://reactdev.ru/reference/hooks/), который возвращает булево число, указывающее, сфокусирован экран или нет.

Хук будет возвращать `true`, когда экран сфокусирован, и `false`, когда наш компонент больше не сфокусирован. Это позволяет нам отображать что-либо условно в зависимости от того, находится ли пользователь на экране или нет.

Хук `useIsFocused` заставит наш компонент перерисовываться при фокусировке и разфокусировке экрана. Использование этого хука может привести к ненужным повторным рендерам компонента, когда экран входит и выходит из фокуса. Это может привести к проблемам, зависящим от типа действия, которое мы вызываем при фокусировке. Поэтому мы рекомендуем использовать этот хук только в том случае, если необходимо вызвать повторный рендеринг. Для таких побочных эффектов, как подписка на события или получение данных, используйте методы, описанные выше.

```js
import * as React from 'react';
import { Text } from 'react-native';
import { useIsFocused } from '@react-navigation/native';

function Profile() {
    // This hook returns `true` if the screen is focused, `false` otherwise
    const isFocused = useIsFocused();

    return (
        <Text>{isFocused ? 'focused' : 'unfocused'}</Text>
    );
}
```

Этот пример также описан в документации по API [`useIsFocused`](use-is-focused.md).

## Ссылки

-   [Call a function when focused screen changes](https://reactnavigation.org/docs/function-after-focusing-screen)
