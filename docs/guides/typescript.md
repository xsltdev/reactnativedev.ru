# Использование TypeScript

[TypeScript](https://www.typescriptlang.org/) — это язык, расширяющий JavaScript путем добавления определений типов. Новые проекты React Native по умолчанию используют TypeScript, но также поддерживают JavaScript и Flow.

## Начало работы с TypeScript

Новые проекты, созданные с помощью [React Native CLI](/docs/environment-setup#creating-a-new-application) или популярных шаблонов, таких как [Ignite][ignite], по умолчанию будут использовать TypeScript.

TypeScript также можно использовать с [Expo][expo], который поддерживает шаблоны TypeScript или предложит вам автоматически установить и настроить TypeScript, когда файл `.ts` или `.tsx` будет добавлен в ваш проект.

```shell
npx create-expo-app --template
```

## Добавление TypeScript в существующий проект

**1.** Добавьте TypeScript, типы и плагины ESLint в свой проект.

=== "npm"

    ```shell
    npm install -D @tsconfig/react-native @types/jest @types/react @types/react-test-renderer typescript
    ```

=== "yarn"

    ```shell
    yarn add --dev @tsconfig/react-native @types/jest @types/react @types/react-test-renderer typescript
    ```

---

!!!note ""

    Эта команда добавляет последнюю версию каждой зависимости. Возможно, потребуется изменить версии, чтобы они соответствовали существующим пакетам, используемым в вашем проекте. Вы можете использовать такой инструмент, как [React Native Upgrade Helper](https://react-native-community.github.io/upgrade-helper/), чтобы посмотреть версии, поставляемые React Native.

**2.** Добавьте файл конфигурации TypeScript. Создайте `tsconfig.json` в корне вашего проекта:

```json
{
    "extends": "@tsconfig/react-native/tsconfig.json"
}
```

**3.** Переименуйте файл JavaScript в `*.tsx`.

!!!warning ""

    Вам следует оставить файл точки входа `./index.js` как есть, иначе вы можете столкнуться с проблемой, когда дело дойдет до комплектации производственной сборки.

**4.** Запустите `yarn tsc`, чтобы проверить тип ваших новых файлов TypeScript.

## Использование JavaScript вместо TypeScript

React Native по умолчанию устанавливает в новых приложениях TypeScript, но JavaScript все еще может использоваться. Файлы с расширением `.jsx` рассматриваются как JavaScript, а не TypeScript, и не будут проверяться. Модули JavaScript по-прежнему могут импортироваться модулями TypeScript, равно как и наоборот.

## Как работает TypeScript и React Native

Исходные тексты TypeScript преобразуются [Babel][babel] при компоновке. Мы рекомендуем использовать компилятор TypeScript только для проверки типов. Это поведение `tsc` по умолчанию для вновь создаваемых приложений. Если вы переносите существующий код TypeScript на React Native, есть [одна или две оговорки][babel-7-caveats] в использовании Babel вместо TypeScript.

## Как выглядит React Native + TypeScript

Вы можете предоставить интерфейс для [Props](props) и [State](state) React Component через `React.Component<Props, State>`, который обеспечит проверку типов и автозавершение редактора при работе с этим компонентом в JSX.

```ts title="components/Hello.tsx"
import React from 'react';
import {
    Button,
    StyleSheet,
    Text,
    View,
} from 'react-native';

export type Props = {
    name: string;
    baseEnthusiasmLevel?: number;
};

const Hello: React.FC<Props> = ({
    name,
    baseEnthusiasmLevel = 0,
}) => {
    const [
        enthusiasmLevel,
        setEnthusiasmLevel,
    ] = React.useState(baseEnthusiasmLevel);

    const onIncrement = () =>
        setEnthusiasmLevel(enthusiasmLevel + 1);
    const onDecrement = () =>
        setEnthusiasmLevel(
            enthusiasmLevel > 0 ? enthusiasmLevel - 1 : 0
        );

    const getExclamationMarks = (numChars: number) =>
        numChars > 0 ? Array(numChars + 1).join('!') : '';

    return (
        <View style={styles.container}>
            <Text style={styles.greeting}>
                Hello {name}
                {getExclamationMarks(enthusiasmLevel)}
            </Text>
            <View>
                <Button
                    title="Increase enthusiasm"
                    accessibilityLabel="increment"
                    onPress={onIncrement}
                    color="blue"
                />
                <Button
                    title="Decrease enthusiasm"
                    accessibilityLabel="decrement"
                    onPress={onDecrement}
                    color="red"
                />
            </View>
        </View>
    );
};

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    },
    greeting: {
        fontSize: 20,
        fontWeight: 'bold',
        margin: 16,
    },
});

export default Hello;
```

Вы можете подробнее изучить синтаксис в [TypeScript playground][tsplay].

## Где найти полезные советы

-   [Справочник по TypeScript][ts-handbook]
-   [документация React по TypeScript](https://reactjs.org/docs/static-type-checking.html#typescript).
-   [React + TypeScript Cheatsheets][cheat] содержит хороший обзор того, как использовать React с TypeScript.

## Использование пользовательских псевдонимов путей с помощью TypeScript

Чтобы использовать пользовательские псевдонимы путей с TypeScript, необходимо настроить псевдонимы путей для работы как в Babel, так и в TypeScript. Вот как это сделать:

**1.** Отредактируйте ваш `tsconfig.json`, чтобы иметь ваши [пользовательские сопоставления путей][path-map]. Установите, чтобы все, что находится в корне `src`, было доступно без предшествующей ссылки на путь, и разрешите доступ к любому тестовому файлу с помощью `tests/File.tsx`:

```diff
{
-  "extends": "@tsconfig/react-native/tsconfig.json"
+  "extends": "@tsconfig/react-native/tsconfig.json",
+  "compilerOptions": {
+    "baseUrl": ".",
+    "paths": {
+      "*": ["src/*"],
+      "tests": ["tests/*"],
+      "@components/*": ["src/components/*"],
+    },
+  }
}
```

**2.** Добавьте [`babel-plugin-module-resolver`][bpmr] в качестве пакета разработки в свой проект:

=== "npm"

    ```shell
    npm install --save-dev babel-plugin-module-resolver
    ```

=== "yarn"

    ```shell
    yarn add --dev babel-plugin-module-resolver
    ```

---

**3.** Наконец, настройте ваш `babel.config.js` (обратите внимание, что синтаксис вашего `babel.config.js` отличается от вашего `tsconfig.json`):

```diff
{
   presets: ['module:metro-react-native-babel-preset'],
+  plugins: [
+    [
+       'module-resolver',
+       {
+         root: ['./src'],
+         extensions: ['.ios.js', '.android.js', '.js', '.ts', '.tsx', '.json'],
+         alias: {
+           tests: ['./tests/'],
+           "@components": "./src/components",
+         }
+       }
+    ]
+  ]
}
```

[ts-template]: https://github.com/react-native-community/react-native-template-typescript
[babel]: /docs/javascript-environment#javascript-syntax-transformers
[babel-7-caveats]: https://babeljs.io/docs/en/next/babel-plugin-transform-typescript
[cheat]: https://github.com/typescript-cheatsheets/react-typescript-cheatsheet#reacttypescript-cheatsheets
[ts-handbook]: https://www.typescriptlang.org/docs/handbook/intro.html
[path-map]: https://www.typescriptlang.org/docs/handbook/module-resolution.html#path-mapping
[bpmr]: https://github.com/tleunen/babel-plugin-module-resolver
[expo]: https://expo.io
[ignite]: https://github.com/infinitered/ignite
[tsplay]: https://www.typescriptlang.org/play?strictNullChecks=false&jsx=3#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wG4BYAKFEljgG8AhAVxhggDsAaOAZRgCeAGyS8AFkiQweAFSQAPaXABqwJAHcAvnGy4CRdDAC0HFDGAA3JGSpUFteILBI4ABRxgAznAC8DKnBwpiBIAFxwnjBQwBwA5hSUgQBGKJ5IAKIcMGLMnsCpIAAySFZCAPzhHMwgSUhQCZq2lGickXAAEkhCQhDhyIYAdABiAMIAPO4QXgB8vnAAFPRBKCE8KWmZ2bn5nkUlXXMADHCaAJS+s-QBcC0cbQDaSFk5eQXFpTxpMJsvO3ulAF05v0MANcqIYGYkPN1hlnts3vshKcEtdbm1OABJDhoIghLJzebnHyzL4-BG7d5deZPLavSlIuAAajgAEYUWjWvBOAARJC4pD4+B+IkXCJScn0-7U2m-RGlOCzY5lOCyinSoRwIxsuDhQ4cyicu7wWIS+RoIQrMzATgAWRQUAA1t4RVUQCMxA7PJVqrUoMTZm6PV7FXBlXAAIJQKAoATzIOeqDeFnsgYAKwgMXm+AAhPhzuF8DZDYk4EQYMwoBwFtdAmNVBoIoIRD56JFhEhPANbpCYnVNNNa4E4GM5Iomx3W+2RF3YkQpDFYgOh8OOl0evR8ARGqXV4F6MEkDu98P6KbvubLSBrXaHc6afCpVTkce92MAPRjmCD3fD+tqdQfxPOsWDYTgVz3cwYBbAAibEBVSFw1SlGCINXdA0E7PIkmAIRgEEQoUFqIQfBgmIBSFVDfxPTh3Cw1ssRxPFaVfYCbggHooFIpIhGYJAqLY98gOAsZQPYDg0OHKDYL5BC0lVR8-gEti4AwrDgBwvCCKIrpSIAE35ZismUtjaKITxPAYjhZKMmBWOAlpONIog9JMvchIgj8G0AocvIA4SDU0VFmi5CcZzmfgO3ESQYG7AwYGhK5Sx7FA+ygcIktXTARHkcJWS4IcUDw2IOExBKQG9OAYMwrI6hggrfzTXJzEwAQRk4BKsnCaraTq65NAawI5xixcMqHTAOt4YAAC8wjgAAmQ5BuHCasgAdSQYBYjEGBCySDi9PwZbAmvKBYhiPKADZloGqgzmC+xoHgAzMBQZghHgTpuggBIgA
