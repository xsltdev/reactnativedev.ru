---
description: Иногда мы хотим иметь несколько drawer на одном экране - один слева, другой справа
---

# Несколько drawers

Иногда мы хотим иметь несколько drawer на одном экране: один слева, другой справа. Этого можно добиться с помощью [вложения](nesting-navigators.md) 2 [навигаторов drawer](drawer-navigator.md).

## Вложение 2 навигаторов drawer

Здесь мы имеем два drawer, вложенных друг в друга, один из которых расположен слева, а другой - справа:

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
                onPress={() => navigation.openDrawer()}
                title="Open drawer"
            />
        </View>
    );
}

const LeftDrawer = createDrawerNavigator();

const LeftDrawerScreen = () => {
    return (
        <LeftDrawer.Navigator
            screenOptions={{ drawerPosition: 'left' }}
        >
            <LeftDrawer.Screen
                name="Home"
                component={HomeScreen}
            />
        </LeftDrawer.Navigator>
    );
};

const RightDrawer = createDrawerNavigator();

const RightDrawerScreen = () => {
    return (
        <RightDrawer.Navigator
            screenOptions={{
                drawerPosition: 'right',
                headerShown: false,
            }}
        >
            <RightDrawer.Screen
                name="HomeDrawer"
                component={LeftDrawerScreen}
            />
        </RightDrawer.Navigator>
    );
};

export default function App() {
    return (
        <NavigationContainer>
            <RightDrawerScreen />
        </NavigationContainer>
    );
}
```

Но есть одна проблема. Когда мы вызываем `navigation.openDrawer()` в нашем `HomeScreen`, то всегда открывается левый ящик, поскольку он является непосредственным родителем экрана.

Чтобы решить эту проблему, нам необходимо использовать [`navigation.getParent`](navigation-prop.md#getparent) для обращения к правому ящику, который является родителем левого ящика. Таким образом, наш код будет выглядеть следующим образом:

```js
<Button
	onPress={() => navigation.openDrawer()}
	title="Open left drawer" />
<Button
	onPress={() => navigation.getParent().openDrawer()}
	title="Open right drawer" />
```

Однако это означает, что наша кнопка должна знать о родительских навигаторах, что не является идеальным вариантом. Если наша кнопка будет вложена в другие навигаторы, то потребуется несколько вызовов `getParent()`. Чтобы решить эту проблему, мы можем использовать пропс [`id`](drawer-navigator.md#id) для идентификации родительского навигатора.

Для настройки содержимого ящика мы можем использовать пропс [`drawerContent`](drawer-navigator.md#drawercontent) для передачи функции, которая отображает пользовательский компонент.

Итоговый код будет выглядеть следующим образом:

```js
import * as React from 'react';
import { Button, Text, View } from 'react-native';
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
                    navigation
                        .getParent('LeftDrawer')
                        .openDrawer()
                }
                title="Open left drawer"
            />
            <Button
                onPress={() =>
                    navigation
                        .getParent('RightDrawer')
                        .openDrawer()
                }
                title="Open right drawer"
            />
        </View>
    );
}

function RightDrawerContent() {
    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Text>This is the right drawer</Text>
        </View>
    );
}

const LeftDrawer = createDrawerNavigator();

function LeftDrawerScreen() {
    return (
        <LeftDrawer.Navigator
            id="LeftDrawer"
            screenOptions={{ drawerPosition: 'left' }}
        >
            <LeftDrawer.Screen
                name="Home"
                component={HomeScreen}
            />
        </LeftDrawer.Navigator>
    );
}

const RightDrawer = createDrawerNavigator();

function RightDrawerScreen() {
    return (
        <RightDrawer.Navigator
            id="RightDrawer"
            drawerContent={(props) => (
                <RightDrawerContent {...props} />
            )}
            screenOptions={{
                drawerPosition: 'right',
                headerShown: false,
            }}
        >
            <RightDrawer.Screen
                name="HomeDrawer"
                component={LeftDrawerScreen}
            />
        </RightDrawer.Navigator>
    );
}

export default function App() {
    return (
        <NavigationContainer>
            <RightDrawerScreen />
        </NavigationContainer>
    );
}
```

Здесь мы передаем строки `"LeftDrawer"` и `"RightDrawer"` (здесь можно использовать любую строку) в свойстве `id` навигаторов ящиков. Затем мы используем `navigation.getParent('LeftDrawer').openDrawer()` для открытия левого ящика и `navigation.getParent('RightDrawer').openDrawer()` для открытия правого ящика.

## Резюме

-   Чтобы иметь на экране 2 ящика, можно использовать опцию [`drawerPosition`](drawer-navigator.md#drawerposition) для их расположения слева и справа.
-   Чтобы открыть нужный ящик, можно использовать [`navigation.getParent`](navigation-prop.md#getparent) в сочетании с параметром [`id`](drawer-navigator.md#id).

## Ссылки

-   [Multiple drawers](https://reactnavigation.org/docs/multiple-drawers)
