---
description: Кроссплатформенный компонент Drawer для React Native, реализованный с использованием react-native-gesture-handler и react-native-reanimated
---

# React Native Drawer Layout

Кроссплатформенный компонент **`Drawer`** для React Native, реализованный с использованием [`react-native-gesture-handler`](https://docs.swmansion.com/react-native-gesture-handler/) и [`react-native-reanimated`](https://docs.swmansion.com/react-native-reanimated/).

![Drawer Navigator](drawer.apng)

Этот пакет не интегрируется с React Navigation. Если вы хотите интегрировать компоновку drawer с навигационной системой React Navigation, например, отображать экраны в drawer и иметь возможность переходить между ними с помощью `navigation.navigate` и т.д., используйте вместо этого [Drawer Navigator](drawer-navigator.md).

## Установка

Чтобы использовать этот пакет, откройте Терминал в корне проекта и выполните команду:

```bash
npm install react-native-drawer-layout
```

Затем необходимо установить и настроить библиотеки, которые требуются drawer:

1.  Сначала установите [`react-native-gesture-handler`](https://docs.swmansion.com/react-native-gesture-handler/) и [`react-native-reanimated`](https://docs.swmansion.com/react-native-reanimated/).

    Если у вас Expo-управляемый проект, то в каталоге проекта выполните команду:

    ```bash
    npx expo install react-native-gesture-handler react-native-reanimated
    ```

    Если у вас есть "голый" проект React Native, в каталоге проекта выполните команду:

    ```bash
    npm install react-native-gesture-handler react-native-reanimated
    ```

    Drawer поддерживает как Reanimated 1, так и Reanimated 2. Если вы хотите использовать Reanimated 2, обязательно настройте его, следуя [руководству по установке](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation).

2.  Чтобы завершить установку `react-native-gesture-handler`, добавьте следующее в **top** (убедитесь, что он находится в самом верху и перед ним нет ничего другого) вашего входного файла, например `index.js` или `App.js`:

    ```js
    import 'react-native-gesture-handler';
    ```

    !!!note ""

        Если вы создаете приложение для Android или iOS, не пропускайте этот шаг, иначе оно может упасть в производстве, даже если в разработке оно работает нормально. Это не относится к другим платформам.

3.  Если вы работаете на Mac и разрабатываете под iOS, то для завершения связывания необходимо также установить капсулы (через [Cocoapods](https://cocoapods.org/)).

    ```bash
    npx pod-install ios
    ```

Готово! Теперь вы можете собрать и запустить приложение на своем устройстве/симуляторе.

## Быстрый старт

```js
import * as React from 'react';
import { Button, Text } from 'react-native';
import { Drawer } from 'react-native-drawer-layout';

export default function DrawerExample() {
    const [open, setOpen] = React.useState(false);

    return (
        <Drawer
            open={open}
            onOpen={() => setOpen(true)}
            onClose={() => setOpen(false)}
            renderDrawerContent={() => {
                return <Text>Drawer content</Text>;
            }}
        >
            <Button
                onPress={() =>
                    setOpen((prevOpen) => !prevOpen)
                }
                title={`${open ? 'Close' : 'Open'} drawer`}
            />
        </Drawer>
    );
}
```

## Справочник по API

Пакет экспортирует компонент `Drawer`, который используется для визуализации ящика.

### `Drawer`

Компонент, отвечающий за отрисовку ящика с анимацией и жестами.

Параметры `Drawer`

**`open`**

Открыт ли ящик или нет.

**`onOpen`**

Обратный вызов, который вызывается при открытии ящика.

**`onClose`**

Обратный вызов, который вызывается при закрытии ящика.

**`renderDrawerContent`**

Обратный вызов, который возвращает элемент react для отображения в качестве содержимого ящика.

**`layout`**

Объект, содержащий макет контейнера. По умолчанию соответствует размерам окна приложения.

**`drawerPosition`**

Положение ящика на экране. По умолчанию в режиме RTL - `правая`, в остальных случаях - `левая`.

**`drawerType`**

Тип ящика. Он определяет внешний вид и анимацию ящика.

-   `front`: Традиционный ящик, закрывающий экран с накладкой за ним.
-   `back`: Ящик открывается за экраном при пролистывании.
-   `slide`: Экран и ящик сдвигаются при пролистывании, открывая ящик.
-   `permanent`: Постоянный ящик отображается в виде боковой панели.

По умолчанию `slide` на iOS и `front` на других платформах.

**`drawerStyle`**

Объект стиля для ящика. Вы можете передать пользовательский цвет фона для ящика или его ширину.

**`overlayStyle`**

Объект стиля для оверлея.

**`hideStatusBarOnOpen`**

Скрывать ли строку состояния при открытии ящика. По умолчанию имеет значение `false`.

**`keyboardDismissMode`**

Должна ли клавиатура отключаться при открытии ящика. Поддерживаются следующие значения:

-   `none`: Клавиатура не будет отключаться при открытии ящика.
-   `on-drag`: Клавиатура будет отключаться при открытии ящика жестом пролистывания.

По умолчанию установлено значение `on-drag`.

**`statusBarAnimation`**

Анимация, используемая при скрытии строки состояния. Поддерживаются следующие значения:

-   `slide`: Строка состояния будет скрыта из виду.
-   `fade`: Строка состояния будет исчезать из поля зрения.
-   `none`: Строка состояния не будет анимироваться.

Используется в сочетании с `hideStatusBarOnOpen`.

**`swipeEnabled`**

Включать ли жесты пролистывания для открытия ящика. По умолчанию имеет значение `true`.

Жесты пролистывания поддерживаются только на iOS и Android.

**`swipeEdgeWidth`**

На каком расстоянии от края экрана должен активироваться жест пролистывания. По умолчанию `32`.

Поддерживается только на iOS и Android.

**`swipeMinDistance`**

Минимальное расстояние пролистывания, при котором должно активироваться открытие ящика. По умолчанию `60`.

Поддерживается только на iOS и Android.

**`swipeMinVelocity`**

Минимальная скорость движения, при которой должно активироваться открытие ящика. По умолчанию `500`.

Поддерживается только на iOS и Android.

**`gestureHandlerProps`**

Реквизиты для передачи базовому обработчику жестов панорамирования.

Поддерживается только на iOS и Android.

**`children`**

Содержимое, в которое должен быть обернут ящик.

### `useDrawerProgress`

Хук `useDrawerProgress` возвращает Reanimated SharedValue (при современной реализации) или Reanimated Node (при устаревшей реализации), которые представляют прогресс работы ящика. Его можно использовать для анимирования содержимого экрана.

Пример с современной реализацией:

```js
import { Animated } from 'react-native-reanimated';
import { useDrawerProgress } from 'react-native-drawer-layout';

// ...

function MyComponent() {
    const progress = useDrawerProgress();

    const animatedStyle = useAnimatedStyle(() => {
        return {
            transform: [
                {
                    translateX: interpolate(
                        progress,
                        [0, 1],
                        [-100, 0]
                    ),
                },
            ],
        };
    });

    return (
        <Animated.View style={animatedStyle}>
            {/* ... */}
        </Animated.View>
    );
}
```

Пример с унаследованной реализацией:

```js
import { Animated } from 'react-native-reanimated';
import { useDrawerProgress } from 'react-native-drawer-layout';

// ...

function MyComponent() {
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
            {/* ... */}
        </Animated.View>
    );
}
```

Если вы используете компоненты класса, то для получения значения прогресса можно использовать `DrawerProgressContext`.

```js
import { DrawerProgressContext } from 'react-native-drawer-layout';

// ...

class MyComponent extends React.Component {
    static contextType = DrawerProgressContext;

    render() {
        const progress = this.context;

        // ...
    }
}
```

## Ссылки

-   [React Native Drawer Layout](https://reactnavigation.org/docs/drawer-layout/)
