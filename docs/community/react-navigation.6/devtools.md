---
description: Инструменты разработчика для облегчения отладки при использовании React Navigation
---

# Инструменты разработчика

Инструменты разработчика для облегчения отладки при использовании React Navigation.

Чтобы воспользоваться инструментами разработчика, установите [`@react-navigation/devtools`](https://github.com/react-navigation/react-navigation/tree/master/packages/devtools):

```bash
npm install @react-navigation/devtools
```

Хуки из этого пакета работают только во время разработки и отключаются в производстве. Чтобы удалить их из производственной сборки, не нужно делать ничего особенного.

## Определение API

Пакет предоставляет следующие API:

### `useFlipper`

Этот хук обеспечивает интеграцию с [Flipper](https://fbflipper.com/) для приложений React Native.

!!!note ""

    Он не работает в приложениях, управляемых Expo, поскольку они не поддерживают Flipper.

Чтобы использовать этот хук, необходимо:

-   [Настроить Flipper в вашем приложении React Native](https://fbflipper.com/docs/features/react-native/), если он еще не настроен.
-   Установить пакет `react-native-flipper` в ваше приложение:

    ```bash
    npm install --save-dev react-native-flipper
    ```

-   Установите плагин `react-navigation` в приложении Flipper

    ![Install Flipper](flipper-plugin-install.png)

**Использование:**.

Чтобы использовать хук, импортируйте его и передайте в качестве аргумента `ref` на `NavigationContainer`:

```js
import * as React from 'react';
import {
    NavigationContainer,
    useNavigationContainerRef,
} from '@react-navigation/native';
import { useFlipper } from '@react-navigation/devtools';

export default function App() {
    const navigationRef = useNavigationContainerRef();

    useFlipper(navigationRef);

    return (
        <NavigationContainer ref={navigationRef}>
            {/* ... */}
        </NavigationContainer>
    );
}
```

Теперь вы сможете использовать инструменты React Navigation devtools в Flipper, когда ваше устройство подключено к Flipper.

![React Navigation Logs](flipper-plugin-logs.png)

![React Navigation Linking](flipper-plugin-linking.png)

### `useReduxDevToolsExtension`

Этот хук обеспечивает интеграцию с [Redux DevTools Extension](https://github.com/reduxjs/redux-devtools). Он также работает с [`React Native Debugger app`](https://github.com/jhen0409/react-native-debugger), которое включает это расширение.

**Использование:**

Чтобы использовать хук, импортируйте его и передайте в качестве аргумента `ref` на `NavigationContainer`:

```js
import * as React from 'react';
import {
    NavigationContainer,
    useNavigationContainerRef,
} from '@react-navigation/native';
import { useReduxDevToolsExtension } from '@react-navigation/devtools';

export default function App() {
    const navigationRef = useNavigationContainerRef();

    useReduxDevToolsExtension(navigationRef);

    return (
        <NavigationContainer ref={navigationRef}>
            {/* ... */}
        </NavigationContainer>
    );
}
```

Теперь вы сможете видеть логи из React Navigation в расширении Redux DevTools Extension, например, при отладке приложения с помощью приложения React Native Debugger.

## Ссылки

-   [Developer tools](https://reactnavigation.org/docs/devtools/)
