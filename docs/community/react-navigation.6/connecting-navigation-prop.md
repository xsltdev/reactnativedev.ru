---
description: useNavigation - это хук, предоставляющий доступ к объекту navigation
---

# Доступ к навигационному параметру из любого компонента

[`useNavigation`](use-navigation.md) - это хук, предоставляющий доступ к объекту `navigation`. Он полезен, когда вы не можете передать свойство `navigation` в компонент напрямую или не хотите передавать его в случае глубоко вложенного дочернего компонента.

Обычный компонент, не являющийся компонентом экрана, не получит автоматически реквизит навигации. Например, в этом компоненте `GoToButton`:

```js
import * as React from 'react';
import { Button } from 'react-native';

function GoToButton({ navigation, screenName }) {
    return (
        <Button
            title={`Go to ${screenName}`}
            onPress={() => navigation.navigate(screenName)}
        />
    );
}
```

Чтобы решить это исключение, можно передать свойство `navigation` в `GoToButton` при его рендеринге с экрана, например, так: `<GoToButton navigation={props.navigation} />`.

В качестве альтернативы можно использовать `useNavigation` для автоматического предоставления свойства `navigation` (через контекст React).

```js
import * as React from 'react';
import { Button } from 'react-native';
import { useNavigation } from '@react-navigation/native';

function GoToButton({ screenName }) {
    const navigation = useNavigation();

    return (
        <Button
            title={`Go to ${screenName}`}
            onPress={() => navigation.navigate(screenName)}
        />
    );
}
```

Используя этот подход, вы можете отобразить `GoToButton` в любом месте вашего приложения без явной передачи свойства `navigation`, и она будет работать так, как ожидалось.

## Ссылки

-   [Access the navigation prop from any component](https://reactnavigation.org/docs/connecting-navigation-prop)
