---
description: Пожалуй, самым распространенным стилем навигации в мобильных приложениях является навигация на основе табов. Это могут быть табы в нижней части экрана или в верхней части под заголовком
---

# Навигация по табам

Пожалуй, самым распространенным стилем навигации в мобильных приложениях является навигация на основе табов. Это могут быть табы в нижней части экрана или в верхней части под заголовком (или даже вместо заголовка).

В данном руководстве рассматривается [`createBottomTabNavigator`](bottom-tab-navigator.md). Для добавления табов в приложение можно также использовать [`createMaterialBottomTabNavigator`](material-bottom-tab-navigator.md) и [`createMaterialTopTabNavigator`](material-top-tab-navigator.md).

Прежде чем продолжить, сначала установите [`@react-navigation/bottom-tabs`](https://github.com/react-navigation/react-navigation/tree/main/packages/bottom-tabs):

```bash
npm install @react-navigation/bottom-tabs
```

## Минимальный пример навигации на основе вкладок

```js
import * as React from 'react';
import { Text, View } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

function HomeScreen() {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Home!</Text>
        </View>
    );
}

function SettingsScreen() {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Settings!</Text>
        </View>
    );
}

const Tab = createBottomTabNavigator();

export default function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator>
                <Tab.Screen
                    name="Home"
                    component={HomeScreen}
                />
                <Tab.Screen
                    name="Settings"
                    component={SettingsScreen}
                />
            </Tab.Navigator>
        </NavigationContainer>
    );
}
```

## Настройка внешнего вида

Это похоже на настройку навигатора стека &mdash; есть некоторые свойства, которые устанавливаются при инициализации навигатора табов, и другие, которые могут быть настроены для каждого экрана в `options`.

```js
// You can import Ionicons from @expo/vector-icons/Ionicons if you use Expo or
// react-native-vector-icons/Ionicons otherwise.
import Ionicons from 'react-native-vector-icons/Ionicons';

// (...)

export default function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator
                screenOptions={({ route }) => ({
                    tabBarIcon: ({
                        focused,
                        color,
                        size,
                    }) => {
                        let iconName;

                        if (route.name === 'Home') {
                            iconName = focused
                                ? 'ios-information-circle'
                                : 'ios-information-circle-outline';
                        } else if (
                            route.name === 'Settings'
                        ) {
                            iconName = focused
                                ? 'ios-list'
                                : 'ios-list-outline';
                        }

                        // You can return any component that you like here!
                        return (
                            <Ionicons
                                name={iconName}
                                size={size}
                                color={color}
                            />
                        );
                    },
                    tabBarActiveTintColor: 'tomato',
                    tabBarInactiveTintColor: 'gray',
                })}
            >
                <Tab.Screen
                    name="Home"
                    component={HomeScreen}
                />
                <Tab.Screen
                    name="Settings"
                    component={SettingsScreen}
                />
            </Tab.Navigator>
        </NavigationContainer>
    );
}
```

Давайте разберемся в этом:

-   `tabBarIcon` является поддерживаемой опцией в навигаторе нижних табов. Поэтому мы знаем, что можем использовать его в компонентах экрана в свойстве `options`, но в данном случае решили поместить его в свойство `screenOptions` объекта `Tab.Navigator`, чтобы для удобства централизовать настройку значка.
-   `tabBarIcon` - это функция, которой передаются параметры `focused` state, `color` и `size`. Если заглянуть дальше в конфигурацию, то можно увидеть `tabBarActiveTintColor` и `tabBarInactiveTintColor`. По умолчанию они соответствуют настройкам платформы iOS, но здесь их можно изменить. `color`, передаваемый в `tabBarIcon`, является либо активным, либо неактивным, в зависимости от состояния `focused` (focused - активный). `Size` - это размер иконки, ожидаемый панелью вкладок.
-   Более подробную информацию о параметрах конфигурации `createBottomTabNavigator` можно найти в [полном справочнике API](bottom-tab-navigator.md).

## Добавление значков к иконкам

Иногда мы хотим добавить значки к некоторым иконкам. Для этого можно использовать опцию [`tabBarBadge`](bottom-tab-navigator.md#tabbarbadge):

```js
<Tab.Screen
    name="Home"
    component={HomeScreen}
    options={{ tabBarBadge: 3 }}
/>
```

С точки зрения пользовательского интерфейса этот компонент уже готов к использованию, однако необходимо найти способ корректно передавать количество значков из другого места, например, используя [React Context](https://reactjs.org/docs/context.html), [Redux](https://redux.js.org/), [MobX](https://mobx.js.org/) или [эмиттеры событий](https://github.com/facebook/react-native/blob/master/Libraries/vendor/emitter/EventEmitter.js).

![Вкладки со значками](tabs-badges.png)

## Переход между вкладками

Переход с одной вкладки на другую имеет знакомый API &mdash; `navigation.navigate`.

```js
function HomeScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Home!</Text>
            <Button
                title="Go to Settings"
                onPress={() =>
                    navigation.navigate('Settings')
                }
            />
        </View>
    );
}

function SettingsScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Settings!</Text>
            <Button
                title="Go to Home"
                onPress={() => navigation.navigate('Home')}
            />
        </View>
    );
}
```

## Навигатор стека для каждого таба

Обычно вкладки отображают не только один экран &mdash; например, в ленте Twitter можно нажать на твит и перейти на новый экран в этой вкладке со всеми ответами. Можно представить это как наличие отдельных навигационных стеков внутри каждого таба, и именно так мы будем моделировать это в React Navigation.

```js
import * as React from 'react';
import { Button, Text, View } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

function DetailsScreen() {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Details!</Text>
        </View>
    );
}

function HomeScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Home screen</Text>
            <Button
                title="Go to Details"
                onPress={() =>
                    navigation.navigate('Details')
                }
            />
        </View>
    );
}

function SettingsScreen({ navigation }) {
    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Text>Settings screen</Text>
            <Button
                title="Go to Details"
                onPress={() =>
                    navigation.navigate('Details')
                }
            />
        </View>
    );
}

const HomeStack = createNativeStackNavigator();

function HomeStackScreen() {
    return (
        <HomeStack.Navigator>
            <HomeStack.Screen
                name="Home"
                component={HomeScreen}
            />
            <HomeStack.Screen
                name="Details"
                component={DetailsScreen}
            />
        </HomeStack.Navigator>
    );
}

const SettingsStack = createNativeStackNavigator();

function SettingsStackScreen() {
    return (
        <SettingsStack.Navigator>
            <SettingsStack.Screen
                name="Settings"
                component={SettingsScreen}
            />
            <SettingsStack.Screen
                name="Details"
                component={DetailsScreen}
            />
        </SettingsStack.Navigator>
    );
}

const Tab = createBottomTabNavigator();

export default function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator
                screenOptions={{ headerShown: false }}
            >
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

## Почему нам нужен TabNavigator, а не TabBarIOS или другой компонент?

Часто встречается попытка использовать отдельный компонент панели вкладок, не интегрируя его в библиотеку навигации, используемую в приложении. В некоторых случаях это прекрасно работает! Однако следует иметь в виду, что в этом случае можно столкнуться с некоторыми досадными непредвиденными проблемами.

Например, навигатор вкладок React Navigation позаботится о работе с кнопкой "назад" в Android, в то время как отдельные компоненты обычно этого не делают. Кроме того, вам (как разработчику) будет сложнее выполнять такие действия, как "перейти на эту вкладку и затем перейти на этот экран", если для этого необходимо обращаться к двум различным API. Наконец, в мобильных пользовательских интерфейсах есть множество мелких деталей, которые требуют, чтобы определенные компоненты знали о расположении или присутствии других компонентов &mdash; например, если у вас есть полупрозрачная панель вкладок, содержимое должно прокручиваться под ней, а вид прокрутки должен иметь вставку внизу, равную высоте панели вкладок, чтобы вы могли видеть все содержимое. Двойное нажатие на панель вкладок должно привести к тому, что активный стек навигации переместится в верхнюю часть стека, а повторное нажатие - к прокрутке активного вида прокрутки в этом стеке до самого верха. Хотя в React Navigation еще не все эти поведения реализованы из коробки, они будут реализованы, и вы не получите ничего из этого, если будете использовать отдельный компонент представления вкладок.

## Навигатор вкладок содержит стек, и вы хотите скрыть панель вкладок на определенных экранах

[См. документацию здесь](hiding-tabbar-in-screens.md)

## Ссылки

-   [Tab navigation](https://reactnavigation.org/docs/tab-based-navigation/)
