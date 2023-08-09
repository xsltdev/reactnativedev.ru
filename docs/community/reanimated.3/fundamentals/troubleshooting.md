---
description: Устранение распространенных проблем
---

# Устранение распространенных проблем

!!!tip "`TypeError: Cannot convert undefined value to object` on `someVariable._closure`."

    Эта ошибка часто возникает, когда кэш метро не обновляется. Очистите его с помощью:

    ```
    watchman watch-del-all
    yarn start --reset-cache
    ```

    Also, make sure that you installed the babel plugin.

!!!tip "`undefined is not an object (evaluating '_toConsumableArray(Array(length)).map')`"

    Эта ошибка проявляется при использовании spread (`...array`) внутри ворклетов. Более подробная информация о поддержке spread приведена в разделе [Известные проблемы и ограничения](about.md#known-problems-and-limitations).
    В зависимости от того, как используется spread, можно попробовать одну из следующих альтернатив:

    -   копирование массива: [`array.slice()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
    -   `[...Array(length)].map` идиома: `Array(length).fill().map()`
    -   объединение объектов: [`Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
    -   распространение args в функции: [`func.apply()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

!!!tip "`Export namespace should be first transformed by 'babel/plugin-proposal-export-namespace-from'`"

    Эта ошибка обычно возникает, когда вы забыли добавить плагин babel в файл `babel.plugin.js`. Пожалуйста, убедитесь, что вы его добавили.

!!!tip "Multiple versions of Reanimated were detected"

    Эта ошибка обычно возникает, когда в проекте существует более одного экземпляра Reanimated. Она может возникнуть, когда некоторые из ваших зависимостей установили Reanimated внутри своих собственных `node_modules` вместо того, чтобы использовать его как одноранговую зависимость. В этом случае две разные версии JS-модуля Reanimated пытаются установить один и тот же нативный модуль. Эту проблему можно решить вручную, изменив файл `package.json`.

    Проверить, какие библиотеки используют Reanimated, можно, например, с помощью команды:

    ```bash
    npm why react-native-reanimated
    ```

    Если вы используете `yarn`, то необходимо добавить свойство [`resolution`](https://classic.yarnpkg.com/lang/en/docs/selective-version-resolutions/).

    ```json
    "resolutions": {
    	"react-native-reanimated": '<Reanimated version>'
    }
    ```

    Если вы используете `npm`, то необходимо добавить свойство [`overrides`](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#overrides).

    ```json
    "overrides": {
    	"react-native-reanimated": '<Reanimated version>'
    }
    ```

    После этого необходимо снова запустить менеджер пакетов

    ```bash
    yarn
    ```

    или

    ```bash
    npm install
    ```

## Ссылки

-   [Troubleshooting common problems](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/troubleshooting/)
