---
description: Раздел Фундаментальные основы был разработан для того, чтобы помочь вам получить прочный фундамент основных концепций Reanimated и дать вам уверенность в себе для самостоятельного изучения более сложных примеров использования
---

# Начало работы

Раздел _Фундаментальные основы_ был разработан для того, чтобы помочь вам получить прочный фундамент основных концепций Reanimated и дать вам уверенность в себе для самостоятельного изучения более сложных примеров использования. Этот раздел содержит интерактивные примеры, фрагменты кода и пояснения. Вы готовы? Давайте погрузимся!

## Что такое React Native Reanimated?

**React Native Reanimated** - это мощная библиотека анимации, созданная компанией [Software Mansion](https://swmansion.com/).

С помощью Reanimated можно легко создавать плавные анимации и взаимодействия, работающие в потоке [UI thread](glossary.md#ui-thread).

## Быстрый старт

Если у вас нет существующего проекта, вы можете создать новое приложение Expo, используя шаблон:

=== "npm"

    ```shell
    npx create-expo-app my-app -e with-reanimated
    ```

=== "yarn"

    ```shell
    yarn create expo-app my-app -e with-reanimated
    ```

В качестве альтернативы вы можете изучить [наши примеры](https://github.com/software-mansion/react-native-reanimated/tree/3.3.0/app/src/examples) на GitHub.

## Установка

Чтобы добавить Reanimated в проект, необходимо выполнить три шага:

### Шаг 1: Установка пакета

Установите пакет `react-native-reanimated` из npm:

=== "Expo"

    ```shell
    npx expo install react-native-reanimated
    ```

=== "npm"

    ```shell
    npm install react-native-reanimated
    ```

=== "yarn"

    ```shell
    yarn add react-native-reanimated
    ```

### Шаг 2: Добавьте плагин babel от Reanimated

Добавьте плагин `react-native-reanimated/plugin` в ваш файл `babel.config.js`.

```js
module.exports = {
    presets: [
        // ... // don't add it here :)
    ],
    plugins: [
        // ...
        'react-native-reanimated/plugin',
    ],
};
```

!!!warning ""

    `react-native-reanimated/plugin` должен быть указан последним.

!!!tip "Why do I need this?"

    Одним словом, плагин Reanimated babel автоматически преобразует специальные JavaScript-функции (называемые [worklets](glossary.md#worklet)) таким образом, чтобы они могли быть переданы и запущены в потоке UI.

    Для получения более подробной информации о плагине перейдите в раздел [Reanimated babel plugin](glossary.md#reanimated-babel-plugin).

### Шаг 3: Очистить кэш (рекомендуется)

=== "Expo"

    ```shell
    npx expo start -c
    ```

=== "npm"

    ```shell
    npm start -- --reset-cache
    ```

=== "yarn"

    ```shell
    yarn start --reset-cache
    ```

### Настройка под конкретную платформу

#### Android

Никаких дополнительных действий не требуется.

#### iOS

При разработке под iOS перед запуском приложения обязательно установите [pods](https://cocoapods.org/):

```bash
cd ios && pod install && cd ..
```

#### Web

Для создания приложений, ориентированных на веб, с использованием [react-native-web](https://www.npmjs.com/package/react-native-web) мы настоятельно рекомендуем использовать [Expo](https://expo.dev/).

Для использования Reanimated в вебе достаточно установить и добавить [`@babel/plugin-proposal-export-namespace-from`](https://babeljs.io/docs/en/babel-plugin-proposal-export-namespace-from) плагин Babel в ваш `babel.config.js`.

=== "Expo"

    ```shell
    npx expo install @babel/plugin-proposal-export-namespace-from
    ```

=== "npm"

    ```shell
    npm install @babel/plugin-proposal-export-namespace-from
    ```

=== "yarn"

    ```shell
    yarn add @babel/plugin-proposal-export-namespace-from
    ```

```js
module.exports = {
    presets: [
        // ... // don't add it here :)
    ],
    plugins: [
        // ...
        '@babel/plugin-proposal-export-namespace-from',
        'react-native-reanimated/plugin',
    ],
};
```

Убедитесь, что список `react-native-reanimated/plugin` является последним.

## Ссылки

-   [Getting started](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/getting-started/)
