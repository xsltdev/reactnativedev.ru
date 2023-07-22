---
description: Простая панель вкладок в нижней части экрана позволяет переключаться между различными маршрутами
---

# Навигатор нижних вкладок

Простая панель вкладок в нижней части экрана позволяет переключаться между различными маршрутами. Маршруты инициализируются лениво - их экранные компоненты не устанавливаются до тех пор, пока они не будут впервые сфокусированы.

<video playsInline autoPlay muted loop>
<source src="/community/react-navigation.6/bottom-tabs-demo.mov" />
</video>

## Установка

Чтобы использовать этот навигатор, убедитесь, что у вас есть [`@react-navigation/native` и его зависимости (следуйте этому руководству)](getting-started.md), затем установите [`@react-navigation/bottom-tabs`](https://github.com/react-navigation/react-navigation/tree/main/packages/bottom-tabs):

```bash
npm install @react-navigation/bottom-tabs
```

## Определение API

Чтобы использовать этот навигатор вкладок, импортируйте его из `@react-navigation/bottom-tabs`:

```js
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Tab = createBottomTabNavigator();

function MyTabs() {
    return (
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
    );
}
```

!!!note ""

    Полное руководство по использованию приведено на сайте [Tab Navigation](tab-based-navigation.md)

### Пропсы {#props}

Компонент `Tab.Navigator` принимает следующие реквизиты:

#### `id`

Необязательный уникальный идентификатор навигатора. Он может быть использован с помощью [`navigation.getParent`](navigation-prop.md#getparent) для ссылки на этот навигатор в дочернем навигаторе.

#### `initialRouteName`

Имя маршрута, которое должно отображаться при первой загрузке навигатора.

#### `screenOptions`

Параметры по умолчанию, используемые для экранов в навигаторе.

#### `backBehavior`

Этот параметр управляет тем, что происходит при вызове `goBack` в навигаторе. Это включает в себя нажатие кнопки "назад" на устройстве или жест "назад" на Android.

Поддерживаются следующие значения:

-   `firstRoute` - возврат на первый экран, заданный в навигаторе (по умолчанию)
-   `initialRoute` - возврат к начальному экрану, переданному в параметре `initialRouteName`, если значение не передано, то по умолчанию возвращается к первому экрану
-   `order` - возврат к экрану, определенному перед сфокусированным экраном
-   `history` - возврат к последнему посещенному экрану в навигаторе; если один и тот же экран посещается несколько раз, то старые записи удаляются из истории
-   `none` - не обрабатывать кнопку "Назад

#### `detachInactiveScreens`

Булево значение, используемое для указания того, следует ли отделять неактивные экраны от иерархии представлений для экономии памяти. Это обеспечивает интеграцию с [react-native-screens](https://github.com/software-mansion/react-native-screens). По умолчанию имеет значение `true`.

#### `sceneContainerStyle`

Объект стиля для компонента, оборачивающего содержимое экрана.

#### `tabBar`

Функция, возвращающая элемент React для отображения в качестве панели вкладок.

Пример:

```js
import { View, Text, TouchableOpacity } from 'react-native';

function MyTabBar({ state, descriptors, navigation }) {
    return (
        <View style={{ flexDirection: 'row' }}>
            {state.routes.map((route, index) => {
                const { options } = descriptors[route.key];
                const label =
                    options.tabBarLabel !== undefined
                        ? options.tabBarLabel
                        : options.title !== undefined
                        ? options.title
                        : route.name;

                const isFocused = state.index === index;

                const onPress = () => {
                    const event = navigation.emit({
                        type: 'tabPress',
                        target: route.key,
                        canPreventDefault: true,
                    });

                    if (
                        !isFocused &&
                        !event.defaultPrevented
                    ) {
                        // The `merge: true` option makes sure
                        // that the params inside the tab screen
                        // are preserved
                        navigation.navigate({
                            name: route.name,
                            merge: true,
                        });
                    }
                };

                const onLongPress = () => {
                    navigation.emit({
                        type: 'tabLongPress',
                        target: route.key,
                    });
                };

                return (
                    <TouchableOpacity
                        accessibilityRole="button"
                        accessibilityState={
                            isFocused
                                ? { selected: true }
                                : {}
                        }
                        accessibilityLabel={
                            options.tabBarAccessibilityLabel
                        }
                        testID={options.tabBarTestID}
                        onPress={onPress}
                        onLongPress={onLongPress}
                        style={{ flex: 1 }}
                    >
                        <Text
                            style={{
                                color: isFocused
                                    ? '#673ab7'
                                    : '#222',
                            }}
                        >
                            {label}
                        </Text>
                    </TouchableOpacity>
                );
            })}
        </View>
    );
}

// ...

<Tab.Navigator tabBar={(props) => <MyTabBar {...props} />}>
    {/* ... */}
</Tab.Navigator>;
```

В этом примере будет отображена базовая панель вкладок с ярлыками.

Обратите внимание, что вы **не можете** использовать хук `useNavigation` внутри `tabBar`, поскольку `useNavigation` доступен только внутри экранов. Вы получаете свойство `navigation` для вашего `tabBar`, которое можно использовать вместо него:

```js
function MyTabBar({ navigation }) {
    return (
        <Button
            title="Go somewhere"
            onPress={() => {
                // Navigate using the `navigation` prop that you received
                navigation.navigate('SomeScreen');
            }}
        />
    );
}
```

### Опции {#options}

Для настройки экранов в навигаторе можно использовать следующие [options](screen-options.md). Они могут быть указаны в свойствах `screenOptions` свойства `Tab.navigator` или `options` свойства `Tab.Screen`.

#### `title`

Общий заголовок, который может использоваться в качестве запасного варианта для `headerTitle` и `tabBarLabel`.

#### `tabBarLabel`

Строка заголовка вкладки, отображаемой в панели вкладок, или функция, которая по команде `{ focused: boolean, color: string }` возвращает узел React.Node, отображаемый в панели вкладок. Если значение не определено, используется сцена `title`. Чтобы скрыть, смотрите `tabBarShowLabel`.

#### `tabBarShowLabel`

Должна ли быть видна метка вкладки. По умолчанию имеет значение `true`.

#### `tabBarLabelPosition`

Отображается ли ярлык под иконкой или рядом с ней.

-   `below-icon`: ярлык отображается под иконкой (характерно для iPhone)
-   `beside-icon`: метка отображается рядом с иконкой (характерно для iPad).

По умолчанию позиция выбирается автоматически в зависимости от ширины устройства.

#### `tabBarLabelStyle`

Объект стиля для метки вкладки.

#### `tabBarIcon`

Функция, которая при задании `{ focused: boolean, color: string, size: number }` возвращает узел React.Node для отображения в панели вкладок.

#### `tabBarIconStyle`

Объект стиля для значка вкладки.

#### `tabBarBadge`

Текст для отображения в виде значка на иконке вкладки. Принимает `string` или `number`.

#### `tabBarBadgeStyle`

Стиль для значка на иконке вкладки. Здесь можно задать цвет фона или цвет текста.

#### `tabBarAccessibilityLabel`

Метка доступности для кнопки вкладки. Она считывается программой чтения с экрана, когда пользователь нажимает кнопку вкладки. Рекомендуется установить это значение, если у вас нет метки для вкладки.

#### `tabBarTestID`

ID для размещения этой кнопки вкладки в тестах.

#### `tabBarButton`

Функция, возвращающая элемент React для рендеринга в качестве кнопки панели вкладок. Он оборачивает иконку и ярлык. По умолчанию рендерится `Pressable`.

Здесь можно указать пользовательскую реализацию:

```js
tabBarButton: (props) => <TouchableOpacity {...props} />;
```

#### `tabBarActiveTintColor`

Цвет для пиктограммы и ярлыка на активной вкладке.

#### `tabBarInactiveTintColor`

Цвет для значка и метки на неактивных вкладках.

#### `tabBarActiveBackgroundColor`

Цвет фона для активной вкладки.

#### `tabBarInactiveBackgroundColor`

Цвет фона для неактивных вкладок.

#### `tabBarHideOnKeyboard`

Скрывать ли панель вкладок при открытии клавиатуры. По умолчанию имеет значение `false`.

#### `tabBarItemStyle`

Объект стиля для контейнера элемента вкладки.

#### `tabBarStyle`

Объект стиля для панели вкладок. Здесь можно настроить такие стили, как цвет фона.

Чтобы экран отображался под панелью вкладок, можно установить для стиля `position` значение `absolute`:

```js
<Tab.Navigator
  screenOptions={{
    tabBarStyle: { position: 'absolute' },
  }}
>
```

Также может потребоваться добавить нижнее поле к содержимому, если у вас есть абсолютно позиционированная панель вкладок. React Navigation не сделает этого автоматически.

Чтобы получить высоту нижней панели вкладок, можно использовать `BottomTabBarHeightContext` с [React's Context API](https://reactjs.org/docs/context.html#contextconsumer) или `useBottomTabBarHeight`:

```js
import { BottomTabBarHeightContext } from '@react-navigation/bottom-tabs';

// ...

<BottomTabBarHeightContext.Consumer>
  {tabBarHeight => (
    /* render something */
  )}
</BottomTabBarHeightContext.Consumer>
```

или

```js
import { useBottomTabBarHeight } from '@react-navigation/bottom-tabs';

// ...

const tabBarHeight = useBottomTabBarHeight();
```

#### `tabBarBackground`

Функция, возвращающая React-элемент для использования в качестве фона для панели вкладок. Можно использовать изображение, градиент, размытие и т. д:

```js
import { BlurView } from 'expo-blur';

// ...

<Tab.Navigator
  screenOptions={{
    tabBarStyle: { position: 'absolute' },
    tabBarBackground: () => (
      <BlurView tint="light" intensity={100} style={StyleSheet.absoluteFill} />
    ),
  }}
>
```

При использовании `BlurView` не забудьте также установить `position: 'absolute'` в `tabBarStyle`. Также необходимо использовать `useBottomTabBarHeight()`, чтобы добавить нижнюю подложку к содержимому.

#### `lazy`

Должен ли этот экран отображаться при первом обращении к нему. По умолчанию имеет значение `true`. Установите значение `false`, если вы хотите, чтобы экран отображался при первом обращении.

#### `unmountOnBlur`

Следует ли размонтировать этот экран при навигации от него. При размонтировании экрана сбрасываются все локальные состояния экрана, а также состояния вложенных в него навигаторов. По умолчанию имеет значение `false`.

Обычно мы не рекомендуем включать этот параметр, поскольку пользователи не ожидают, что их история навигации будет потеряна при переключении вкладок. Если вы включаете этот параметр, пожалуйста, подумайте, действительно ли это обеспечит лучший опыт для пользователя.

#### `freezeOnBlur`

Булево значение, указывающее, следует ли запретить повторное отображение неактивных экранов. По умолчанию имеет значение `false`.

По умолчанию принимает значение `true`, если `enableFreeze()` из пакета `react-native-screens` запущен в верхней части приложения.

Требуется версия `react-native-screens` >=3.16.0.

Поддерживается только на iOS и Android.

### Опции, связанные с заголовками

Список опций, связанных с заголовками, можно найти [здесь](elements.md#header). Эти [опции](screen-options.md) могут быть указаны в свойствах `screenOptions` свойства `Tab.navigator` или `options` свойства `Tab.Screen`. Для использования этих опций не обязательно использовать непосредственно `@react-navigation/elements`, они просто документированы на этой странице.

Кроме того, в нижних вкладках поддерживаются следующие опции:

#### `header`

Пользовательский заголовок, используемый вместо заголовка по умолчанию.

Функция принимает функцию, возвращающую React-элемент для отображения в качестве заголовка. В качестве аргумента функция получает объект, содержащий следующие свойства:

-   `navigation` - Объект навигации для текущего экрана.
-   `route` - Объект маршрута для текущего экрана.
-   `options` - Опции для текущего экрана.
-   `layout` - Размеры экрана, содержит свойства `height` и `width`.

Пример:

```js
import { getHeaderTitle } from '@react-navigation/elements';

// ..

header: ({ navigation, route, options }) => {
    const title = getHeaderTitle(options, route.name);

    return (
        <MyHeader
            title={title}
            style={options.headerStyle}
        />
    );
};
```

Чтобы задать пользовательский заголовок для всех экранов навигатора, можно указать эту опцию в свойстве `screenOptions` навигатора.

##### Укажите `height` в `headerStyle`

Если высота пользовательского заголовка отличается от высоты заголовка по умолчанию, то могут наблюдаться глюки, связанные с несинхронностью измерений. Явное указание высоты позволит избежать таких глюков.

Пример:

```js
headerStyle: {
  height: 80, // Specify the height of your custom header
};
```

Обратите внимание, что по умолчанию этот стиль не применяется к заголовку, поскольку вы сами управляете стилем вашего пользовательского заголовка. Если вы также хотите применить этот стиль к заголовку, используйте `options.headerStyle` из реквизита.

#### `headerShown`

Показывать или скрывать заголовок экрана. По умолчанию заголовок отображается. Установка этого значения в `false` скрывает заголовок.

### События {#events}

Навигатор может [испускать события](navigation-events.md) при определенных действиях. Поддерживаются следующие события:

#### `tabPress`

Это событие возникает, когда пользователь нажимает кнопку вкладки для текущего экрана на панели вкладок. По умолчанию нажатие кнопки вкладки выполняет несколько действий:

-   Если вкладка не сфокусирована, то нажатие кнопки вкладки фокусирует эту вкладку.
-   Если вкладка уже сфокусирована:
    -   Если экран для вкладки отображается в виде прокрутки, то можно использовать [`useScrollToTop`](use-scroll-to-top.md) для прокрутки в верхнее положение.
    -   Если на экране вкладки отображается стековый навигатор, то для стека выполняется действие `popToTop`.

Чтобы предотвратить поведение по умолчанию, можно вызвать `event.preventDefault`:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'tabPress',
        (e) => {
            // Prevent default behavior
            e.preventDefault();

            // Do something manually
            // ...
        }
    );

    return unsubscribe;
}, [navigation]);
```

Если у вас есть пользовательская панель вкладок, обязательно вызовите это событие.

#### `tabLongPress`

Это событие возникает, когда пользователь в течение длительного времени нажимает кнопку вкладки для текущего экрана на панели вкладок. Если у вас есть пользовательская панель вкладок, обязательно вызовите это событие.

Пример:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'tabLongPress',
        (e) => {
            // Do something
        }
    );

    return unsubscribe;
}, [navigation]);
```

### Хелперы {#helpers}

Навигатор вкладок добавляет в реквизит навигации следующие методы:

#### `jumpTo`

Осуществляет переход к существующему экрану в навигаторе вкладок. Метод принимает следующие аргументы:

-   `name` - _string_ - Имя маршрута, на который необходимо перейти.
-   `params` - _object_ - Параметры экрана, используемые для маршрута назначения.

```js
navigation.jumpTo('Profile', { owner: 'Michaś' });
```

## Пример {#example}

```js
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import MaterialCommunityIcons from 'react-native-vector-icons/MaterialCommunityIcons';

const Tab = createBottomTabNavigator();

function MyTabs() {
    return (
        <Tab.Navigator
            initialRouteName="Feed"
            screenOptions={{
                tabBarActiveTintColor: '#e91e63',
            }}
        >
            <Tab.Screen
                name="Feed"
                component={Feed}
                options={{
                    tabBarLabel: 'Home',
                    tabBarIcon: ({ color, size }) => (
                        <MaterialCommunityIcons
                            name="home"
                            color={color}
                            size={size}
                        />
                    ),
                }}
            />
            <Tab.Screen
                name="Notifications"
                component={Notifications}
                options={{
                    tabBarLabel: 'Updates',
                    tabBarIcon: ({ color, size }) => (
                        <MaterialCommunityIcons
                            name="bell"
                            color={color}
                            size={size}
                        />
                    ),
                    tabBarBadge: 3,
                }}
            />
            <Tab.Screen
                name="Profile"
                component={Profile}
                options={{
                    tabBarLabel: 'Profile',
                    tabBarIcon: ({ color, size }) => (
                        <MaterialCommunityIcons
                            name="account"
                            color={color}
                            size={size}
                        />
                    ),
                }}
            />
        </Tab.Navigator>
    );
}
```

## Ссылки

-   [Drawer Navigator](https://reactnavigation.org/docs/drawer-navigator/)
