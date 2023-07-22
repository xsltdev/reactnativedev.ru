---
description: Темы позволяют изменять цвета различных компонентов, предоставляемых React Navigation
---

# Темы

Темы позволяют изменять цвета различных компонентов, предоставляемых React Navigation. Темы можно использовать для:

-   Настроить цвета в соответствии с вашим брендом
-   Предоставить светлые и темные темы в зависимости от времени суток или предпочтений пользователя.

## Базовое использование

Чтобы передать пользовательскую тему, можно передать свойство `theme` контейнеру навигации.

```js
import * as React from 'react';
import {
    NavigationContainer,
    DefaultTheme,
} from '@react-navigation/native';

const MyTheme = {
    ...DefaultTheme,
    colors: {
        ...DefaultTheme.colors,
        primary: 'rgb(255, 45, 85)',
    },
};

export default function App() {
    return (
        <NavigationContainer theme={MyTheme}>
            {/* content */}
        </NavigationContainer>
    );
}
```

Вы можете динамически изменять реквизит темы, и все компоненты будут автоматически обновляться в соответствии с новой темой. Если вы не указали реквизит `theme`, то будет использоваться тема по умолчанию.

Тема - это JS-объект, содержащий список используемых цветов. Она содержит следующие свойства:

-   `dark` (`boolean`): Является ли эта тема темной или светлой.
-   `colors` (`object`): Различные цвета, используемые компонентами навигации react:
    -   `primary` (`string`): Основной цвет приложения, используемый для оттенка различных элементов. Обычно для этого используется цвет вашего бренда.
    -   `background` (`string`): Цвет различных фоновых элементов, например, цвет фона для экранов.
    -   `card` (`string`): Цвет фона элементов, похожих на карту, таких как заголовки, панели вкладок и т.д.
    -   `text` (`string`): Цвет текста различных элементов.
    -   `border` (`string`): Цвет границ, например, границы заголовка, границы панели вкладок и т.д.
    -   `notification` (`string`): Цвет значка Tab Navigator.

При создании пользовательской темы необходимо указать все эти свойства.

Пример темы:

```js
const MyTheme = {
    dark: false,
    colors: {
        primary: 'rgb(255, 45, 85)',
        background: 'rgb(242, 242, 242)',
        card: 'rgb(255, 255, 255)',
        text: 'rgb(28, 28, 30)',
        border: 'rgb(199, 199, 204)',
        notification: 'rgb(255, 69, 58)',
    },
};
```

Предоставление темы позаботится о стилизации всех официальных навигаторов. React Navigation также предоставляет несколько инструментов для настройки этих навигаторов, а экраны внутри навигаторов также могут использовать тему.

## Встроенные темы

По мере того как операционные системы добавляют встроенную поддержку светлого и темного режимов, поддержка темного режима становится не столько модным трендом, сколько необходимостью соответствовать ожиданиям рядовых пользователей в отношении работы приложений. Для того чтобы обеспечить поддержку светлого и темного режимов в разумном соответствии с настройками ОС по умолчанию, эти темы встроены в React Navigation.

Импортировать темы по умолчанию и темные темы можно следующим образом:

```js
import {
    DefaultTheme,
    DarkTheme,
} from '@react-navigation/native';
```

## Использование предпочтений операционной системы

На iOS 13+ и Android 10+ можно получить предпочитаемую пользователем цветовую схему (`dark` или `light`) с помощью ([Appearance API](https://reactnative.dev/docs/appearance)).

```js
import { useColorScheme } from 'react-native';
import {
    NavigationContainer,
    DefaultTheme,
    DarkTheme,
} from '@react-navigation/native';

export default () => {
    const scheme = useColorScheme();

    return (
        <NavigationContainer
            theme={
                scheme === 'dark' ? DarkTheme : DefaultTheme
            }
        >
            {/* content */}
        </NavigationContainer>
    );
};
```

## Использование текущей темы в собственных компонентах

Для получения доступа к теме в любом компоненте, который рендерится внутри навигационного контейнера:, можно использовать хук `useTheme`. Он возвращает объект темы:

```js
import * as React from 'react';
import { TouchableOpacity, Text } from 'react-native';
import { useTheme } from '@react-navigation/native';

// Black background and white text in light theme, inverted on dark theme
function MyButton() {
    const { colors } = useTheme();

    return (
        <TouchableOpacity
            style={{ backgroundColor: colors.card }}
        >
            <Text style={{ color: colors.text }}>
                Button!
            </Text>
        </TouchableOpacity>
    );
}
```

## Ссылки

-   [Themes](https://reactnavigation.org/docs/themes)
