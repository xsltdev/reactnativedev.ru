# Приложение

!!!warning "Внимание"

    Эта документация все еще является экспериментальной, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в обсуждении внутри рабочей группы для этой страницы.

    Более того, она содержит несколько ручных шагов. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

## I. Терминология

Во всех руководствах, связанных с новой архитектурой, мы будем придерживаться следующей **терминологии**:

-   **Legacy Native Components** - Для обозначения компонентов, работающих на старой архитектуре React Native.
-   **Legacy Native Modules** - Модули, работающие на старой архитектуре React Native.
-   **Fabric Native Components** - Компоненты, которые были адаптированы для работы с новой архитектурой, а именно с новым рендерером. Для краткости вы можете называть их **Fabric Components**.
-   **Нативные модули Turbo** - Модули, которые были адаптированы для работы с новой архитектурой, а именно с новой системой нативных модулей. Для краткости их можно назвать **Модули Turbo**.

## II. Сопоставление типов потоков и нативных типов

Вы можете использовать следующую таблицу в качестве справочника о том, какие типы поддерживаются и чему они соответствуют в каждой платформе:

### `string`

| Nullable Support? | Android (Java) | iOS        |
| ----------------- | -------------- | ---------- |
| `?string`         | `String`       | `NSString` |

### `boolean`

| Nullable Support? | Android (Java) | iOS        |
| ----------------- | -------------- | ---------- |
| `?boolean`        | `Boolean`      | `NSNumber` |

### Объектный литерал

Это рекомендуется вместо использования простого `Object`, для безопасности типов.

**Пример:** `{| foo: string, ... |}`

