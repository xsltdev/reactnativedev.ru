---
description: Вы можете использовать функцию PlatformColor для доступа к родным цветам на целевой платформе, предоставив соответствующее строковое значение родного цвета
---

# PlatformColor

```js
PlatformColor(color1, [color2, ...colorN]);
```

Вы можете использовать функцию **`PlatformColor`** для доступа к родным цветам на целевой платформе, предоставив соответствующее строковое значение родного цвета. Вы передаете строку в функцию `PlatformColor` и, если она существует на данной платформе, она возвращает соответствующий родной цвет, который вы можете применить в любой части вашего приложения.

Если вы передадите функции `PlatformColor` более одного строкового значения, она будет рассматривать первое значение как значение по умолчанию, а остальные — как запасные.

```js
PlatformColor('bogusName', 'linkColor');
```

Поскольку родные цвета могут быть чувствительны к темам и/или высокому контрасту, эта логика, специфичная для платформы, также переводится в ваши компоненты.

## Поддерживаемые цвета {#supported-colors}

Полный список типов поддерживаемых системных цветов см:

-   Android:
    -   [R.attr](https://developer.android.com/reference/android/R.attr) — префикс `?attr`.
    -   [R.color](https://developer.android.com/reference/android/R.color) — префикс `@android:color`.
-   iOS (нотации Objective-C и Swift):
    -   [UIColor Standard Colors](https://developer.apple.com/documentation/uikit/uicolor/standard_colors)
    -   [UIColor UI Element Colors](https://developer.apple.com/documentation/uikit/uicolor/ui_element_colors)

**Заметки разработчика**

!!!note "Web"

    Если вы знакомы с системами дизайна, то можно сказать, что `PlatformColor` позволяет вам использовать цветовые маркеры локальной системы дизайна, чтобы ваше приложение вписалось в нее!

## Пример {#example}

<div data-snack-id="@bndby/platformcolor-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Строковое значение, передаваемое функции `PlatformColor`, должно совпадать со строкой, существующей на родной платформе, на которой запущено приложение. Чтобы избежать ошибок во время выполнения, функция должна быть обернута в проверку платформы, либо через `Platform.OS === 'platform'`, либо через `Platform.select()`, как показано в примере выше.

!!!info "Примечание"

    Полный пример, демонстрирующий правильное, целевое использование `PlatformColor`, вы можете найти в [PlatformColorExample.js](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/PlatformColor/PlatformColorExample.js).
