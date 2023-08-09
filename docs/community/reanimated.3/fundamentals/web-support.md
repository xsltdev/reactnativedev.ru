---
description: Запуск Reanimated возможен и в веб-браузере. В этом случае все функциональные возможности реализованы исключительно на JavaScript, поэтому эффективность анимации может быть ниже
---

# Web-поддержка

Запуск Reanimated возможен и в веб-браузере. В этом случае все функциональные возможности реализованы исключительно на JavaScript, поэтому эффективность анимации может быть ниже.

Reanimated для Web требует следующих шагов по настройке. Необходимо добавить [`@babel/plugin-proposal-export-namespace-from`](https://babeljs.io/docs/en/babel-plugin-proposal-export-namespace-from), а также плагин Reanimated Babel в ваш `babel.config.js`.

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

Если вы используете [playground](https://github.com/software-mansion-labs/reanimated-2-playground) и хотите запустить его в браузере, просто введите:

```shell
yarn web
```

Если вы хотите запустить примеры приложений из [reanimated репозитория](https://github.com/software-mansion/react-native-reanimated), то необходимо выполнить следующую команду внутри каталога `Example`:

```shell
yarn start-web
```

## Поддержка Webpack

Если вы хотите использовать Reanimated в приложении на `webpack`, вам необходимо настроить конфигурацию `webpack`.

Пример конфигурационного файла webpack с поддержкой Reanimated:

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');

module.exports = {
    entry: ['babel-polyfill', './index.js'],
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html',
            template: './index.html',
        }),
        new webpack.EnvironmentPlugin({
            JEST_WORKER_ID: null,
        }),
        new webpack.DefinePlugin({ process: { env: {} } }),
    ],
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [
                            '@babel/preset-react',
                            {
                                plugins: [
                                    '@babel/plugin-proposal-class-properties',
                                ],
                            },
                        ],
                    },
                },
            },
        ],
    },
    resolve: {
        alias: { 'react-native$': 'react-native-web' },
        extensions: ['.web.js', '.js'],
    },
};
```

## Web без плагина Babel {#web-without-a-babel-plugin}

В Web можно использовать Reanimated без плагина Babel (`react-native-reanimated/plugin`), с некоторыми дополнительными настройками.

Все хуки Reanimated принимают необязательные массивы зависимостей. Под капотом плагин Reanimated Babel вставляет их за вас.

Чтобы использовать Reanimated без плагина Babel/SWC, необходимо явно передавать массив зависимостей каждый раз, когда вы используете хук Reanimated.

Передача массива зависимостей актуальна как для Web, так и для native. Их добавление не окажет негативного влияния на iOS или Android.

Убедитесь, что следующие хуки имеют массив зависимостей в качестве последнего аргумента:

-   `useDerivedValue`
-   `useAnimatedStyle`
-   `useAnimatedProps`
-   `useAnimatedReaction`

Например:

```ts
const sv = useSharedValue(0);
const dv = useDerivedValue(
    () => sv.value + 1,
    [sv] // dependency array here
);
```

Обязательно передавайте в массив зависимостей саму зависимость (`sv`), а не `sv.value`.

!!!note ""

    Пользователям Babel по-прежнему необходимо установить плагин `@babel/plugin-proposal-class-properties`.

### Поддержка ESLint

Когда вы используете хуки из React, они дают вам приятные предложения от ESLint включить все зависимости. Чтобы добавить эту поддержку в хуки Reanimated, добавьте в конфигурацию ESLint следующее:

```json
{
    "rules": {
        "react-hooks/exhaustive-deps": [
            "error",
            {
                "additionalHooks": "(useAnimatedStyle|useDerivedValue|useAnimatedProps)"
            }
        ]
    }
}
```

При этом предполагается, что у вас уже установлен [плагин](https://www.npmjs.com/package/eslint-plugin-react-hooks) eslint `react-hooks` .

Если вы используете автофикс ESLint, то плагин ESLint может добавить `.value` в массив зависимостей, а не в корневую зависимость. В таких случаях следует обновлять массив самостоятельно.

```tsx
const sv = useSharedValue(0);

// 🚨 bad, sv.value is in the array
const dv = useDerivedValue(() => sv.value, [sv.value]);

// ✅ good, sv is in the array
const dv = useDerivedValue(() => sv.value, [sv]);
```

## Совместимость с Solito / Next.js

В разработке находится экспериментальный плагин SWC. Однако, учитывая, что он может работать некорректно, вы можете воспользоваться инструкцией ["Web без плагина Babel"](#web-without-a-babel-plugin), приведенной выше.

### Next.js Polyfill

Для того чтобы использовать Reanimated с Next.js / Solito, необходимо добавить полифилл `raf` для того, чтобы `requestAnimationFrame` не бросался на сервер:

```sh
yarn add raf
```

Добавьте в верхнюю часть файла `_app.tsx` следующее:

```ts
import 'raf/polyfill';
```

## Ссылки

-   [Web Support](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/web-support/)
