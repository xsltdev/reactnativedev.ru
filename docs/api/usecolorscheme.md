---
description: Реактивный хук useColorScheme предоставляет и подписывается на обновления цветовой схемы из модуля Appearance
---

# useColorScheme

```ts
import { useColorScheme } from 'react-native';
```

Реактивный хук `useColorScheme` предоставляет и подписывается на обновления цветовой схемы из модуля [`Appearance`](appearance.md). Возвращаемое значение указывает текущую предпочитаемую пользователем цветовую схему. Значение может быть обновлено позже, либо через прямое действие пользователя (например, выбор темы в настройках устройства), либо по расписанию (например, светлые и темные темы, которые следуют циклу день/ночь).

## Поддерживаемые цветовые схемы {#supported-color-schemes}

-   `light`: Пользователь предпочитает светлую цветовую тему.
-   `dark`: Пользователь предпочитает темную цветовую тему.
-   `null`: Пользователь не указал предпочитаемую цветовую тему.

!!!note ""

    В настоящее время из-за технических ограничений, когда включен отладчик Chrome, этот хук будет _всегда_ возвращать `"light"`.

## Пример {#example}

<div data-snack-id="@bndby/usecolorscheme-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Полный пример, демонстрирующий использование этого хука вместе с контекстом React для добавления поддержки светлых и темных тем в ваше приложение, можно найти в [`AppearanceExample.js`](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/Appearance/AppearanceExample.js).
