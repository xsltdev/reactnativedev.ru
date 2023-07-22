---
description: Панель вкладок в верхней части экрана, оформленная в стиле Material Design, позволяет переключаться между различными маршрутами, касаясь вкладок или проводя пальцем по горизонтали
---

# Навигатор верхних вкладок материала

Панель вкладок в верхней части экрана, оформленная в стиле Material Design, позволяет переключаться между различными маршрутами, касаясь вкладок или проводя пальцем по горизонтали. Переходы по умолчанию анимированы. Компоненты экрана для каждого маршрута монтируются сразу.

<video playsInline autoPlay muted loop>
<source src="/community/react-navigation.6/material-top-tabs.mov" />
</video>

Это обертывание [`react-native-tab-view`](tab-view.md). Если вы хотите использовать представление вкладок без интеграции с React Navigation, используйте библиотеку напрямую.

## Установка

Чтобы использовать этот навигатор, убедитесь, что у вас есть [`@react-navigation/native` и его зависимости (следуйте этому руководству)](getting-started.md), затем установите [`@react-navigation/material-top-tabs`](https://github.com/react-navigation/react-navigation/tree/main/packages/material-top-tabs):

```bash
npm install @react-navigation/material-top-tabs react-native-tab-view
```

Затем необходимо установить [`react-native-pager-view`](https://github.com/callstack/react-native-pager-view), который требуется навигатору.

Если у вас есть Expo-управляемый проект, то в каталоге проекта выполните команду:

```bash
npx expo install react-native-pager-view
```

Если у вас есть "голый" проект React Native, в каталоге проекта выполните команду:

```bash
npm install react-native-pager-view
```

Если вы работаете на Mac и разрабатываете под iOS, то для завершения связывания необходимо также установить капсулы (через [Cocoapods](https://cocoapods.org/)).

```bash
npx pod-install ios
```

## Определение API

Чтобы использовать этот навигатор вкладок, импортируйте его из `@react-navigation/material-top-tabs`:

```js
import { createMaterialTopTabNavigator } from '@react-navigation/material-top-tabs';

const Tab = createMaterialTopTabNavigator();

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

Компонент `Tab.Navigator` принимает следующие параметры:

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

#### `tabBarPosition`

Положение панели вкладок в представлении вкладок. Возможные значения: `top` и `bottom`. По умолчанию принимается значение `top`.

#### `keyboardDismissMode`

Строка, указывающая, будет ли клавиатура отключаться в ответ на жест перетаскивания. Возможные значения:

-   `'auto'` (по умолчанию): клавиатура отключается при изменении индекса.
-   `'on-drag'`: клавиатура отключается, когда начинается перетаскивание.
-   `'none'`: перетаскивание не приводит к отключению клавиатуры.

#### `initialLayout`

Объект, содержащий начальную высоту и ширину экранов. Передача этого параметра позволит повысить производительность начального рендеринга. Для большинства приложений это хорошее значение по умолчанию:

```js
{
    width: Dimensions.get('window').width;
}
```

#### `sceneContainerStyle`

Стиль, применяемый к представлению, оборачивающему каждый экран. Его можно передать, чтобы отменить некоторые стили по умолчанию, например, обрезание переполнения.

#### `style`

Стиль, применяемый к контейнеру представления вкладки.

#### `tabBar`

Функция, возвращающая элемент React для отображения в качестве панели вкладок.

Пример:

```js
import {
    Animated,
    View,
    TouchableOpacity,
} from 'react-native';

function MyTabBar({
    state,
    descriptors,
    navigation,
    position,
}) {
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

                const inputRange = state.routes.map(
                    (_, i) => i
                );
                const opacity = position.interpolate({
                    inputRange,
                    outputRange: inputRange.map((i) =>
                        i === index ? 1 : 0
                    ),
                });

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
                        <Animated.Text style={{ opacity }}>
                            {label}
                        </Animated.Text>
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

Для настройки экранов в навигаторе можно использовать следующие [options](screen-options.md):

Пример:

```js
<Tab.Navigator
    screenOptions={{
        tabBarLabelStyle: { fontSize: 12 },
        tabBarItemStyle: { width: 100 },
        tabBarStyle: { backgroundColor: 'powderblue' },
    }}
>
    {/* ... */}
</Tab.Navigator>
```

#### `title`

Общий заголовок, который может использоваться в качестве запасного варианта для `headerTitle` и `tabBarLabel`.

#### `tabBarLabel`

Строка заголовка вкладки, отображаемой в панели вкладок, или функция, которая по команде `{ focused: boolean, color: string }` возвращает узел React.Node, отображаемый в панели вкладок. Если значение не определено, используется сцена `title`. Чтобы скрыть, смотрите опцию [`tabBarShowLabel`](#tabbarshowlabel).

#### `tabBarAccessibilityLabel`

Метка доступности для кнопки вкладки. Она считывается программой чтения с экрана, когда пользователь нажимает кнопку вкладки. Рекомендуется установить это значение, если у вас нет метки для вкладки.

#### `tabBarAllowFontScaling`

Должен ли шрифт этикетки масштабироваться с учетом настроек доступности Text Size.

#### `tabBarShowLabel`

Должна ли быть видна метка вкладки. По умолчанию имеет значение `true`.

#### `tabBarIcon`

Функция, которая при задании `{ focused: boolean, color: string }` возвращает узел React.Node для отображения в панели вкладок.

#### `tabBarShowIcon`

Должен ли быть виден значок вкладки. По умолчанию имеет значение `false`.

#### `tabBarBadge`

Функция, возвращающая элемент React для использования в качестве значка вкладки.

#### `tabBarIndicator`

Функция, возвращающая элемент React в качестве индикатора панели вкладок.

#### `tabBarIndicatorStyle`

Объект стиля для индикатора панели вкладок.

#### `tabBarIndicatorContainerStyle`

Объект стиля для представления, содержащего индикатор панели вкладок.

#### `tabBarTestID`

ID для размещения этой кнопки вкладки в тестах.

#### `tabBarActiveTintColor`

Цвет для пиктограммы и ярлыка на активной вкладке.

#### `tabBarInactiveTintColor`

Цвет для значка и метки на неактивных вкладках.

#### `tabBarGap`

Расстояние между элементами табуляции на панели табуляции.

Пример:

```js
<Tab.Navigator
    //...
    screenOptions={{
        tabBarGap: 10,
    }}
></Tab.Navigator>
```

#### `tabBarAndroidRipple`

Позволяет настроить эффект пульсации для андроида.

Пример:

```js
<Tab.Navigator
    //...
    screenOptions={{
        tabBarAndroidRipple: { borderless: false },
    }}
></Tab.Navigator>
```

#### `tabBarPressColor`

Цвет для пульсации материала.

Поддерживается только на Android.

#### `tabBarPressOpacity`

Непрозрачность для нажатой вкладки.

Поддерживается только на iOS.

#### `tabBarBounces`

Булево значение, указывающее, отскакивает ли панель вкладок при перелистывании.

#### `tabBarScrollEnabled`

Булево значение, указывающее, делать ли панель вкладок прокручиваемой.

При установке значения `true` следует также указать ширину в `tabBarItemStyle` для повышения производительности начального рендеринга.

#### `tabBarIconStyle`

Объект стиля для контейнера значка вкладки.

#### `tabBarLabelStyle`

Объект стиля для метки вкладки.

#### `tabBarItemStyle`

Объект стиля для отдельных элементов вкладки.

#### `tabBarContentContainerStyle`

Объект стиля для представления, содержащего элементы вкладки.

#### `tabBarStyle`

Объект стиля для панели вкладок.

#### `swipeEnabled`

Булево значение, указывающее, следует ли включить жесты пролистывания. По умолчанию жесты пролистывания включены. Если передать `false`, то жесты пролистывания будут отключены, но пользователь по-прежнему сможет переключать вкладки, нажимая на панель вкладок.

#### `lazy`

Должен ли этот экран лениво отрисовываться. Если установлено значение `true`, экран будет отрисовываться по мере его появления в области просмотра. По умолчанию все экраны отрисовываются, чтобы обеспечить более плавное пролистывание. Однако вы можете захотеть отложить отрисовку экранов, находящихся вне области просмотра, до тех пор, пока пользователь не увидит их. Чтобы включить ленивый рендеринг для этого экрана, установите `lazy` в значение `true`.

Когда вы включите `lazy`, экраны с ленивой загрузкой обычно будут занимать некоторое время для рендеринга, когда они попадают в область просмотра. Вы можете использовать параметр `lazyPlaceholder` для настройки того, что пользователь видит в течение этого короткого периода.

#### `lazyPreloadDistance`

Когда функция `lazy` включена, с помощью этого параметра можно указать, сколько соседних экранов должно быть загружено заранее. По умолчанию это значение равно `0`, что означает, что "ленивые" страницы загружаются по мере их появления в области просмотра.

#### `lazyPlaceholder`

Функция, возвращающая React-элемент для рендеринга, если этот экран еще не был отрендерен. Для работы этой функции также должна быть включена опция `lazy`.

Это представление обычно показывается только на долю секунды. Сохраняйте его легким.

По умолчанию отображается `null`.

### События {#events}

Навигатор может [выдавать события](navigation-events.md) на определенные действия. Поддерживаются следующие события:

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

#### `tabLongPress`

Это событие возникает, когда пользователь длительное время нажимает кнопку вкладки для текущего экрана на панели вкладок.

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
-   `params` - _object_ - Параметры экрана для передачи маршруту назначения.

```js
navigation.jumpTo('Profile', { name: 'Michaś' });
```

## Пример {#example}

```js
import { createMaterialTopTabNavigator } from '@react-navigation/material-top-tabs';

const Tab = createMaterialTopTabNavigator();

function MyTabs() {
    return (
        <Tab.Navigator
            initialRouteName="Feed"
            screenOptions={{
                tabBarActiveTintColor: '#e91e63',
                tabBarLabelStyle: { fontSize: 12 },
                tabBarStyle: {
                    backgroundColor: 'powderblue',
                },
            }}
        >
            <Tab.Screen
                name="Feed"
                component={Feed}
                options={{ tabBarLabel: 'Home' }}
            />
            <Tab.Screen
                name="Notifications"
                component={Notifications}
                options={{ tabBarLabel: 'Updates' }}
            />
            <Tab.Screen
                name="Profile"
                component={Profile}
                options={{ tabBarLabel: 'Profile' }}
            />
        </Tab.Navigator>
    );
}
```

## Ссылки

-   [Material Top Tabs Navigator](https://reactnavigation.org/docs/material-top-tab-navigator/)
