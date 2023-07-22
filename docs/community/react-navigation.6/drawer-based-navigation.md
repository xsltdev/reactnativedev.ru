---
description: Общим для навигации является использование drawer с левой (иногда с правой) стороны для перемещения между экранами
---

# Drawer навигация

Общим для навигации является использование drawer с левой (иногда с правой) стороны для перемещения между экранами.

![Drawer navigation](drawer.apng)

Прежде чем продолжить, установите и настройте [`@react-navigation/drawer`](https://github.com/react-navigation/react-navigation/tree/main/packages/drawer) и его зависимости, следуя [инструкции по установке](drawer-navigator.md#installation).

## Минимальный пример навигации на основе drawer

Чтобы использовать этот навигатор drawer, импортируйте его из `@react-navigation/drawer` (проведите пальцем вправо, чтобы открыть):

```js
import * as React from 'react';
import { Button, View } from 'react-native';
import { createDrawerNavigator } from '@react-navigation/drawer';
import { NavigationContainer } from '@react-navigation/native';

function HomeScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Button
                onPress={() =>
                    navigation.navigate('Notifications')
                }
                title="Go to notifications"
            />
        </View>
    );
}

function NotificationsScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Button
                onPress={() => navigation.goBack()}
                title="Go back home"
            />
        </View>
    );
}

const Drawer = createDrawerNavigator();

export default function App() {
    return (
        <NavigationContainer>
            <Drawer.Navigator initialRouteName="Home">
                <Drawer.Screen
                    name="Home"
                    component={HomeScreen}
                />
                <Drawer.Screen
                    name="Notifications"
                    component={NotificationsScreen}
                />
            </Drawer.Navigator>
        </NavigationContainer>
    );
}
```

## Открытие и закрытие drawer

Чтобы открыть и закрыть drawer, воспользуйтесь следующими вспомогательными функциями:

```js
navigation.openDrawer();
navigation.closeDrawer();
```

Если вы хотите переключить drawer, то сделайте следующее:

```js
navigation.toggleDrawer();
```

Каждая из этих функций за кулисами просто диспетчеризирует действия:

```js
navigation.dispatch(DrawerActions.openDrawer());
navigation.dispatch(DrawerActions.closeDrawer());
navigation.dispatch(DrawerActions.toggleDrawer());
```

Если Вы хотите определить, открыт или закрыт drawer, Вы можете сделать следующее:

```js
import { useDrawerStatus } from '@react-navigation/drawer';

// ...

const isDrawerOpen = useDrawerStatus() === 'open';
```

## Ссылки

-   [Drawer navigation](https://reactnavigation.org/docs/drawer-based-navigation)
