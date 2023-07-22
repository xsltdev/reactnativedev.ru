---
description: Testing code using React Navigation may require some setup since we need to mock native dependencies used in the navigators
---

# Тестирование с Jest

Тестирование кода с использованием React Navigation может потребовать некоторой настройки, поскольку нам необходимо имитировать нативные зависимости, используемые в навигаторах. Мы рекомендуем использовать [Jest](https://jestjs.io) для написания модульных тестов.

## Подражание нативным модулям

Чтобы протестировать компоненты React Navigation, необходимо подружить некоторые зависимости в зависимости от того, какие компоненты используются.

Если вы используете `@react-navigation/drawer`, то вам нужно будет подружить:

-   `react-native-reanimated`
-   `react-native-gesture-handler`.

Если вы используете `@react-navigation/stack`, то вам нужно только подражать:

-   `react-native-gesture-handler`.

Для добавления макетов создайте файл `jest/setup.js` (или любое другое имя файла по вашему выбору) и вставьте в него следующий код:

```js
// include this line for mocking react-native-gesture-handler
import 'react-native-gesture-handler/jestSetup';

// include this section and the NativeAnimatedHelper section for mocking react-native-reanimated
jest.mock('react-native-reanimated', () => {
    const Reanimated = require('react-native-reanimated/mock');

    // The mock for `call` immediately calls the callback which is incorrect
    // So we override it with a no-op
    Reanimated.default.call = () => {};

    return Reanimated;
});

// Silence the warning: Animated: `useNativeDriver` is not supported because the native animated module is missing
jest.mock(
    'react-native/Libraries/Animated/NativeAnimatedHelper'
);
```

Затем нам необходимо использовать этот установочный файл в конфигурации jest. Вы можете добавить его в опцию `setupFiles` в файле `jest.config.js` или в ключ `jest` в файле `package.json`:

```json
{
    "preset": "react-native",
    "setupFiles": ["<rootDir>/jest/setup.js"]
}
```

Убедитесь, что путь к файлу в `setupFiles` указан правильно. Jest будет запускать эти файлы перед выполнением тестов, поэтому это лучшее место для размещения глобальных имитаторов.

Если вы не используете Jest, то вам придется подставлять эти модули в соответствии с используемым тестовым фреймворком.

## Ссылки

-   [Testing with Jest](https://reactnavigation.org/docs/testing/)
