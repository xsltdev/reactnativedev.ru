---
description: В этом документе мы объясним, как работают опции экрана при наличии нескольких навигаторов
---

# Параметры экрана с вложенными навигаторами

В этом документе мы объясним, как работают [опции экрана](screen-options.md) при наличии нескольких навигаторов. Важно понимать это, чтобы правильно разместить свои `options` и правильно сконфигурировать навигаторы. Если вы поместите их в неправильное место, то в лучшем случае ничего не произойдет, а в худшем - случится что-то непонятное и неожиданное.

**Вы можете изменять навигационные опции навигатора только из одного из его экранных компонентов. Это в равной степени относится и к вложенным экранам-навигаторам**.

Возьмем, к примеру, навигатор вкладок, который содержит собственный стек на каждой вкладке. Что произойдет, если мы установим `options` на экране внутри стека?

```js
const Tab = createTabNavigator();
const HomeStack = createNativeStackNavigator();
const SettingsStack = createNativeStackNavigator();

function HomeStackScreen() {
    return (
        <HomeStack.Navigator>
            <HomeStack.Screen
                name="A"
                component={A}
                options={{ tabBarLabel: 'Home!' }}
            />
        </HomeStack.Navigator>
    );
}

function SettingsStackScreen() {
    return (
        <SettingsStack.Navigator>
            <SettingsStack.Screen
                name="B"
                component={B}
                options={{ tabBarLabel: 'Settings!' }}
            />
        </SettingsStack.Navigator>
    );
}

export default function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator>
                <Tab.Screen
                    name="Home"
                    component={HomeStackScreen}
                />
                <Tab.Screen
                    name="Settings"
                    component={SettingsStackScreen}
                />
            </Tab.Navigator>
        </NavigationContainer>
    );
}
```

Как мы уже говорили, изменить параметры навигации для навигатора можно только из одного из его экранных компонентов. Приведенные выше `A` и `B` являются экранными компонентами в `HomeStack` и `SettingsStack` соответственно, а не в навигаторе вкладок. Поэтому в результате свойство `tabBarLabel` не будет применено к навигатору вкладок. Однако мы можем это исправить!

```js
export default function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator>
                <Tab.Screen
                    name="Home"
                    component={HomeStackScreen}
                    options={{ tabBarLabel: 'Home!' }}
                />
                <Tab.Screen
                    name="Settings"
                    component={SettingsStackScreen}
                    options={{ tabBarLabel: 'Settings!' }}
                />
            </Tab.Navigator>
        </NavigationContainer>
    );
}
```

Когда мы задаем `options` непосредственно для компонентов `Screen`, содержащих компоненты `HomeStack` и `SettingsStack`, это позволяет нам управлять опциями родительского навигатора, когда он используется в качестве компонента экрана. В данном случае опции наших компонентов стека определяют метку в навигаторе вкладок, на котором отображаются стеки.

## Установка опций родительского экрана на основе состояния дочернего навигатора

Представьте себе следующую конфигурацию:

```js
const Tab = createBottomTabNavigator();

function HomeTabs() {
    return (
        <Tab.Navigator>
            <Tab.Screen
                name="Feed"
                component={FeedScreen}
            />
            <Tab.Screen
                name="Profile"
                component={ProfileScreen}
            />
            <Tab.Screen
                name="Account"
                component={AccountScreen}
            />
        </Tab.Navigator>
    );
}

const Stack = createNativeStackNavigator();

export default function App() {
    return (
        <NavigationContainer>
            <Stack.Navigator>
                <Stack.Screen
                    name="Home"
                    component={HomeTabs}
                />
                <Stack.Screen
                    name="Settings"
                    component={SettingsScreen}
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
}
```

Если бы мы задали `headerTitle` с `options` для `FeedScreen`, то это бы не сработало. Это связано с тем, что стек `App` при конфигурировании будет обращаться только к своим ближайшим дочерним элементам: `HomeTabs` и `SettingsScreen`.

