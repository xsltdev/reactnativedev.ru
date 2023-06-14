# React Native Gradle плагин

Это руководство описывает, как настроить **React Native Gradle Plugin** (часто называемый RNGP) при создании приложения React Native для Android.

## Использование плагина

React Native Gradle Plugin распространяется в виде отдельного пакета NPM, который устанавливается автоматически с `react-native`.

Плагин **уже настроен** для новых проектов, созданных с помощью `npx react-native init`. Вам не нужно делать никаких дополнительных шагов для его установки, если вы создали свое приложение с помощью этой команды.

Если вы интегрируете React Native в существующий проект, обратитесь к [соответствующей странице](integration-with-existing-apps.md#configuring-gradle): она содержит конкретные инструкции по установке плагина.

## Настройка плагина

По умолчанию плагин будет работать **из коробки** с разумными настройками по умолчанию. Вы должны обратиться к этому руководству и настраивать поведение только в том случае, если вам это необходимо.

Для настройки плагина вы можете изменить блок `react` в вашем `android/app/build.gradle`:

```groovy
apply plugin: "com.facebook.react"

/**
 * This is the configuration block to customize your React Native Android app.
 * By default you don't need to apply any configuration, just uncomment the lines you need.
 */
react {
  // Custom configuration goes here.
}
```

Каждый ключ конфигурации описан ниже:

### `root`.

Это корневая папка вашего проекта React Native, т.е. место, где находится файл `package.json`. По умолчанию это `...`. Вы можете настроить его следующим образом:

```groovy
root = file("../")
```

### `reactNativeDir`.

Это папка, в которой находится пакет `react-native`. По умолчанию это `../node_modules/react-native`.

Если вы находитесь в монорепо или используете другой пакетный менеджер, вы можете использовать настройку `reactNativeDir` в соответствии с вашими установками.

Вы можете настроить его следующим образом:

```groovy
reactNativeDir = file("../node_modules/react-native")
```

### `codegenDir`

Это папка, в которой находится пакет `react-native-codegen`. По умолчанию это `../node_modules/react-native-codegen`.

Если вы находитесь в монорепо или используете другой менеджер пакетов, вы можете настроить `codegenDir` в соответствии с вашими настройками.

Вы можете настроить его следующим образом:

```groovy
codegenDir = file("../node_modules/@react-native/codegen")
```

### `cliFile`

Это файл точки входа для React Native CLI. По умолчанию это `../node_modules/react-native/cli.js`.

Файл точки входа необходим, поскольку плагин должен вызывать CLI для компоновки и создания вашего приложения.

Если вы находитесь в монорепо или используете другой менеджер пакетов, вы можете настроить `cliFile` в соответствии с вашими настройками.

Вы можете настроить его следующим образом:

```groovy
cliFile = file("../node_modules/react-native/cli.js")
```

### `debuggableVariants`.

Это список вариантов, которые являются отлаживаемыми.

По умолчанию плагин считает `debuggableVariants` только `debug`, а `release` - нет. Если у вас есть другие

варианты (например, `staging`, `lite` и т.д.), вам нужно будет настроить это соответствующим образом.

Варианты, перечисленные как `debuggableVariants`, не будут поставляться в комплекте, поэтому для их запуска вам понадобится Metro.

Вы можете настроить его следующим образом:

```groovy
debuggableVariants = ["liteDebug", "prodDebug"]
```

### `nodeExecutableAndArgs`.

Это список команды узла и аргументов, которые должны быть вызваны для всех скриптов. По умолчанию это `[node]`, но может быть настроен для добавления дополнительных флагов следующим образом:

```groovy
nodeExecutableAndArgs = ["node"]
```

### `bundleCommand`.

Это имя команды `bundle`, которая будет вызвана при создании пакета для вашего приложения. Это полезно, если вы используете [RAM Bundles](ram-bundles-inline-requires.md). По умолчанию это `bundle`, но может быть настроена для добавления дополнительных флагов следующим образом:

```groovy
bundleCommand = "ram-bundle"
```

### `bundleConfig`.

Это путь к файлу конфигурации, который будет передан в команду `bundle --config <file>`, если он указан. По умолчанию пусто (файл конфигурации не будет передан). Более подробную информацию об объединении конфигурационных файлов можно найти [в документации CLI](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle). Может быть настроен следующим образом:

```groovy
bundleConfig = file(../rn-cli.config.js)
```

### `bundleAssetName`.

Это имя файла пакета, который должен быть сгенерирован. По умолчанию `index.android.bundle`. Может быть настроено следующим образом:

```groovy
bundleAssetName = "MyApplication.android.bundle"
```

### `entryFile`

Входной файл, используемый для генерации пакета. По умолчанию используется поиск `index.android.js` или `index.js`. Может быть настроен следующим образом:

```groovy
entryFile = file("../js/MyApplication.android.js")
```

### `extraPackagerArgs`.

Список дополнительных флагов, которые будут переданы команде `bundle`. Список доступных флагов находится в [документации CLI](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle). По умолчанию пустой. Может быть настроен следующим образом:

```groovy
extraPackagerArgs = []
```

### `hermesCommand`

Путь к команде `hermesc` (компилятор Hermes). React Native поставляется с версией компилятора Hermes в комплекте, поэтому вам обычно не нужно будет настраивать его. По умолчанию плагин будет использовать правильный компилятор для вашей системы.

### `hermesFlags`

Список флагов для передачи в `hermesc`. По умолчанию это `["-O", "-output-source-map"]`. Вы можете настроить его следующим образом

```groovy
hermesFlags = ["-O", "-output-source-map"]
```

## Использование ароматов и вариантов сборки

При создании приложений Android вы можете захотеть использовать [custom flavors](https://developer.android.com/studio/build/build-variants#product-flavors), чтобы иметь различные версии вашего приложения, начиная с одного проекта.

Обратитесь к [официальному руководству по Android](https://developer.android.com/studio/build/build-variants), чтобы настроить пользовательские типы сборки (например, `staging`) или пользовательские вкусы (например, `full`, `lite` и т.д.).

По умолчанию новые приложения создаются с двумя типами сборки (`debug` и `release`) и без пользовательских вкусов.

Комбинация всех типов сборки и всех вкусов создает набор **вариантов сборки**. Например, для типов сборки `debug`/`staging`/`release` и `full`/`lite` у вас будет 6 вариантов сборки: `fullDebug`, `fullStaging`, `fullRelease` и так далее.

Если вы используете пользовательские варианты помимо `debug` и `release`, вам необходимо указать React Native Gradle Plugin, какие из ваших вариантов являются **отлаживаемыми**, используя конфигурацию `debuggableVariants` следующим образом:

```diff
apply plugin: "com.facebook.react"

react {
+ debuggableVariants = ["fullStaging", "fullDebug"]
}
```

Это необходимо, потому что плагин пропустит JS-пакет для всех `debuggableVariants`: для их запуска вам понадобится Metro. Например, если вы перечислите `fullStaging` в `debuggableVariants`, вы не сможете опубликовать его в магазине, так как он будет лишен пакета.

## Что плагин делает под капотом?

React Native Gradle Plugin отвечает за настройку сборки вашего приложения для отправки приложений React Native в продакшн.

Плагин также используется внутри сторонних библиотек для запуска [Codegen](the-new-architecture-pillars-codegen.md), используемого для Новой архитектуры.

Вот краткое описание обязанностей плагина:

-   Добавить задачу `createBundle<Variant>JsAndAssets` для каждого не отлаживаемого варианта, которая отвечает за вызов команд `bundle`, `hermesc` и `compose-source-map`.
-   Установка правильной версии зависимостей `com.facebook.react:react-android` и `com.facebook.react:hermes-android`, чтение версии React Native из `package.json` команды `react-native`.
-   Настройка соответствующих Maven-репозиториев (Maven Central, Google Maven Repo, локальный Maven-репозиторий JSC и т.д.), необходимых для потребления всех необходимых Maven-зависимостей.
-   Настройка NDK для создания приложений, использующих новую архитектуру.
-   Настройка полей `buildConfigFields`, чтобы вы могли знать во время выполнения, включены ли Hermes или Новая архитектура.
-   Настройка порта Metro DevServer в качестве ресурса Android, чтобы приложение знало, через какой порт подключаться.
-   Вызов [React Native Codegen](the-new-architecture-pillars-codegen.md), если библиотека или приложение использует Codegen для новой архитектуры.
