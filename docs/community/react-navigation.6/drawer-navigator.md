---
description: Drawer Navigator отображает на боковой стороне экрана навигационный ящик, который можно открывать и закрывать с помощью жестов
---

# Drawer навигатор

Drawer Navigator отображает на боковой стороне экрана навигационный ящик, который можно открывать и закрывать с помощью жестов.

![Drawer Navigator](drawer.apng)

Это обертывание [`react-native-drawer-layout`](drawer-layout.md). Если вы хотите использовать представление вкладки без интеграции с React Navigation, используйте библиотеку напрямую.

## Установка

Чтобы использовать этот навигатор, убедитесь, что у вас есть [`@react-navigation/native` и его зависимости (следуйте этому руководству)](getting-started.md), затем установите [`@react-navigation/drawer`](https://github.com/react-navigation/react-navigation/tree/main/packages/drawer):

```bash
npm install @react-navigation/drawer
```

Затем необходимо установить и настроить библиотеки, необходимые для работы навигатора ящиков:

1.  Сначала установите [`react-native-gesture-handler`](https://docs.swmansion.com/react-native-gesture-handler/) и [`react-native-reanimated`](https://docs.swmansion.com/react-native-reanimated/).

    Если у вас есть проект, управляемый Expo, то в каталоге проекта выполните команду:

    ```bash
    npx expo install react-native-gesture-handler react-native-reanimated
    ```

    Если у вас есть "голый" проект React Native, в каталоге проекта выполните команду:

    ```bash
    npm install react-native-gesture-handler react-native-reanimated
    ```

    Навигатор Drawer Navigator поддерживает как Reanimated 1, так и Reanimated 2. Если вы хотите использовать Reanimated 2, то обязательно настройте его, следуя [руководству по установке](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation).

2.  Чтобы завершить установку `react-native-gesture-handler`, добавьте следующее в **верх** (убедитесь, что он находится в самом верху и перед ним нет ничего другого) вашего входного файла, например `index.js` или `App.js`:

    ```js
    import 'react-native-gesture-handler';
    ```

    !!!note ""

        Если вы создаете приложение для Android или iOS, не пропускайте этот шаг, иначе оно может упасть в производстве, даже если в разработке оно работает нормально. Это не относится к другим платформам.

3.  Если вы работаете на Mac и разрабатываете приложение для iOS, вам также необходимо установить капсулы (через [Cocoapods](https://cocoapods.org/)) для завершения связывания.

    ```bash
    npx pod-install ios
    ```

## Определение API

Чтобы использовать этот навигатор, импортируйте его из `@react-navigation/drawer`:

```js
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function MyDrawer() {
    return (
        <Drawer.Navigator>
            <Drawer.Screen name="Feed" component={Feed} />
            <Drawer.Screen
                name="Article"
                component={Article}
            />
        </Drawer.Navigator>
    );
}
```

!!!note ""

    Полное руководство по использованию приведено на сайте [Drawer Navigation](drawer-based-navigation.md).

### Пропсы {#props}

Компонент `Drawer.Navigator` принимает следующие параметры:

#### `id`

Необязательный уникальный идентификатор навигатора. Он может быть использован с помощью [`navigation.getParent`](navigation-prop.md#getparent) для ссылки на этот навигатор в дочернем навигаторе.

#### `initialRouteName`

Имя маршрута, который должен отображаться при первой загрузке навигатора.

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

#### `defaultStatus`

Статус ящика по умолчанию - должен ли ящик по умолчанию оставаться `open` или `closed`.

Если установлено значение `open`, то ящик будет открыт с момента первоначального рендеринга. Он может быть закрыт обычным способом с помощью жестов или программно. Однако при возврате назад ящик снова откроется, если он был закрыт. Это, по сути, противоположно поведению ящика по умолчанию, когда он начинается с `closed`, а кнопка назад закрывает открытый ящик.

#### `detachInactiveScreens`

Булево значение, используемое для указания того, следует ли отделять неактивные экраны от иерархии представлений для экономии памяти. Это обеспечивает интеграцию с [react-native-screens](https://github.com/software-mansion/react-native-screens). По умолчанию имеет значение `true`.

#### `useLegacyImplementation`

Использовать ли старую реализацию, основанную на Reanimated 1. Новая реализация, основанная на Reanimated 2, будет работать лучше, но потребует дополнительной настройки и необходимости использования Hermes с Flipper для отладки.

Это значение по умолчанию равно `true` в следующих случаях:

-   Reanimated 2 не настроен
-   Приложение подключено к отладчику Chrome (Reanimated 2 не может быть использован с отладчиком Chrome)
-   Приложение запущено на Web

В противном случае значение по умолчанию равно `false`.

#### `drawerContent`

Функция, возвращающая React-элемент для отображения в качестве содержимого ящика, например, элементов навигации.

Компонент content по умолчанию получает следующие реквизиты:

-   `state` - Состояние [навигации](navigation-state.md) навигатора.
-   `navigation` - Объект навигации для навигатора.
-   `descriptors` - Объект дескриптора, содержащий опции для экранов ящиков. Доступ к опциям можно получить по адресу `descriptors[route.key].options`.

##### Предоставление пользовательского `drawerContent`

Компонент по умолчанию для ящика прокручивается и содержит только ссылки на маршруты в RouteConfig. Вы можете легко переопределить компонент по умолчанию, чтобы добавить в ящик верхний и нижний колонтитулы или другое содержимое. Компонент содержимого по умолчанию экспортируется как `DrawerContent`. Он отображает компонент `DrawerItemList` внутри `ScrollView`.

По умолчанию ящик имеет возможность прокрутки и поддерживает устройства с насечками. Если вы настраиваете содержимое, то можете использовать `DrawerContentScrollView` для автоматической прокрутки:

```js
import {
    DrawerContentScrollView,
    DrawerItemList,
} from '@react-navigation/drawer';

function CustomDrawerContent(props) {
    return (
        <DrawerContentScrollView {...props}>
            <DrawerItemList {...props} />
        </DrawerContentScrollView>
    );
}
```

Для добавления дополнительных элементов в ящик можно использовать компонент `DrawerItem`:

```js
function CustomDrawerContent(props) {
    return (
        <DrawerContentScrollView {...props}>
            <DrawerItemList {...props} />
            <DrawerItem
                label="Help"
                onPress={() =>
                    Linking.openURL(
                        'https://mywebsite.com/help'
                    )
                }
            />
        </DrawerContentScrollView>
    );
}
```

Компонент `DrawerItem` принимает следующие свойства:

-   `label` (required): Текст метки элемента. Может быть строкой или функцией, возвращающей элемент react. Например, `({ focused, color }) => <Text style={{ color }}>{focused ? 'Focused text' : 'Unfocused text'}</Text>`.
-   `icon`: Иконка для отображения элемента. Принимает функцию, возвращающую элемент react. Например, `({ focused, color, size }) => <Icon color={color} size={size} name={focused ? 'heart' : 'heart-outline'} />`.
-   `focused`: Булево значение, указывающее, следует ли выделять элемент ящика как активный.
-   `onPress` (required): Функция, выполняемая при нажатии.
-   `activeTintColor`: Цвет для пиктограммы и метки, когда элемент активен.
-   `inactiveTintColor`: Цвет для пиктограммы и ярлыка, когда элемент неактивен.
-   `activeBackgroundColor`: Цвет фона для активного элемента.
-   `inactiveBackgroundColor`: Цвет фона для неактивного элемента.
-   `labelStyle`: Объект стиля для метки `Text`.
-   `style`: Объект стиля для обертки `View`.

Объект `progress` может быть использован для создания интересной анимации в `drawerContent`, например, параллаксного движения содержимого ящика:

```js
function CustomDrawerContent(props) {
    const progress = useDrawerProgress();

    // If you are on react-native-reanimated 1.x, use `Animated.interpolate` instead of `Animated.interpolateNode`
    const translateX = Animated.interpolateNode(progress, {
        inputRange: [0, 1],
        outputRange: [-100, 0],
    });

    return (
        <Animated.View
            style={{ transform: [{ translateX }] }}
        >
            {/* ... drawer contents */}
        </Animated.View>
    );
}
```

Объект `progress` представляет собой Reanimated `Node`, если используется Reanimated 1 (см. [`useLegacyImplementation`](#uselegacyimplementation)), в противном случае - `SharedValue`. Оно представляет собой анимированное положение drawer (0 - закрыт, 1 - открыт).

Обратите внимание, что вы **не можете** использовать хук `useNavigation` внутри `drawerContent`, поскольку `useNavigation` доступен только внутри экранов. Вы получаете свойство `navigation` для вашего `drawerContent`, которое можно использовать вместо него:

```js
function CustomDrawerContent({ navigation }) {
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

Чтобы использовать пользовательский компонент, нам необходимо передать его в свойстве `drawerContent`:

```js
<Drawer.Navigator
    drawerContent={(props) => (
        <CustomDrawerContent {...props} />
    )}
>
    {/* screens */}
</Drawer.Navigator>
```

### Свойства {#options}

Для настройки экранов в навигаторе можно использовать следующие [options](screen-options.md). Они могут быть указаны в свойствах `screenOptions` свойства `Drawer.navigator` или `options` свойства `Drawer.Screen`.

#### `title`

Общий заголовок, который может использоваться в качестве запасного варианта для `headerTitle` и `drawerLabel`.

#### `lazy`

Должен ли этот экран отображаться при первом обращении к нему. По умолчанию имеет значение `true`. Установите значение `false`, если вы хотите, чтобы экран отображался при первом обращении.

#### `drawerLabel`

Строка или функция, которая по команде `{ focused: boolean, color: string }` возвращает узел React.Node для отображения в боковой панели drawer. Если значение не определено, используется сцена `title`.

#### `drawerIcon`

Функция, задающая `{ focused: boolean, color: string, size: number }`, возвращает React.Node для отображения в боковой панели drawer.

#### `drawerActiveTintColor`

Цвет для пиктограммы и метки активного элемента в drawer.

#### `drawerActiveBackgroundColor`

Цвет фона для активного элемента в drawer.

#### `drawerInactiveTintColor`

Цвет для пиктограммы и метки неактивных элементов в drawer.

#### `drawerInactiveBackgroundColor`

Цвет фона для неактивных элементов в drawer.

#### `drawerItemStyle`

Объект стиля для отдельного элемента, который может содержать иконку и/или метку.

#### `drawerLabelStyle`

Объект стиля для применения к стилю `Text` внутри секции content, в которой отображается метка.

#### `drawerContentContainerStyle`

Объект стиля для секции содержимого внутри `ScrollView`.

#### `drawerContentStyle`

Объект стиля для представления-обертки.

#### `drawerStyle`

Объект стиля для компонента drawer. Сюда можно передать пользовательский цвет фона drawer или его ширину.

```js
<Drawer.Navigator
    screenOptions={{
        drawerStyle: {
            backgroundColor: '#c6cbef',
            width: 240,
        },
    }}
>
    {/* screens */}
</Drawer.Navigator>
```

#### `drawerPosition`

В качестве опций можно выбрать `left` или `right`. По умолчанию `left` для языков LTR и `right` для языков RTL.

#### `drawerType`

Тип ящика. Он определяет внешний вид и анимацию ящика.

-   `front`: Традиционный ящик, закрывающий экран с накладкой за ним.
-   `back`: Ящик открывается за экраном при пролистывании.
-   `slide`: Экран и ящик сдвигаются при пролистывании, открывая ящик.
-   `permanent`: Постоянный ящик отображается в виде боковой панели. Полезно для того, чтобы ящик всегда был виден на больших экранах.

По умолчанию используется значение `slide` на iOS и `front` на других платформах.

Можно условно указать `drawerType` для отображения постоянного ящика на больших экранах и традиционного ящика на маленьких экранах:

```js
import { useWindowDimensions } from 'react-native';
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function MyDrawer() {
    const dimensions = useWindowDimensions();

    return (
        <Drawer.Navigator
            screenOptions={{
                drawerType:
                    dimensions.width >= 768
                        ? 'permanent'
                        : 'front',
            }}
        >
            {/* Screens */}
        </Drawer.Navigator>
    );
}
```

Для настройки поведения можно указать и другие реквизиты, например `drawerStyle`, в зависимости от размера экрана. Например, в сочетании с `defaultStatus="open"` можно получить макет "мастер-деталь":

```js
import { useWindowDimensions } from 'react-native';
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function MyDrawer() {
    const dimensions = useWindowDimensions();

    const isLargeScreen = dimensions.width >= 768;

    return (
        <Drawer.Navigator
            defaultStatus="open"
            screenOptions={{
                drawerType: isLargeScreen
                    ? 'permanent'
                    : 'back',
                drawerStyle: isLargeScreen
                    ? null
                    : { width: '100%' },
                overlayColor: 'transparent',
            }}
        >
            {/* Screens */}
        </Drawer.Navigator>
    );
}
```

#### `drawerHideStatusBarOnOpen`

Если установить значение `true`, Drawer будет скрывать строку состояния ОС, когда ящик потянут или когда он находится в состоянии "открыт".

#### `drawerStatusBarAnimation`

Анимация статусбара при его скрытии. Используется в сочетании с `hideStatusBar`.

Поддерживаемые значения:

-   `slide`
-   `fade`
-   `none`

Это поддерживается только в iOS. По умолчанию используется значение `slide`.

#### `overlayColor`

Цветное наложение, которое будет отображаться поверх представления содержимого при открытии ящика. Непрозрачность анимируется от `0` до `1` при открытии ящика.

#### `sceneContainerStyle`

Объект стиля для компонента, оборачивающего содержимое экрана.

#### `gestureHandlerProps`

Свойства для передачи базовому обработчику жестов панорамирования.

Это не поддерживается в Web.

#### `swipeEnabled`

Можно ли использовать жесты пролистывания для открытия или закрытия ящика. По умолчанию имеет значение `true`.

Жест пролистывания не поддерживается в Web.

#### `swipeEdgeWidth`

Позволяет определить, на каком расстоянии от края представления содержимого должен активироваться жест пролистывания.

В Web эта функция не поддерживается.

#### `swipeMinDistance`

Минимальный порог расстояния между пальцами, при котором должно активироваться открытие ящика.

#### `keyboardDismissMode`

Должна ли клавиатура отключаться, когда начинается жест пролистывания. По умолчанию имеет значение `'on-drag'`. Установите значение `'none'', чтобы отключить обработку клавиатуры.

#### `unmountOnBlur`

Следует ли размонтировать этот экран при навигации от него. При размонтировании экрана сбрасываются все локальные состояния экрана, а также состояния вложенных в него навигаторов. По умолчанию имеет значение `false`.

Обычно мы не рекомендуем включать этот параметр, поскольку пользователи не ожидают, что их история навигации будет потеряна при переключении экранов. Если вы включаете этот параметр, пожалуйста, подумайте, действительно ли это обеспечит лучший опыт для пользователя.

#### `freezeOnBlur`

Булево значение, указывающее, следует ли запретить повторное отображение неактивных экранов. По умолчанию имеет значение `false`.

По умолчанию принимает значение `true`, если `enableFreeze()` из пакета `react-native-screens` запущен в верхней части приложения.

Требуется версия `react-native-screens` >=3.16.0.

Поддерживается только на iOS и Android.

### Опции, связанные с заголовками

Список опций, связанных с заголовками, можно найти [здесь](elements.md#header). Эти [опции](screen-options.md) могут быть указаны в свойствах `screenOptions` свойства `Drawer.navigator` или `options` свойства `Drawer.Screen`. Для использования этих опций не обязательно использовать непосредственно `@react-navigation/elements`, они просто документированы на этой странице.

Кроме них, в drawer также поддерживаются следующие опции:

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

Навигатор может [выдавать события](navigation-events.md) на определенные действия. Поддерживаются следующие события:

#### `drawerItemPress`

Это событие возникает, когда пользователь нажимает кнопку для экрана в ящике. По умолчанию нажатие элемента ящика выполняет несколько действий:

-   Если экран не сфокусирован, то нажатие элемента ящика сфокусирует этот экран.
-   Если экран уже сфокусирован, то это приведет к закрытию ящика.

Чтобы предотвратить поведение по умолчанию, можно вызвать `event.preventDefault`:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'drawerItemPress',
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

Если у вас есть пользовательское содержимое ящика, обязательно вызовите это событие.

### Хелперы {#helpers}

Навигатор drawer добавляет в навигационный параметр следующие методы:

#### `openDrawer`

Открывает панель drawer.

```js
navigation.openDrawer();
```

#### `closeDrawer`

Закрывает панель drawer.

```js
navigation.closeDrawer();
```

#### `toggleDrawer`

Открывает панель drawer, если она закрыта, и закрывает панель drawer, если она открыта.

```js
navigation.toggleDrawer();
```

#### `jumpTo`

Осуществляет переход к существующему экрану в навигаторе ящика. Метод принимает следующие аргументы:

-   `name` - _string_ - Имя маршрута, на который необходимо перейти.
-   `params` - _object_ - Параметры экрана для передачи маршруту назначения.

```js
navigation.jumpTo('Profile', { owner: 'Satya' });
```

## Пример {#example}

```js
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function MyDrawer() {
    return (
        <Drawer.Navigator initialRouteName="Feed">
            <Drawer.Screen
                name="Feed"
                component={Feed}
                options={{ drawerLabel: 'Home' }}
            />
            <Drawer.Screen
                name="Notifications"
                component={Notifications}
                options={{ drawerLabel: 'Updates' }}
            />
            <Drawer.Screen
                name="Profile"
                component={Profile}
                options={{ drawerLabel: 'Profile' }}
            />
        </Drawer.Navigator>
    );
}
```

## Проверка открытости drawer

Проверить, открыт ли drawer, можно с помощью хука `useDrawerStatus`.

```js
import { useDrawerStatus } from '@react-navigation/drawer';

// ...

const isDrawerOpen = useDrawerStatus() === 'open';
```

Если нет возможности использовать хук, можно также воспользоваться хелпером `getDrawerStatusFromState`:

```js
import { getDrawerStatusFromState } from '@react-navigation/drawer';

// ...

const isDrawerOpen =
    getDrawerStatusFromState(navigation.getState()) ===
    'open';
```

Для компонентов класса можно прослушать событие `state`, чтобы проверить, был ли ящик открыт или закрыт:

```js
class Profile extends React.Component {
    componentDidMount() {
        this._unsubscribe = navigation.addListener(
            'state',
            () => {
                const isDrawerOpen =
                    getDrawerStatusFromState(
                        navigation.getState()
                    ) === 'open';

                // do something
            }
        );
    }

    componentWillUnmount() {
        this._unsubscribe();
    }

    render() {
        // Content of the component
    }
}
```

## Вложение навигаторов drawer в другие навигаторы

Если навигатор drawer вложен в другой навигатор, предоставляющий некоторый пользовательский интерфейс, например, навигатор табов или навигатор стека, то drawer будет отображаться ниже пользовательского интерфейса этих навигаторов. Drawer будет отображаться под панелью табов и под заголовком стека. Необходимо сделать навигатор drawer родителем любого навигатора, в котором drawer должен отображаться поверх его пользовательского интерфейса.

## Ссылки

-   [Drawer Navigator](https://reactnavigation.org/docs/drawer-navigator/)
