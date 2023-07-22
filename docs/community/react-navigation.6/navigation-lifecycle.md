---
description: Жизненный цикл экранов при навигации, какие собыия при этом возникают
---

# Жизненный цикл навигации

В предыдущем разделе мы работали со стековым навигатором, имеющим два экрана (`Home` и `Details`), и научились использовать `navigation.navigate('RouteName')` для перехода между маршрутами.

В связи с этим возникает важный вопрос: что происходит с экраном `Home`, когда мы уходим от него или когда возвращаемся к нему? Как маршрут узнает, что пользователь покидает его или возвращается к нему?

Если вы знакомитесь с react-навигацией на веб-основе, то можете предположить, что при переходе пользователя с маршрута `A` на маршрут `B`, маршрут `A` размонтируется (вызывается его `componentWillUnmount`) и `A` снова смонтируется, когда пользователь вернется к нему. Хотя эти методы жизненного цикла React по-прежнему актуальны и используются в react-navigation, их применение отличается от веб-приложений. Это обусловлено более сложными потребностями мобильной навигации.

## Пример сценария

Рассмотрим стековый навигатор с экранами A и B. При переходе к A вызывается его `componentDidMount`. При переходе к экрану B также вызывается его `componentDidMount`, но A остается смонтированным в стеке, и его `componentWillUnmount`, соответственно, не вызывается.

При возврате из B в A вызывается `componentWillUnmount` для B, но `componentDidMount` для A не вызывается, поскольку A все это время оставался смонтированным.

Подобные результаты можно наблюдать (в сочетании) и с другими навигаторами. Рассмотрим навигатор табов с двумя вкладками, где каждая вкладка является навигатором стека:

```js
function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator>
                <Tab.Screen name="First">
                    {() => (
                        <SettingsStack.Navigator>
                            <SettingsStack.Screen
                                name="Settings"
                                component={SettingsScreen}
                            />
                            <SettingsStack.Screen
                                name="Profile"
                                component={ProfileScreen}
                            />
                        </SettingsStack.Navigator>
                    )}
                </Tab.Screen>
                <Tab.Screen name="Second">
                    {() => (
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
                    )}
                </Tab.Screen>
            </Tab.Navigator>
        </NavigationContainer>
    );
}
```

Начнем с экрана `Домашний экран` и перейдем к экрану `Детали`. Затем с помощью панели вкладок переходим на экран `SettingsScreen` и переходим на экран `ProfileScreen`. После выполнения этой последовательности операций все 4 экрана будут смонтированы! Если с помощью панели вкладок переключиться обратно на `HomeStack`, то можно заметить, что перед вами откроется `DetailsScreen` - состояние навигации по `HomeStack` сохранилось!

## События жизненного цикла React Navigation

Теперь, когда мы поняли, как работают методы жизненного цикла React в React Navigation, давайте ответим на вопрос, который мы задали в самом начале: "Как мы узнаем, что пользователь покидает (размытие) или возвращается к нему (фокус)?"

React Navigation передает события экранным компонентам, которые на них подписаны. Мы можем прослушивать события `focus` и `blur`, чтобы узнать, когда экран попадает в фокус или уходит из фокуса соответственно.

Пример:

```js
function Profile({ navigation }) {
    React.useEffect(() => {
        const unsubscribe = navigation.addListener(
            'focus',
            () => {
                // Screen was focused
                // Do something
            }
        );

        return unsubscribe;
    }, [navigation]);

    return <ProfileContent />;
}
```

Более подробно о доступных событиях и использовании API см. в разделе [Navigation events](navigation-events.md).

Вместо того чтобы добавлять слушателей событий вручную, мы можем использовать хук [`useFocusEffect`](use-focus-effect.md) для выполнения побочных эффектов. Он похож на хук `useEffect` в React, но связан с жизненным циклом навигации.

Пример:

```js
import { useFocusEffect } from '@react-navigation/native';

function Profile() {
    useFocusEffect(
        React.useCallback(() => {
            // Do something when the screen is focused

            return () => {
                // Do something when the screen is unfocused
                // Useful for cleanup functions
            };
        }, [])
    );

    return <ProfileContent />;
}
```

Если вы хотите отображать различные объекты в зависимости от того, сфокусирован экран или нет, вы можете использовать хук [`useIsFocused`](use-is-focused.md), который возвращает булево число, указывающее, сфокусирован ли экран.

## Резюме

-   Хотя методы жизненного цикла React по-прежнему актуальны, React Navigation добавляет больше событий, на которые можно подписаться с помощью свойства `navigation`.
-   Также можно использовать хуки `useFocusEffect` или `useIsFocused`.

## Ссылки

-   [Navigation lifecycle](https://reactnavigation.org/docs/navigation-lifecycle)
