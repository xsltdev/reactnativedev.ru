---
description: Иногда требуется запустить навигационное действие из мест, где нет доступа к реквизиту navigation, например, из промежуточного модуля Redux
---

# Навигация без навигационного пропса

Иногда требуется запустить навигационное действие из мест, где нет доступа к реквизиту `navigation`, например, из промежуточного модуля Redux. В таких случаях для диспетчеризации навигационных действий можно использовать [`ref` на контейнер навигации](navigation-container.md#ref).

**Не** используйте `ref`, если:

-   Вам нужно осуществлять навигацию изнутри компонента без необходимости передачи свойства `navigation` вниз, вместо этого смотрите [`useNavigation`](use-navigation.md). Реквизит `ref` ведет себя по-другому, и многие вспомогательные методы, специфичные для экранов, недоступны.
-   Необходимо обрабатывать глубокие ссылки или универсальные ссылки. Это можно сделать с помощью `ref`. Дополнительную информацию о работе с глубокими ссылками см. в [configuring links](configuring-links.md).
-   Необходима интеграция со сторонними библиотеками, такими как push-уведомления, branch и т.д. Смотрите [Интеграции сторонних библиотек для глубокого связывания](deep-linking.md#third-party-integrations) вместо этого.

**Используйте** `ref`, если:

-   Вы используете библиотеку управления состоянием, такую как Redux, где вам необходимо диспетчеризировать действия навигации из промежуточного ПО.

Обратите внимание, что обычно лучше запускать навигацию от действий пользователя, например, от нажатия кнопки, а не от промежуточного ПО Redux. Навигация по действиям пользователя делает приложение более отзывчивым и обеспечивает лучший UX. Поэтому подумайте об этом, прежде чем использовать `ref` для навигации. `ref` - это выход для сценариев, которые не могут быть реализованы с помощью существующих API, и должен использоваться только в редких случаях.

## Использование

Вы можете получить доступ к корневому объекту навигации через `ref` и передать его в `RootNavigation`, который мы в дальнейшем будем использовать для навигации.

```js
// App.js

import { NavigationContainer } from '@react-navigation/native';
import { navigationRef } from './RootNavigation';

export default function App() {
    return (
        <NavigationContainer ref={navigationRef}>
            {/* ... */}
        </NavigationContainer>
    );
}
```

На следующем шаге мы определяем `RootNavigation`, который представляет собой простой модуль с функциями, диспетчеризирующими определенные пользователем навигационные действия.

```js
// RootNavigation.js

import { createNavigationContainerRef } from '@react-navigation/native';

export const navigationRef = createNavigationContainerRef();

export function navigate(name, params) {
    if (navigationRef.isReady()) {
        navigationRef.navigate(name, params);
    }
}

// add other navigation functions that you need and export them
```

Затем в любом из своих javascript-модулей импортируйте `RootNavigation` и вызывайте экспортированные из него функции. Этот подход можно использовать вне React-компонентов, и, по сути, он также хорошо работает, если использовать его внутри них.

```js
// any js module
import * as RootNavigation from './path/to/RootNavigation.js';

// ...

RootNavigation.navigate('ChatScreen', { userName: 'Lucy' });
```

Кроме `navigate`, можно добавить и другие действия навигации:

```js
import { StackActions } from '@react-navigation/native';

// ...

export function push(...args) {
    if (navigationRef.isReady()) {
        navigationRef.dispatch(StackActions.push(...args));
    }
}
```

Обратите внимание, что для выполнения этого действия необходимо отрисовать стек навигаторов. Более подробную информацию можно найти в [документации по вложению](nesting-navigators.md#navigating-to-a-screen-in-a-nested-navigator).

При написании тестов можно подражать функциям навигации и делать утверждения о том, что вызываются правильные функции с правильными параметрами.

## Обработка инициализации

При использовании данного паттерна необходимо учитывать несколько моментов, чтобы избежать сбоев в работе навигации в приложении.

-   Установка `ref` происходит только после рендеринга контейнера навигации, при работе с глубокими ссылками это может быть асинхронно
-   Навигатор должен быть отрендерен, чтобы иметь возможность обрабатывать действия, `ref` не будет готов без навигатора

Если попытаться выполнить навигацию без рендеринга навигатора или до того, как навигатор закончит монтироваться, то будет выведена ошибка и ничего не будет сделано. Поэтому необходимо добавить дополнительную проверку, чтобы решить, что делать, пока приложение не смонтируется.

Для примера рассмотрим следующий сценарий: у вас есть экран где-то в приложении, и этот экран отправляет действие redux по команде `useEffect`/`componentDidMount`. Вы слушаете это действие в своем промежуточном ПО и пытаетесь выполнить навигацию, когда получаете его. Это приведет к ошибке, поскольку к этому моменту родительский навигатор еще не закончил монтирование и не готов. Родительский `useEffect`/`componentDidMount` всегда вызывается **после** дочернего `useEffect`/`componentDidMount`.

Чтобы избежать этого, можно использовать метод `isReady()`, доступный на ref, как показано в примерах выше.

```js
// RootNavigation.js

import * as React from 'react';

export const navigationRef = createNavigationContainerRef();

export function navigate(name, params) {
    if (navigationRef.isReady()) {
        // Perform navigation if the react navigation is ready to handle actions
        navigationRef.navigate(name, params);
    } else {
        // You can decide what to do if react navigation is not ready
        // You can ignore this, or add these actions to a queue you can call later
    }
}
```

Если вы не уверены, что навигатор отображается, можно вызвать `navigationRef.current.getRootState()`, и он вернет корректный объект состояния, если все навигаторы отображаются, в противном случае он вернет `undefined`.

## Ссылки

-   [Navigating without the navigation prop](https://reactnavigation.org/docs/navigating-without-navigation-prop)
