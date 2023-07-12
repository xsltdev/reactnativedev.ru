# Codegen

!!!warning "Внимание"

    Эта документация все еще является **экспериментальной**, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в [обсуждении внутри рабочей группы](https://github.com/reactwg/react-native-new-architecture/discussions/8) для этой страницы.

    Более того, она содержит несколько **ручных шагов**. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

**Codegen** — это не совсем столп, но это инструмент, который можно использовать, чтобы избежать написания большого количества повторяющегося кода. Использование **Codegen** не является обязательным: весь код, который генерируется с его помощью, можно написать и вручную. Однако он генерирует код, который может сэкономить вам много времени.

**Codegen** автоматически вызывается React Native каждый раз, когда создается приложение для iOS или Android. Иногда вы захотите запустить скрипты, генерирующие код, вручную, чтобы узнать, какие типы и файлы действительно генерируются: это распространенный сценарий при разработке [**Turbo Native Modules**](the-new-architecture-pillars-turbomodules.md) и [**Fabric Native Components**](the-new-architecture-pillars-fabric-components.md), например.

В этом руководстве рассказывается, как настроить **Codegen**, как вызвать его вручную для каждой платформы, а также описывается сгенерированный код.

## Предварительные условия

Для правильной генерации кода всегда требуется приложение React Native, даже если вызывать **Codegen** вручную.

Процесс **Codegen** тесно связан со сборкой приложения, а скрипты находятся в NPM-пакете `react-native`.

В рамках данного руководства создайте проект с помощью React Native CLI следующим образом:

```sh
npx react-native init SampleApp --version 0.70.0
```

!!!note ""

    Это руководство предполагает, что используемая версия React Native — 0.70.0 или выше.

    Предыдущие версии React Native используют версию **Codegen**, которая требует несколько иной настройки.

Затем добавьте модуль, требующий **Codegen**, в качестве NPM-зависимости приложения:

```sh
yarn add <path/to/your/TurboNativeModule_or_FabricNativeComponent>
```

Смотрите, как создать [Turbo Native Module](the-new-architecture-pillars-turbomodules.md) или [Fabric Native Component](the-new-architecture-pillars-fabric-components.md), чтобы получить больше информации о том, как их настроить.

Остальная часть этого руководства предполагает, что у вас правильно настроены `Turbo Native Module` и/или `Fabric Native Component`.

## iOS

### Запуск Codegen

**Codegen** для iOS полагается на некоторые скрипты Node, которые вызываются в процессе сборки. Скрипты находятся в папке `MyApp/node_modules/react-native/scripts/`.

Сценарий, который вам необходимо запустить, — это сценарий `generate-codegen-artifacts.js`. Он выполняет поиск среди всех зависимостей приложения, ищет JS-файлы, которые соответствуют некоторым определенным соглашениям (подробности смотрите в разделах [TurboModules](the-new-architecture-pillars-turbomodules.md) и [Fabric Components](the-new-architecture-pillars-fabric-components.md)), и генерирует необходимый код.

Чтобы вызвать скрипт, вы можете выполнить эту команду из корневой папки вашего приложения:

```sh
node node_modules/react-native/scripts/generate-codegen-artifacts.js \
    --path SampleApp/ \
    --outputPath <an/output/path> \
```

Учитывая, что приложение имеет `Turbo Native Modules` и/или `Fabric Native Components`, настроенные как зависимость, **Codegen** ищет их все и генерирует код по указанному вами пути.

### Сгенерированный код

Если вы запустите **Codegen** в вашем приложении с выходным путем `codegen`, например, вы получите следующую структуру:

```title="iOS Codegen output"
codegen
└── build
    └── generated
        └── ios
            ├── MyTurboModuleSpecs
            │   ├── MyTurboModuleSpecs-generated.mm
            │   └── MyTurboModuleSpecs.h
            ├── FBReactNativeSpec
            │   ├── FBReactNativeSpec-generated.mm
            │   └── FBReactNativeSpec.h
            ├── RCTThirdPartyFabricComponentsProvider.h
            ├── RCTThirdPartyFabricComponentsProvider.mm
            └── react
                └── renderer
                    └── components
                        ├── MyFabricComponent
                        │   ├── ComponentDescriptors.h
                        │   ├── EventEmitters.cpp
                        │   ├── EventEmitters.h
                        │   ├── Props.cpp
                        │   ├── Props.h
                        │   ├── RCTComponentViewHelpers.h
                        │   ├── ShadowNodes.cpp
                        │   └── ShadowNodes.h
                        └── rncore
                            ├── ComponentDescriptors.h
                            ├── EventEmitters.cpp
                            ├── EventEmitters.h
                            ├── Props.cpp
                            ├── Props.h
                            ├── RCTComponentViewHelpers.h
                            ├── ShadowNodes.cpp
                            └── ShadowNodes.h
```

Папка `codegen`, как и ожидалось, находится в корне иерархии. В нее вложены еще две папки: `build/generated`.

Затем, есть папка `ios`, которая содержит:

-   Собственную папку для каждого ТурбоМодуля.
-   Файлы заголовка (`.h`) и реализации (`.mm`) для `RCTThirdPartyFabricComponentsProvider`.
-   Базовая папка `react/renderer/components`, которая содержит пользовательскую папку для каждого `Fabric Native Component`.

В приведенном выше примере есть как TurboModule, так и набор Fabric Native Components. Они генерируются самим React Native: `FBReactNativeSpec` и `rncore`. Эти модули будут появляться всегда, даже если у вас нет дополнительного TurboModule или Fabric Native Component: React Native требует их для правильной работы.

#### Turbo Native Modules

Каждая папка содержит два файла: файл интерфейса и файл реализации.

Файлы интерфейса имеют то же имя, что и имя модуля Turbo Native, и содержат методы для инициализации интерфейса JSI.

Файлы реализации вместо этого имеют суффикс `-generated` и содержат логику для вызова нативных методов из JS и наоборот.

#### Fabric Native Components

Содержимое каждой папки Fabric Native Component содержит несколько файлов. Основным элементом для Fabric Native Component является `ShadowNode`: он представляет собой узел в абстрактном дереве React. Узел `ShadowNode` представляет собой сущность React, поэтому ему могут понадобиться некоторые реквизиты, которые определяются в файлах `Props` и, иногда, `EventEmitter`, определенный в соответствующем файле.

Кроме того, **Codegen** также создает файлы `ComponentDescriptor.h` и `RCTComponentViewHelpers.h`: первый используется React Native и Fabric для правильного получения ссылки на нативный компонент Fabric, а второй содержит некоторые вспомогательные методы и протоколы, которые могут быть реализованы нативным представлением для правильного ответа на вызовы JSI.

Для получения более подробной информации о работе Fabric ознакомьтесь с разделом [Renderer](../architecture/fabric-renderer.md).

#### RCTThirdPartyFabricComponentsProvider

Это файлы интерфейса и реализации для реестра. React Native использует этот реестр во время выполнения, чтобы получить нужный класс для требуемого Fabric Native Component. Как только у React Native есть хэндл к этому классу, он может его инстанцировать.

## Android

### Запуск Codegen

Android `Codegen` полагается на задачу Gradle для генерации необходимого кода. Сначала необходимо настроить приложение Android для работы с Новой архитектурой; в противном случае задача Gradle не выполнится.

1.  Откройте файл `MyApp/android/gradle.properties`.
2.  Переключите флаг `newArchEnabled` с `false` на `true`.

После этого вы можете перейти в папку `SampleApp/android` и запустить:

```sh
./gradlew generateCodegenArtifactsFromSchema
```

Эти задачи вызывают `generateCodegenArtifactsFromSchema` на всех импортированных проектах приложения (приложение и все модули node, которые связаны с ним). Он генерирует код в соответствующей папке `node_modules/<dependency>`. Так, например, если у вас есть Fabric Native Component, нодовый модуль которого называется `my-fabric-component`, сгенерированный код будет расположен по пути `SampleApp/node_modules/my-fabric-component/android/build/generated/source/codegen`.

### Сгенерированный код

После завершения задачи Gradle вы можете увидеть различные структуры для Turbo Native Module или Fabric Native Component. На следующей вкладке показано, как они выглядят:

=== "Turbo Modules"

    ```sh
    codegen
    ├── java
    │   └── com
    │       └── MyTurbomodule
    │           └── MyTurbomodule.java
    ├── jni
    │   ├── Android.mk
    │   ├── CMakeLists.txt
    │   ├── MyTurbomodule-generated.cpp
    │   ├── MyTurbomodule.h
    │   └── react
    │       └── renderer
    │           └── components
    │               └── MyTurbomodule
    │                   ├── ComponentDescriptors.h
    │                   ├── EventEmitters.cpp
    │                   ├── EventEmitters.h
    │                   ├── Props.cpp
    │                   ├── Props.h
    │                   ├── ShadowNodes.cpp
    │                   └── ShadowNodes.h
    └── schema.json
    ```

=== "Fabric Components"

    ```sh
    codegen
    ├── java
    │   └── com
    │       └── facebook
    │           └── react
    │               └── viewmanagers
    │                   ├── MyFabricComponentManagerDelegate.java
    │                   └── MyFabricComponentManagerInterface.java
    ├── jni
    │   ├── Android.mk
    │   ├── CMakeLists.txt
    │   ├── MyFabricComponent-generated.cpp
    │   ├── MyFabricComponent.h
    │   └── react
    │       └── renderer
    │           └── components
    │               └── MyFabricComponent
    │                   ├── ComponentDescriptors.h
    │                   ├── EventEmitters.cpp
    │                   ├── EventEmitters.h
    │                   ├── Props.cpp
    │                   ├── Props.h
    │                   ├── ShadowNodes.cpp
    │                   └── ShadowNodes.h
    └── schema.json
    ```

Java не может беспрепятственно взаимодействовать с C++, как это делает Objective-C++. Для правильной работы **Codegen** создает некоторые мостики между миром Java и C++ в папке `jni`, где определены нативные интерфейсы Java.

Обратите внимание, что и Turbo Native Modules, и Fabric Native Components поставляются с двумя дескрипторами файлов сборки: `Android.mk` и `CMakeLists.txt`. Они используются приложением Android для фактической сборки внешних модулей.

#### Turbo Native Module

**Codegen** генерирует абстрактный класс Java в пакете `java` с тем же именем, что и у TurboModule. Этот абстрактный класс должен быть реализован реализацией JNI C++.

Затем генерируются файлы C++ в папке `jni`. Они следуют той же конвенции iOS: есть интерфейс под названием `MyTurbomodule.h` и файл реализации под названием `MyTurbomodule-generated.cpp`. Первый — это интерфейс, который позволяет React Native инициализировать интерфейс JSI для TurboModule. Второй — это файл реализации, который содержит логику для вызова нативного метода из JS и наоборот.

#### Fabric Native Component

**Кодеген** для нативного компонента Fabric содержит `MyFabricComponentManagerInterface.java` и `MyFabricComponentManagerDelegate.java` в пакете `java`. Они реализуются и используются собственным `MyFabricComponentManager`, необходимым для правильной загрузки компонента во время выполнения (Подробнее см. руководство по созданию [Fabric Native Component](the-new-architecture-pillars-fabric-components.md)).

Затем идет слой файлов JNI C++, которые используются Fabric для рендеринга компонентов. Основным элементом компонента Fabric является `ShadowNode`: он представляет собой узел в абстрактном дереве React. Узел `ShadowNode` представляет собой сущность React, поэтому ему могут понадобиться некоторые реквизиты, которые определяются в файлах `Props` и, иногда, `EventEmitter`, определенный в соответствующем файле.

**Codegen** также создает `ComponentDescriptor.h`, который необходим для получения правильного управления нативным компонентом Fabric.