| Nullable Support?                             | Android (Java) | iOS |
| --------------------------------------------- | -------------- | --- |
| <code>?{&#124; foo: string, ...&#124;}</code> | -              | -   |

### `Объект`

!!!warning ""

    Рекомендуется вместо этого использовать `Object literal`.

| Nullable Support? | Android (Java) | iOS                        |
| ----------------- | -------------- | -------------------------- |
| `?Object`         | `ReadableMap`  | `@{}` (untyped dictionary) |

### `Array<*>`

| Nullable Support? | Android (Java)  | iOS                                                            |
| ----------------- | --------------- | -------------------------------------------------------------- |
| `?Array<*>`       | `ReadableArray` | `NSArray` (or `RCTConvertVecToArray` when used inside objects) |

### `Function`

| Nullable Support? | Android (Java) | iOS |
| ----------------- | -------------- | --- |
| `?Function`       | -              | -   |

### `Promise<*>`

| Nullable Support? | Android (Java)                      | iOS                                             |
| ----------------- | ----------------------------------- | ----------------------------------------------- |
| `?Promise<*>`     | `com.facebook.react.bridge.Promise` | `RCTPromiseResolve` and `RCTPromiseRejectBlock` |

### Союзы типов

Союзы типов поддерживаются только в качестве обратных вызовов.

**Пример:** `'SUCCESS' | 'FAIL'`.

| Nullable Support?  | Android (Java) | iOS |
| ------------------ | -------------- | --- |
| Only as callbacks. | -              | -   |

### Обратные вызовы

Функции обратного вызова не проверяются на тип и обобщаются как `Object`.

**Пример:** `() =>`.

| Nullable Support? | Android (Java)                       | iOS                      |
| ----------------- | ------------------------------------ | ------------------------ |
| Yes               | `com.facebook.react.bridge.Callback` | `RCTResponseSenderBlock` |

!!!note ""

    Вам также может быть полезно обратиться к спецификациям JavaScript для основных модулей React Native. Они находятся в директории `Libraries/` в репозитории React Native.

## III. Сопоставление типов TypeScript и Native

Вы можете использовать следующую таблицу в качестве справочника о том, какие типы поддерживаются и чему они соответствуют в каждой платформе:

| TypeScript Type                                | Nullable Support?                                        | Android (Java)                       | iOS                                                            | Note                                                                           |
| ---------------------------------------------- | -------------------------------------------------------- | ------------------------------------ | -------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| `string`                                       | <code>string &#124; null </code>                         | `String`                             | `NSString`                                                     |                                                                                |
| `boolean`                                      | <code>boolean &#124; null </code>                        | `Boolean`                            | `NSNumber`                                                     |                                                                                |
| `number`                                       | No                                                       | `double`                             | `NSNumber`                                                     |                                                                                |
| <code>{&#124; foo: string, ... &#124;}</code>  | <code>{&#124; foo: string, ...&#124;} &#124; null</code> |                                      |                                                                | Object literal. This is recommended over simply using Object, for type safety. |
| `Object`                                       | <code>Object &#124; null </code>                         | `ReadableMap`                        | `@{} (untyped dictionary)`                                     | Recommended to use object literal (see above).                                 |
| `Array<*>`                                     | <code>Array<\*> &#124; null </code>                      | `ReadableArray`                      | `NSArray` (or `RCTConvertVecToArray` when used inside objects) |                                                                                |
| `Function`                                     | <code>Function &#124; null </code>                       |                                      |                                                                |                                                                                |
| `Promise<*>`                                   | <code>Promise<\*> &#124; null </code>                    | `com.facebook.react.bridge.Promise`  | `RCTPromiseResolve` and `RCTPromiseRejectBlock`                |                                                                                |
| Type aliases of the above                      | Yes                                                      |                                      |                                                                |                                                                                |
| Type Unions <code>'SUCCESS'&#124;'FAIL'</code> | Only as callbacks.                                       |                                      |                                                                | Type unions only supported as callbacks.                                       |
| Callbacks: `( ) =>`                            | Yes                                                      | `com.facebook.react.bridge.Callback` | `RCTResponseSenderBlock`                                       | Callback functions are not type checked, and are generalized as Objects.       |

Вам также может быть полезно обратиться к спецификациям JavaScript для основных модулей React Native. Они находятся в директории `Libraries/` в репозитории React Native.

## IV. Вызов code-gen во время разработки

!!!note ""

    Этот раздел содержит информацию, специфичную для v0.66 React Native.

Обычно кодоген вызывается во время сборки, но вы можете посчитать полезным генерировать код нативного интерфейса по требованию для устранения неполадок.

Если вы хотите вызвать codegen вручную, у вас есть два варианта:

1.  Вызов задачи Gradle напрямую (Android).
2.  Вызов скрипта вручную.

### Вызов задачи Gradle напрямую

Вы можете запустить Codegen, вызвав следующую задачу:

```bash
./gradlew generateCodegenArtifactsFromSchema --rerun-tasks
```

Дополнительный флаг `--rerun-tasks` добавлен, чтобы убедиться, что Gradle игнорирует проверки `UP-TO-DATE` для этой задачи. Во время обычной разработки он не нужен.

Задача `generateCodegenArtifactsFromSchema` обычно запускается перед задачей `preBuild`, поэтому вам не нужно вызывать ее вручную, но она будет запущена перед вашими сборками.

### Вызов скрипта вручную

В качестве альтернативы вы можете вызвать Codegen напрямую, минуя Gradle Plugin или инфраструктуру CocoaPods.

Это можно сделать с помощью следующих команд.

Параметры, которые нужно будет указать, покажутся вам вполне знакомыми, если вы уже настраивали плагин Gradle или библиотеку CocoaPods.

#### Генерация файла схемы

Сначала вам нужно будет сгенерировать файл схемы из ваших источников JavaScript. Это нужно будет делать только в том случае, если ваши спецификации JavaScript изменятся. Сценарий для генерации схемы предоставляется как часть пакета `react-native-codegen`. Если вы запускаете его из своего приложения React Native, вы можете использовать пакет из `node_modules` напрямую:

```bash
node node_modules/react-native-codegen/lib/cli/combine/combine-js-to-schema-cli.js \
  <output_file_schema_json> <javascript_sources_dir>
```

!!!note ""

    Исходный код для `react-native-codegen` доступен в репозитории React Native в каталоге `packages/react-native-codegen`. Запустите `yarn install` и `yarn build` в этом каталоге, чтобы собрать свой собственный пакет `react-native-codegen` из исходников. В большинстве случаев вы не захотите этого делать, поскольку руководство предполагает использование версии пакета `react-native-codegen`, связанной с соответствующим ночным релизом React Native.

#### Генерация артефактов нативного кода

Как только у вас есть файл схемы для ваших нативных модулей или компонентов, вы можете использовать второй скрипт для генерации фактических артефактов нативного кода для вашей библиотеки. Вы можете использовать тот же файл схемы, сгенерированный предыдущим скриптом.

```bash
node node_modules/react-native/scripts/generate-specs-cli.js \
  --platform <ios|android> \
  --schemaPath <generated_schema_json_file> \
  --outputDir <output_dir> \
  [--libraryName library_name] \
  [--javaPackageName java_package_name] \
  [--libraryType all(default)|modules|components]
```

!!!note ""

    Выходные артефакты Codegen находятся внутри папки сборки и не должны быть зафиксированы. Их следует рассматривать только в качестве справочной информации.

##### Пример

Ниже приведен базовый пример вызова сценария Codegen для генерации кода нативного интерфейса iOS для библиотеки, предоставляющей нативные модули. Исходные тексты JavaScript spec для этой библиотеки расположены в подкаталоге `js/`, а нативный код этой библиотеки ожидает, что нативные интерфейсы будут доступны в подкаталоге `ios`.

```bash
# Generate schema - only needs to be done whenever JS specs change
node node_modules/react-native-codegen/lib/cli/combine/combine-js-to-schema-cli.js /tmp/schema.json ./js

# Generate native code artifacts
node node_modules/react-native/scripts/generate-specs-cli.js \
  --platform ios \
  --schemaPath /tmp/schema.json \
  --outputDir ./ios \
  --libraryName MyLibSpecs \
  --libraryType modules
```

В приведенном выше примере сценарий code-gen сгенерирует несколько файлов: `MyLibSpecs.h` и `MyLibSpecs-generated.mm`, а также несколько файлов `.h` и `.cpp`, расположенных в директории `ios`.

## V. Замечание по существующим приложениям

Это руководство содержит инструкции по миграции приложения, основанного на шаблоне приложения по умолчанию, предоставляемом React Native. Если ваше приложение отклонилось от шаблона, или вы работаете с приложением, которое никогда не было основано на шаблоне, то вам могут помочь следующие разделы.

### Поиск делегата моста

В этом руководстве предполагается, что `AppDelegate` настроен как делегат моста. Если вы не уверены, какой делегат является делегатом моста, то поставьте точку останова в `RCTBridge` и `RCTCxxBridge`, запустите ваше приложение и проверьте `self.delegate`.