Но мы можем определить опцию `headerTitle` на основе [состояния навигации](navigation-state.md) нашего навигатора вкладок с помощью помощника `getFocusedRouteNameFromRoute`. Сначала создадим функцию для получения заголовка:

```js
import { getFocusedRouteNameFromRoute } from '@react-navigation/native';

function getHeaderTitle(route) {
    // If the focused route is not found, we need to assume it's the initial screen
    // This can happen during if there hasn't been any navigation inside the screen
    // In our case, it's "Feed" as that's the first screen inside the navigator
    const routeName =
        getFocusedRouteNameFromRoute(route) ?? 'Feed';

    switch (routeName) {
        case 'Feed':
            return 'News feed';
        case 'Profile':
            return 'My profile';
        case 'Account':
            return 'My account';
    }
}
```

Затем мы можем использовать эту функцию с параметром `options` на `Screen`:

```js
<Stack.Screen
    name="Home"
    component={HomeTabs}
    options={({ route }) => ({
        headerTitle: getHeaderTitle(route),
    })}
/>
```

Что же здесь происходит? С помощью хелпера `getFocusedRouteNameFromRoute` мы можем получить название активного маршрута из дочернего навигатора (в данном случае это навигатор табов, так как именно его мы отображаем) и задать соответствующее название для заголовка.

Этот подход можно использовать в любом случае, когда необходимо задать параметры родительского навигатора, основываясь на состоянии дочернего навигатора. Часто встречаются следующие случаи:

1.  Показать заголовок вкладки в заголовке стека: стек содержит навигатор табов, и вы хотите установить заголовок в заголовке стека (пример выше).
2.  Показывать экраны без панели вкладок: навигатор вкладок содержит стек, и вы хотите скрыть панель вкладок на определенных экранах (не рекомендуется, вместо этого смотрите [Скрытие панели вкладок на определенных экранах](hiding-tabbar-in-screens.md))
3.  Блокировка drawer на определенных экранах: в drawer находится стек, и вы хотите заблокировать drawer на определенных экранах.

Во многих случаях аналогичного поведения можно добиться, реорганизовав навигаторы. Обычно мы рекомендуем использовать этот вариант, если он подходит для конкретного случая.

Например, в приведенном выше случае вместо того, чтобы добавлять навигатор вкладок внутри навигатора стопки, можно добавить навигатор стопки внутри каждой из вкладок.

```js
const FeedStack = createNativeStackNavigator();

function FeedStackScreen() {
    return (
        <FeedStack.Navigator>
            <FeedStack.Screen
                name="Feed"
                component={FeedScreen}
            />
            {/* other screens */}
        </FeedStack.Navigator>
    );
}

const ProfileStack = createNativeStackNavigator();

function ProfileStackScreen() {
    return (
        <ProfileStack.Navigator>
            <ProfileStack.Screen
                name="Profile"
                component={ProfileScreen}
            />
            {/* other screens */}
        </ProfileStack.Navigator>
    );
}

const Tab = createBottomTabNavigator();

function HomeTabs() {
    return (
        <Tab.Navigator>
            <Tab.Screen
                name="Feed"
                component={FeedStackScreen}
            />
            <Tab.Screen
                name="Profile"
                component={ProfileStackScreen}
            />
        </Tab.Navigator>
    );
}

const RootStack = createNativeStackNavigator();

export default function App() {
    return (
        <NavigationContainer>
            <RootStack.Navigator>
                <RootStack.Screen
                    name="Home"
                    component={HomeTabs}
                />
                <RootStack.Screen
                    name="Settings"
                    component={SettingsScreen}
                />
            </RootStack.Navigator>
        </NavigationContainer>
    );
}
```

Кроме того, это позволяет добавлять новые экраны в стеки лент и профилей, не скрывая панель вкладок, путем добавления дополнительных маршрутов в эти стеки.

Если вы хотите разместить экраны поверх панели табов (т.е. не показывающие панель табов), то их можно добавить в стек `App` вместо того, чтобы добавлять их в экраны внутри навигатора по табам.

## Ссылки

-   [Screen options with nested navigators](https://reactnavigation.org/docs/screen-options-resolution)
