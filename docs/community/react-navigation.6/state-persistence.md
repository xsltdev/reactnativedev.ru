---
description: Возможно, вам захочется сохранить местоположение пользователя в приложении, чтобы после перезапуска приложения он сразу же возвращался в то же место
---

# Сохранение состояния

Возможно, вам захочется сохранить местоположение пользователя в приложении, чтобы после перезапуска приложения он сразу же возвращался в то же место.

Это особенно важно при разработке, поскольку позволяет разработчику оставаться на том же экране при обновлении приложения.

## Использование

Для сохранения состояния [navigation state](navigation-state.md) мы можем использовать реквизиты `onStateChange` и `initialState` контейнера.

-   `onStateChange` - этот реквизит уведомляет нас о любых изменениях состояния. Мы можем сохранять состояние в этом обратном вызове.
-   `initialState` - Этот реквизит позволяет нам передать начальное состояние для использования в [navigation state](navigation-state.md). Мы можем передать восстановленное состояние в этом реквизите.

```js
import * as React from 'react';
import { Linking, Platform } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { NavigationContainer } from '@react-navigation/native';

const PERSISTENCE_KEY = 'NAVIGATION_STATE_V1';

export default function App() {
    const [isReady, setIsReady] = React.useState(false);
    const [
        initialState,
        setInitialState,
    ] = React.useState();

    React.useEffect(() => {
        const restoreState = async () => {
            try {
                const initialUrl = await Linking.getInitialURL();

                if (
                    Platform.OS !== 'web' &&
                    initialUrl == null
                ) {
                    // Only restore state if there's no deep link and we're not on web
                    const savedStateString = await AsyncStorage.getItem(
                        PERSISTENCE_KEY
                    );
                    const state = savedStateString
                        ? JSON.parse(savedStateString)
                        : undefined;

                    if (state !== undefined) {
                        setInitialState(state);
                    }
                }
            } finally {
                setIsReady(true);
            }
        };

        if (!isReady) {
            restoreState();
        }
    }, [isReady]);

    if (!isReady) {
        return null;
    }

    return (
        <NavigationContainer
            initialState={initialState}
            onStateChange={(state) =>
                AsyncStorage.setItem(
                    PERSISTENCE_KEY,
                    JSON.stringify(state)
                )
            }
        >
            {/* ... */}
        </NavigationContainer>
    );
}
```

### Режим разработки

Эта функция особенно полезна в режиме разработки. Вы можете включить ее выборочно, используя следующий подход:

```js
const [isReady, setIsReady] = React.useState(
    __DEV__ ? false : true
);
```

Хотя его можно использовать и в производстве, следует соблюдать осторожность, поскольку он может сделать приложение непригодным для использования, если оно аварийно завершает работу на определенном экране - ведь после перезапуска пользователь все равно будет находиться на том же экране.

### Loading View

Поскольку состояние восстанавливается асинхронно, приложение должно отобразить пустой/загрузочный вид на некоторое время, прежде чем мы получим начальное состояние. Чтобы справиться с этим, мы можем возвращать загрузочное представление, когда `isReady` равно `false`:

```js
if (!isReady) {
    return <ActivityIndicator />;
}
```

## Предупреждение: Сериализуемое состояние

Для работы этой функции состояние каждого параметра, маршрута и навигации должно быть полностью сериализуемым. Обычно состояние сериализуется в виде JSON-строки. Это означает, что маршруты и параметры не должны содержать функций, экземпляров классов или рекурсивных структур данных. React Navigation уже [предупреждает вас во время разработки](troubleshooting.md#i-get-the-warning-"non-serializable-values-were-found-in-the-navigation-state"), если встречает несериализуемые данные, поэтому обратите внимание на это предупреждение, если вы планируете сохранять состояние навигации.

Вы можете изменить объект начального состояния перед передачей его контейнеру, но учтите, что если ваш `initialState` не является [действительным состоянием навигации](navigation-state.md#partial-state-objects), то React Navigation может не справиться с ситуацией изящно.

## Ссылки

-   [State persistence](https://reactnavigation.org/docs/state-persistence/)
