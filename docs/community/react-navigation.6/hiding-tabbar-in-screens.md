---
description: Иногда мы можем захотеть скрыть панель вкладок на определенных экранах в стековом навигаторе, вложенном в навигатор вкладок
---

# Скрытие панели вкладок на определенных экранах

Иногда мы можем захотеть скрыть панель вкладок на определенных экранах в стековом навигаторе, вложенном в навигатор вкладок. Допустим, у нас есть 5 экранов: `Home`, `Feed`, `Notifications`, `Profile` и `Settings`, а структура навигации выглядит следующим образом:

```js
function HomeStack() {
    return (
        <Stack.Navigator>
            <Stack.Screen name="Home" component={Home} />
            <Stack.Screen
                name="Profile"
                component={Profile}
            />
            <Stack.Screen
                name="Settings"
                component={Settings}
            />
        </Stack.Navigator>
    );
}

function App() {
    return (
        <Tab.Navigator>
            <Tab.Screen name="Home" component={HomeStack} />
            <Tab.Screen name="Feed" component={Feed} />
            <Tab.Screen
                name="Notifications"
                component={Notifications}
            />
        </Tab.Navigator>
    );
}
```

При такой структуре при переходе к экрану `Профиль` или `Настройки` панель вкладок будет оставаться видимой на этих экранах.

Но если мы хотим, чтобы панель вкладок отображалась только на экранах `Home`, `Feed` и `Notifications`, но не на экранах `Profile` и `Settings`, то нам необходимо изменить структуру навигации. Самый простой способ добиться этого - вложить навигатор вкладок в первый экран стека вместо вложения стека в навигатор вкладок:

```js
function HomeTabs() {
    return (
        <Tab.Navigator>
            <Tab.Screen name="Home" component={Home} />
            <Tab.Screen name="Feed" component={Feed} />
            <Tab.Screen
                name="Notifications"
                component={Notifications}
            />
        </Tab.Navigator>
    );
}

function App() {
    return (
        <NavigationContainer>
            <Stack.Navigator>
                <Stack.Screen
                    name="Home"
                    component={HomeTabs}
                />
                <Stack.Screen
                    name="Profile"
                    component={Profile}
                />
                <Stack.Screen
                    name="Settings"
                    component={Settings}
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
}
```

После реорганизации структуры навигации теперь при переходе к экранам `Профиль` или `Настройки` панель вкладок больше не будет видна над экраном.

## Ссылки

-   [Hiding tab bar in specific screens](https://reactnavigation.org/docs/hiding-tabbar-in-screens)
