# Код, специфичный для платформы

При создании кроссплатформенного приложения вы захотите повторно использовать как можно больше кода. Однако могут возникнуть ситуации, когда код должен быть разным, например, вы можете захотеть реализовать отдельные визуальные компоненты для Android и iOS.

React Native предоставляет два способа организовать ваш код и разделить его по платформам:

-   Использование модуля `Platform`.
-   Используя расширения файлов для конкретной платформы.

Некоторые компоненты могут иметь свойства, которые работают только на одной платформе. Все эти реквизиты аннотируются `@platform` и имеют небольшой значок рядом с ними на сайте.

## Модуль платформы

React Native предоставляет модуль, который определяет платформу, на которой работает приложение. Вы можете использовать логику обнаружения для реализации кода, специфичного для платформы. Используйте эту опцию, когда только небольшие части компонента зависят от платформы.

<!-- 0001.part.md -->

```ts
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
    height: Platform.OS === 'ios' ? 200 : 100,
});
```

<!-- 0002.part.md -->

`Platform.OS` будет `ios` при работе на iOS и `android` при работе на Android.

Существует также метод `Platform.select`, который, учитывая объект, где ключи могут быть одним из `'ios' | 'android' | 'native' | 'default'`, возвращает наиболее подходящее значение для платформы, на которой вы работаете в данный момент. То есть, если вы работаете на телефоне, предпочтение будет отдано ключам `ios` и `android`. Если они не указаны, будет использован ключ `native`, а затем ключ `default`.

<!-- 0003.part.md -->

```ts
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
    container: {
        flex: 1,
        ...Platform.select({
            ios: {
                backgroundColor: 'red',
            },
            android: {
                backgroundColor: 'green',
            },
            default: {
                // other platforms, web for example
                backgroundColor: 'blue',
            },
        }),
    },
});
```

<!-- 0004.part.md -->

В результате контейнер будет иметь `flex: 1` на всех платформах, красный цвет фона на iOS, зеленый цвет фона на Android и синий цвет фона на других платформах.

Поскольку он принимает `любое` значение, вы также можете использовать его для возврата компонентов, специфичных для конкретной платформы, как показано ниже:

<!-- 0005.part.md -->

```ts
const Component = Platform.select({
    ios: () => require('ComponentIOS'),
    android: () => require('ComponentAndroid'),
})();

<Component />;
```

<!-- 0006.part.md -->

<!-- 0007.part.md -->

```ts
const Component = Platform.select({
    native: () => require('ComponentForNative'),
    default: () => require('ComponentForWeb'),
})();

<Component />;
```

<!-- 0008.part.md -->

### Определение версии Android

В Android модуль `Platform` также может быть использован для определения версии платформы Android, на которой работает приложение:

<!-- 0009.part.md -->

```ts
import { Platform } from 'react-native';

if (Platform.Version === 25) {
    console.log('Running on Nougat!');
}
```

<!-- 0010.part.md -->

**Примечание**: `Version` устанавливается на версию Android API, а не на версию Android OS. Чтобы найти соответствие, пожалуйста, обратитесь к [Истории версий Android](https://ru.wikipedia.org/wiki/%D0%98%D1%81%D1%82%D0%BE%D1%80%D0%B8%D1%8F_%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B9_Android).

### Определение версии iOS

На iOS, `Version` является результатом `-[UIDevice systemVersion]`, который представляет собой строку с текущей версией операционной системы. Примером версии системы является "10.3". Например, чтобы определить номер основной версии на iOS:

<!-- 0011.part.md -->

```ts
import { Platform } from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
    console.log('Work around a change in behavior');
}
```

<!-- 0012.part.md -->

## Расширения для конкретной платформы

Если ваш код, специфичный для конкретной платформы, более сложен, вам следует рассмотреть возможность разделения кода на отдельные файлы. React Native определит, когда файл имеет расширение `.ios.` или `.android.`, и загрузит соответствующий файл платформы, когда это потребуется от других компонентов.

Например, допустим, в вашем проекте есть следующие файлы:

<!-- 0013.part.md -->

```shell
BigButton.ios.js
BigButton.android.js
```

<!-- 0014.part.md -->

Затем вы можете импортировать компонент следующим образом:

<!-- 0015.part.md -->

```ts
import BigButton from './BigButton';
```

<!-- 0016.part.md -->

React Native автоматически подберет нужный файл в зависимости от используемой платформы.

## Расширения, специфичные для Native (т.е. совместное использование кода с NodeJS и Web)

Вы также можете использовать расширение `.native.js`, когда модуль должен быть общим для NodeJS/Web и React Native, но не имеет отличий от Android/iOS. Это особенно полезно для проектов, в которых есть общий код, разделяемый между React Native и ReactJS.

Например, допустим, в вашем проекте есть следующие файлы:

<!-- 0017.part.md -->

```shell
Container.js # picked up by Webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

<!-- 0018.part.md -->

Вы можете импортировать его без расширения `.native` следующим образом:

<!-- 0019.part.md -->

```ts
import Container from './Container';
```

<!-- 0020.part.md -->

**Простой совет:** Настройте ваш Web bundler на игнорирование расширений `.native.js`, чтобы избежать наличия неиспользуемого кода в вашем производственном пакете, тем самым уменьшая конечный размер пакета.

<!-- 0021.part.md -->
