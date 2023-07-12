# DynamicColorIOS

Функция **`DynamicColorIOS`** - это тип цвета платформы, специфичный для iOS.

```ts
DynamicColorIOS({
    light: color,
    dark: color,
    highContrastLight: color, // (optional) will fallback to "light" if not provided
    highContrastDark: color, // (optional) will fallback to "dark" if not provided
});
```

`DynamicColorIOS` принимает единственный аргумент в виде объекта с двумя обязательными ключами: `dark` и `light`, и двумя необязательными ключами `highContrastLight` и `highContrastDark`. Они соответствуют цветам, которые вы хотите использовать для "светлого режима" и "темного режима" на iOS, а когда включен режим высококонтрастной доступности, высококонтрастной их версии.

Во время выполнения система будет выбирать, какой из цветов отображать, в зависимости от текущего внешнего вида системы и настроек доступности. Динамические цвета полезны для брендинговых цветов или других специфических цветов приложения, которые при этом автоматически реагируют на изменения системных настроек.

**Заметки для разработчика**

=== "Web"

    Если вы знакомы с `@media (prefers-color-scheme: dark)` в CSS, это похоже! Только вместо того, чтобы определять все цвета в медиа-запросе, вы определяете, какой цвет использовать при тех или иных обстоятельствах, прямо там, где вы его используете. Отлично!

=== "iOS"

    Функция `DynamicColorIOS` аналогична нативным методам iOS [`UIColor colorWithDynamicProvider:`](https://developer.apple.com/documentation/uikit/uicolor/3238040-colorwithdynamicprovider)

## Пример

```ts
import { DynamicColorIOS } from 'react-native';

const customDynamicTextColor = DynamicColorIOS({
    dark: 'lightskyblue',
    light: 'midnightblue',
});

const customContrastDynamicTextColor = DynamicColorIOS({
    dark: 'darkgray',
    light: 'lightgray',
    highContrastDark: 'black',
    highContrastLight: 'white',
});
```
