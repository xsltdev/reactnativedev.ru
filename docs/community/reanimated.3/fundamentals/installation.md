---
description: Установка Reanimated требует нескольких дополнительных шагов по сравнению с установкой большинства популярных пакетов react-native
---

# Установка

Установка Reanimated требует нескольких дополнительных шагов по сравнению с установкой большинства популярных пакетов react-native. Шаги, необходимые для правильной настройки Reanimated, перечислены в следующих пунктах.

## Установка пакета

Первым шагом является установка `react-native-reanimated` в качестве зависимости в ваш проект:

```bash
yarn add react-native-reanimated
```

## Плагин Babel

Добавьте плагин babel от Reanimated в ваш файл `babel.config.js`:

```js
module.exports = {
    presets: [
        // ...
    ],
    plugins: [
        // ...
        'react-native-reanimated/plugin',
    ],
};
```

По умолчанию плагин Reanimated генерирует местоположение источника по абсолютному пути. Вы можете настроить использование относительного пути:

```js
module.exports = {
    presets: [
        // ...
    ],
    plugins: [
        // ...
        [
            'react-native-reanimated/plugin',
            {
                relativeSourceLocation: true,
            },
        ],
    ],
};
```

!!!warning ""

    Плагин Reanimated должен быть указан последним.

!!!info ""

    После добавления плагина `react-native-reanimated/plugin` в проект, может возникнуть ложноположительная ошибка "Reanimated 2 failed to create a worklet". В большинстве случаев это можно исправить, очистив кэш приложения. В зависимости от вашего рабочего процесса или используемого пакетного менеджера это можно сделать следующим образом:

    -   `yarn start --reset-cache`
    -   `npm start -- --reset-cache`
    -   `expo start -c`

    или другим способом.

## Android

Никаких дополнительных действий не требуется.

## iOS

Поскольку reanimated настроен на автоматическую настройку и установку, единственное, что вам нужно сделать, это запустить `pod install` в директории `ios/`. Обратите внимание, что автоматическая установка работает для стандартных приложений React Native, если у вас возникнут проблемы с настройкой пользовательских приложений (например, brownfield), пожалуйста, создайте новый вопрос, в котором мы сможем найти способ помочь вам в этом процессе.

## Web

Необходимо добавить [`@babel/plugin-proposal-export-namespace-from`](https://babeljs.io/docs/en/babel-plugin-proposal-export-namespace-from) плагин Babel.

```bash
yarn add @babel/plugin-proposal-export-namespace-from
```

```js
module.exports = {
    presets: [
        // ...
    ],
    plugins: [
        // ...
        '@babel/plugin-proposal-export-namespace-from',
        'react-native-reanimated/plugin',
    ],
};
```

## Пример проекта React-Native, сконфигурированного с Reanimated

Если у вас возникли проблемы с настройкой Reanimated в вашем проекте или вы просто хотите попробовать библиотеку без необходимости ее настройки на свежем проекте, мы рекомендуем обратиться к нашему репозиторию [Reanimated Playground](https://github.com/software-mansion-labs/reanimated-2-playground), который, по сути, является свежим React-Native-приложением с установленной и настроенной должным образом библиотекой Reanimated.

[Посетите репо Playground здесь](https://github.com/software-mansion-labs/reanimated-2-playground) или скопируйте приведенную ниже команду для создания git-клона:

```
git clone https://github.com/software-mansion-labs/reanimated-2-playground.git
```

## Ссылки

-   [Installation](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation)
