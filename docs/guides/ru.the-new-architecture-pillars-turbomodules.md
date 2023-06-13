

---
id: pillars-turbomodules
title: Turbo Native Modules
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

import constants from '@site/core/TabsConstants';

import NewArchitectureWarning from '../\_markdown-new-architecture-warning.mdx';

<NewArchitectureWarning/>

Если вы работали с React Native, вы можете быть знакомы с концепцией [Native Modules](../native-modules-intro.md), которая позволяет JavaScript и платформенно-нативному коду общаться через "мост" React Native, который обрабатывает межплатформенную сериализацию через JSON.



Turbo Native Modules - это следующая итерация Native Modules, которая обеспечивает несколько дополнительных [преимуществ](./why):




- Сильно типизированные интерфейсы, согласованные между платформами.

- Возможность писать код исключительно на C++ или интегрировать его с другим родным языком платформы, уменьшая необходимость дублирования реализаций на разных платформах

- Ленивая загрузка модулей, позволяющая ускорить запуск приложения

- Использование JSI, интерфейса JavaScript для нативного кода, позволяет более эффективно взаимодействовать между нативным и JavaScript-кодом, чем мост.




Это руководство покажет вам, как создать базовый Turbo Native модуль, совместимый с последней версией React Native.




:::осторожно

Модули Turbo Native работают только с включенной **Новой архитектурой**.

Чтобы перейти на **Новую архитектуру**, следуйте [Руководству по миграции](../new-architecture-intro)

:::




## Как создать модуль Turbo Native




Чтобы создать модуль Turbo Native, нам необходимо:




1. Определить спецификацию JavaScript.

2. Настроить модуль так, чтобы Codegen мог генерировать строительные леса.

3. Напишите нативный код для завершения реализации модуля.




## 1. Настройка папки




Для того чтобы модуль не был связан с приложением, хорошей идеей будет определить модуль отдельно от приложения, а затем добавить его в качестве зависимости в ваше приложение. Так же вы поступите при написании Turbo Native Modules, которые позже можно будет выпустить в виде библиотек с открытым исходным кодом.




Рядом с вашим приложением создайте папку `RTNCalculator`. **RTN** означает "**R**eac**t** **N**ative" и является рекомендуемым префиксом для модулей React Native.




Внутри `RTNCalculator` создайте три подпапки: `js`, `ios` и `android`.




Конечный результат должен выглядеть следующим образом:

```sh
TurboModulesGuide
├── MyApp
└── RTNCalculator
    ├── android
    ├── ios
    └── js
```

## 2. Спецификация JavaScript



Для **Новой архитектуры** требуются интерфейсы, специфицированные в типизированном диалекте JavaScript (либо [Flow](https://flow.org/), либо [TypeScript](https://www.typescriptlang.org/)). **Codegen** будет использовать эти спецификации для генерации кода на сильно типизированных языках, включая C++, Objective-C++ и Java.




Файл, содержащий эту спецификацию, должен удовлетворять двум требованиям:




1. Файл **должен** быть назван `Native<MODULE_NAME>`, с расширением `.js` или `.jsx` при использовании Flow, или `.ts`, или `.tsx` при использовании TypeScript. Codegen будет искать только файлы, соответствующие этому шаблону.

2. Файл должен экспортировать объект `TurboModuleRegistrySpec`.

<Tabs groupId="turbomodule-specs" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="flow">

```typescript title="NativeCalculator.js"
// @flow
import type {TurboModule} from 'react-native/Libraries/TurboModule/RCTExport';
import {TurboModuleRegistry} from 'react-native';

export interface Spec extends TurboModule {
  add(a: number, b: number): Promise<number>;
}
export default (TurboModuleRegistry.get<Spec>(
  'RTNCalculator'
): ?Spec);
```

</TabItem>
<TabItem value="typescript">

```typescript title="NativeCalculator.ts"
import type {TurboModule} from 'react-native/Libraries/TurboModule/RCTExport';
import {TurboModuleRegistry} from 'react-native';

export interface Spec extends TurboModule {
  add(a: number, b: number): Promise<number>;
}

export default TurboModuleRegistry.get<Spec>(
  'RTNCalculator',
) as Spec | null;
```

</TabItem>
</Tabs>

В начале файлов спецификаций находятся импорты:



- Тип `TurboModule`, который определяет базовый интерфейс для всех Turbo Native Modules

- JavaScript-модуль `TurboModuleRegistry`, который содержит функции для загрузки Turbo Native Modules.




Вторая секция файла содержит спецификацию интерфейса для модуля Turbo Native Module. В данном случае интерфейс определяет функцию `add`, которая принимает два числа и возвращает обещание, которое разрешается в число. Этот тип интерфейса **должен** иметь имя `Spec` для Turbo Native Module.




Наконец, мы вызываем `TurboModuleRegistry.get`, передавая имя модуля, который загрузит Turbo Native Module, если он доступен.




:::предостережение

Мы пишем файлы JavaScript, импортирующие типы из библиотек, без установки соответствующего модуля node и установки его зависимостей. Ваша IDE не сможет разрешить утверждения импорта, и вы можете увидеть ошибки и предупреждения. Это ожидаемо и не вызовет проблем, когда вы добавите модуль в свое приложение.

:::




## 3. Конфигурация модуля




Далее необходимо добавить некоторые конфигурации для [**Codegen**](pillars-codegen.md) и автоссылки.




Некоторые файлы конфигурации являются общими для iOS и Android, в то время как другие зависят от конкретной платформы.




### Shared




Общая конфигурация - это файл `package.json`, используемый yarn при установке вашего модуля. Создайте файл `package.json` в корне каталога `RTNCalculator`.

```json title="package.json"
{
  "name": "rtn-calculator",
  "version": "0.0.1",
  "description": "Add numbers with Turbo Native Modules",
  "react-native": "js/index",
  "source": "js/index",
  "files": [
    "js",
    "android",
    "ios",
    "rtn-calculator.podspec",
    "!android/build",
    "!ios/build",
    "!**/__tests__",
    "!**/__fixtures__",
    "!**/__mocks__"
  ],
  "keywords": ["react-native", "ios", "android"],
  "repository": "https://github.com/<your_github_handle>/rtn-calculator",
  "author": "<Your Name> <your_email@your_provider.com> (https://github.com/<your_github_handle>)",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/<your_github_handle>/rtn-calculator/issues"
  },
  "homepage": "https://github.com/<your_github_handle>/rtn-calculator#readme",
  "devDependencies": {},
  "peerDependencies": {
    "react": "*",
    "react-native": "*"
  },
  "codegenConfig": {
    "name": "RTNCalculatorSpec",
    "type": "modules",
    "jsSrcsDir": "js",
    "android": {
      "javaPackageName": "com.rtncalculator"
    }
  }
}
```

Верхняя часть файла содержит некоторую описательную информацию, такую как название компонента, его версия и исходные файлы. Обязательно обновите различные заполнители, которые обернуты в `<>`: замените все вхождения маркеров `<ваш_github_handle>`, `<ваше имя>` и `<your_email@your_provider.com>`.



Затем есть зависимости для этого пакета. Для этого руководства вам понадобятся `react` и `react-native`.




Наконец, конфигурация **Codegen** задается полем `codegenConfig`. Оно содержит массив библиотек, каждая из которых определяется тремя другими полями:




- `name`: Имя библиотеки. По соглашению, вы должны добавить суффикс `Spec`.

- `type`: Тип модуля, содержащегося в этом пакете. В данном случае это Turbo Native Module; поэтому следует использовать значение `modules`.

- `jsSrcsDir`: относительный путь для доступа к спецификации `js`, которая анализируется **Codegen**.

- `android.javaPackageName`: пакет, который будет использоваться в Java-файлах, генерируемых **Codegen**.




### iOS: Создание файла `podspec`.




Для iOS вам нужно будет создать файл `rtn-calculator.podspec`, который определит модуль как зависимость для вашего приложения. Он будет находиться в корне `RTNCalculator`, рядом с папкой `ios`.




Файл будет выглядеть следующим образом:

```ruby title="rtn-calculator.podspec"
require "json"

package = JSON.parse(File.read(File.join(__dir__, "package.json")))

Pod::Spec.new do |s|
  s.name            = "rtn-calculator"
  s.version         = package["version"]
  s.summary         = package["description"]
  s.description     = package["description"]
  s.homepage        = package["homepage"]
  s.license         = package["license"]
  s.platforms       = { :ios => "11.0" }
  s.author          = package["author"]
  s.source          = { :git => package["repository"], :tag => "#{s.version}" }

  s.source_files    = "ios/**/*.{h,m,mm,swift}"

  install_modules_dependencies(s)
end
```

Файл `.podspec` должен быть дочерним по отношению к файлу `package.json`, а его имя - то, которое мы задали в свойстве `name` файла `package.json`: `rtn-calculator`.



В первой части файла подготавливаются некоторые переменные, которые мы используем во всем файле. Затем идет раздел, содержащий некоторую информацию, используемую для конфигурации пакета, такую как его имя, версия и описание.




Все требования для новой архитектуры были заключены в [`install_modules_dependencies`](https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L198). Она заботится об установке соответствующих зависимостей в зависимости от того, какая архитектура включена в данный момент. Она также автоматически устанавливает зависимость `React-Core` в старой архитектуре.




### Android: `build.gradle` и класс `ReactPackage`.




Чтобы подготовить Android к запуску **Codegen**, необходимо:




1. Обновить файл `build.gradle`.

2. Класс Java/Kotlin, реализующий интерфейс `ReactPackage`.




По завершении этих шагов папка `android` должна выглядеть следующим образом:

```title="Android Folder Structure"
android
├── build.gradle
└── src
    └── main
        └── java
            └── com
                └── rtncalculator
                    └── CalculatorPackage.java
```

#### Файл `build.gradle`.



Сначала создайте файл `build.gradle` в папке `android` со следующим содержимым:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="build.gradle"
buildscript {
  ext.safeExtGet = {prop, fallback ->
    rootProject.ext.has(prop) ? rootProject.ext.get(prop) : fallback
  }
  repositories {
    google()
    gradlePluginPortal()
  }
  dependencies {
    classpath("com.android.tools.build:gradle:7.3.1")
  }
}

apply plugin: 'com.android.library'
apply plugin: 'com.facebook.react'

android {
  compileSdkVersion safeExtGet('compileSdkVersion', 33)
  namespace "com.rtncalculator"
}

repositories {
  mavenCentral()
  google()
}

dependencies {
  implementation 'com.facebook.react:react-native'
}
```

</TabItem>

<TabItem value="kotlin">

```kotlin title="build.gradle"
buildscript {
  ext.safeExtGet = {prop, fallback ->
    rootProject.ext.has(prop) ? rootProject.ext.get(prop) : fallback
  }
  repositories {
    google()
    gradlePluginPortal()
  }
  dependencies {
    classpath("com.android.tools.build:gradle:7.3.1")
    classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:1.7.22")
  }
}

apply plugin: 'com.android.library'
apply plugin: 'com.facebook.react'
apply plugin: 'org.jetbrains.kotlin.android'

android {
  compileSdkVersion safeExtGet('compileSdkVersion', 33)
  namespace "com.rtncalculator"
}

repositories {
  mavenCentral()
  google()
}

dependencies {
  implementation 'com.facebook.react:react-native'
}
```

</TabItem>
</Tabs>

#### Класс `ReactPackage`.



Затем вам нужен класс, который расширяет интерфейс `TurboReactPackage`. Для запуска процесса **Codegen** не обязательно полностью реализовывать класс пакета: достаточно пустой реализации, чтобы приложение приняло модуль как правильную зависимость React Native и попыталось сгенерировать код подмостков.




Создайте папку `android/src/main/java/com/rtncalculator` и внутри нее создайте файл `CalculatorPackage.java`.

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="CalculatorPackage.java"
package com.rtncalculator;

import androidx.annotation.Nullable;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.module.model.ReactModuleInfoProvider;
import com.facebook.react.TurboReactPackage;

import java.util.Collections;
import java.util.List;

public class CalculatorPackage extends TurboReactPackage {

  @Nullable
  @Override
  public NativeModule getModule(String name, ReactApplicationContext reactContext) {
          return null;
  }

  @Override
  public ReactModuleInfoProvider getReactModuleInfoProvider() {
      return null;
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="CalculatorPackage.kt"
package com.rtncalculator;

import com.facebook.react.TurboReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.module.model.ReactModuleInfoProvider

class CalculatorPackage : TurboReactPackage() {
  override fun getModule(name: String?, reactContext: ReactApplicationContext): NativeModule? = null

  override fun getReactModuleInfoProvider(): ReactModuleInfoProvider? = null
}
```

</TabItem>
</Tabs>

React Native использует интерфейс `ReactPackage`, чтобы понять, какие нативные классы приложение должно использовать для `ViewManager` и `Native Modules`, экспортируемых библиотекой.



## 4. Нативный код




Для последнего шага в подготовке вашего Turbo Native Module к работе, вам нужно будет написать нативный код, чтобы соединить JavaScript сторону с нативными платформами. Этот процесс требует двух основных шагов:




- Запустите **Codegen** и посмотрите, что он сгенерирует.

- Напишите свой нативный код, реализующий сгенерированные интерфейсы.




При разработке приложения React Native, использующего Turbo Native Module, ответственность за фактическую генерацию кода с помощью **Codegen** лежит на приложении. Однако при разработке TurboModule как библиотеки нам необходимо ссылаться на сгенерированный код, и поэтому полезно видеть, что будет сгенерировано приложением.




В качестве первого шага для iOS и Android в этом руководстве показано, как вручную выполнить скрипты, используемые **Codegen** для генерации необходимого кода. Дополнительную информацию о **Codegen** можно найти [здесь] (pillars-codegen.md).




:::осторожно

Код, сгенерированный **Codegen** на этом шаге, не должен быть зафиксирован в системе версий. Приложения React Native могут генерировать код во время сборки приложения. Это позволяет приложению убедиться, что для всех библиотек сгенерирован код для правильной версии React Native.

:::




### iOS




#### Генерация кода - iOS




Чтобы запустить Codegen для платформы iOS, нам нужно открыть терминал и выполнить следующую команду:

```sh title="Running Codegen for iOS"
cd MyApp
yarn add ../RTNCalculator
cd ..
node MyApp/node_modules/react-native/scripts/generate-codegen-artifacts.js \
  --path MyApp/ \
  --outputPath RTNCalculator/generated/
```

Этот сценарий сначала добавляет модуль `RTNCalculator` в приложение с помощью `yarn add`. Затем он вызывает Codegen с помощью сценария `generate-codegen-artifacts.js`.



Опция `--path` указывает путь к приложению, а опция `--outputPath` указывает Codegen, куда выводить сгенерированный код.




Результатом этого процесса будет следующая структура папок:

```sh
generated
└── build
    └── generated
        └── ios
            ├── FBReactNativeSpec
            │   ├── FBReactNativeSpec-generated.mm
            │   └── FBReactNativeSpec.h
            ├── RCTThirdPartyFabricComponentsProvider.h
            ├── RCTThirdPartyFabricComponentsProvider.mm
            ├── RTNCalculatorSpec
            │   ├── RTNCalculatorSpec-generated.mm
            │   └── RTNCalculatorSpec.h
            └── react
                └── renderer
                    └── components
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

Соответствующий путь для интерфейса Turbo Native Module - `generated/build/generated/ios/RTNCalculatorSpec`.



Более подробную информацию о сгенерированных файлах смотрите в разделе [Codegen](./pillars-codegen).




:::примечание

При генерации строительного кода с помощью **Codegen**, iOS не очищает папку `build` автоматически. Если вы, например, измените имя Spec, а затем снова запустите **Codegen**, старые файлы будут сохранены.

Если это произойдет, не забудьте удалить папку `build` перед повторным запуском **Codegen**.

```
cd MyApp/ios
rm -rf build
```

:::



#### Напишите нативный код для iOS




Теперь добавьте нативный код для вашего модуля Turbo Native. Создайте два файла в папке `RTNCalculator/ios`:




1. `RTNCalculator.h`, заголовочный файл для модуля.

2. `RTNCalculator.mm`, реализация модуля.




##### RTNCalculator.h

```objc title="RTNCalculator.h"
#import <RTNCalculatorSpec/RTNCalculatorSpec.h>

NS_ASSUME_NONNULL_BEGIN

@interface RTNCalculator : NSObject <NativeCalculatorSpec>

@end

NS_ASSUME_NONNULL_END
```

Этот файл определяет интерфейс для модуля `RTNCalculator`. Здесь мы можем добавить любой собственный метод, который мы хотим вызвать на представлении. В данном руководстве нам ничего не нужно, поэтому интерфейс пуст.



##### RTNCalculator.mm

```objc title="RTNCalculator.mm"
#import "RTNCalculatorSpec.h"
#import "RTNCalculator.h"

@implementation RTNCalculator

RCT_EXPORT_MODULE()

- (void)add:(double)a b:(double)b resolve:(RCTPromiseResolveBlock)resolve reject:(RCTPromiseRejectBlock)reject {
    NSNumber *result = [[NSNumber alloc] initWithInteger:a+b];
    resolve(result);
}

- (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:
    (const facebook::react::ObjCTurboModule::InitParams &)params
{
    return std::make_shared<facebook::react::NativeCalculatorSpecJSI>(params);
}

@end
```

Наиболее важным является вызов `RCT_EXPORT_MODULE`, который необходим для экспорта модуля, чтобы React Native мог загрузить Turbo Native Module.



Затем метод `add`, сигнатура которого должна совпадать с сигнатурой, указанной Codegen в файле `RTNCalculatorSpec.h`.




Наконец, метод `getTurboModule` получает экземпляр Turbo Native Module, чтобы сторона JavaScript могла вызывать его методы. Эта функция определена в файле `RTNCalculatorSpec.h` (и запрашивается им), который был сгенерирован ранее Codegen.




:::info

Существуют и другие макросы, которые могут быть использованы для экспорта модулей и методов. Код, определяющий их, можно посмотреть [здесь](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h).

:::




### Android




Android следует тем же шагам, что и iOS. Мы должны сгенерировать код для Android, а затем написать нативный код, чтобы заставить его работать.




#### Генерация кода - Android




Чтобы сгенерировать код для Android, нам нужно вручную вызвать Codegen. Это делается аналогично тому, как мы делали для iOS: сначала нужно добавить пакет в приложение, а затем вызвать скрипт.

```sh title="Running Codegen for Android"
cd MyApp
yarn add ../RTNCalculator
cd android
./gradlew generateCodegenArtifactsFromSchema
```

Этот сценарий сначала добавляет пакет в приложение, так же, как это делает iOS. Затем, после перемещения в папку `android`, он вызывает задачу Gradle для создания сгенерированного кода.



:::примечание

Чтобы запустить **Codegen**, необходимо включить **Новую архитектуру** в приложении Android. Это можно сделать, открыв файлы `gradle.properties` и переключив свойство `newArchEnabled` с `false` на `true`.

:::




Сгенерированный код хранится в папке `MyApp/node_modules/rtn-calculator/android/build/generated/source/codegen` и имеет такую структуру:

```title="Android generated code"
codegen
├── java
│   └── com
│       └── rtncalculator
│           └── NativeCalculatorSpec.java
├── jni
│   ├── Android.mk
│   ├── CMakeLists.txt
│   ├── RTNCalculator-generated.cpp
│   ├── RTNCalculator.h
│   └── react
│       └── renderer
│           └── components
│               └── RTNCalculator
│                   ├── ComponentDescriptors.h
│                   ├── EventEmitters.cpp
│                   ├── EventEmitters.h
│                   ├── Props.cpp
│                   ├── Props.h
│                   ├── ShadowNodes.cpp
│                   └── ShadowNodes.h
└── schema.json
```

#### Напишите нативный код Android



Нативный код для Android-стороны модуля Turbo Native Module требует:




1. создать `CalculatorModule.java`, который реализует модуль.

2. обновить `CalculatorPackage.java`, созданный на предыдущем шаге.




Окончательная структура библиотеки Android должна выглядеть следующим образом:

```title="Android Folder Structure"
android
├── build.gradle
└── src
    └── main
        └── java
            └── com
                └── rtncalculator
                    ├── CalculatorModule.java
                    └── CalculatorPackage.java
```

##### Создание `CalculatorModule.java`.

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="CalculatorModule.java"
package com.rtncalculator;

import androidx.annotation.NonNull;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.Promise;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
import java.util.Map;
import java.util.HashMap;
import com.rtncalculator.NativeCalculatorSpec;

public class CalculatorModule extends NativeCalculatorSpec {

    public static String NAME = "RTNCalculator";

    CalculatorModule(ReactApplicationContext context) {
        super(context);
    }

    @Override
    @NonNull
    public String getName() {
        return NAME;
    }

    @Override
    public void add(double a, double b, Promise promise) {
        promise.resolve(a + b);
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="CalculatorModule.kt"
package com.rtncalculator

import com.facebook.react.bridge.Promise
import com.facebook.react.bridge.ReactApplicationContext
import com.rtncalculator.NativeCalculatorSpec

class CalculatorModule(reactContext: ReactApplicationContext) : NativeCalculatorSpec(reactContext) {

  override fun getName() = NAME

  override fun add(a: Double, b: Double, promise: Promise) {
    promise.resolve(a + b)
  }

  companion object {
    const val NAME = "RTNCalculator"
  }
}
```

</TabItem>
</Tabs>

Этот класс реализует сам модуль, который расширяет `NativeCalculatorSpec`, сгенерированный из файла спецификации JavaScript `NativeCalculator`.



##### Обновление `CalculatorPackage.java`

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```diff title="CalculatorPackage.java"
package com.rtncalculator;

import androidx.annotation.Nullable;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
+ import com.facebook.react.module.model.ReactModuleInfo;
import com.facebook.react.module.model.ReactModuleInfoProvider;
import com.facebook.react.TurboReactPackage;

import java.util.Collections;
import java.util.List;
+ import java.util.HashMap;
+ import java.util.Map;

public class CalculatorPackage extends TurboReactPackage {

  @Nullable
  @Override
  public NativeModule getModule(String name, ReactApplicationContext reactContext) {
+      if (name.equals(CalculatorModule.NAME)) {
+          return new CalculatorModule(reactContext);
+      } else {
          return null;
+      }
  }


  @Override
  public ReactModuleInfoProvider getReactModuleInfoProvider() {
-      return null;
+      return () -> {
+          final Map<String, ReactModuleInfo> moduleInfos = new HashMap<>();
+          moduleInfos.put(
+                  CalculatorModule.NAME,
+                  new ReactModuleInfo(
+                          CalculatorModule.NAME,
+                          CalculatorModule.NAME,
+                          false, // canOverrideExistingModule
+                          false, // needsEagerInit
+                          true, // hasConstants
+                          false, // isCxxModule
+                          true // isTurboModule
+          ));
+          return moduleInfos;
+      };
  }
}
```

</TabItem>
<TabItem value="kotlin">

```diff title="CalculatorPackage.kt"
package com.rtncalculator;

import com.facebook.react.TurboReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
+import com.facebook.react.module.model.ReactModuleInfo
import com.facebook.react.module.model.ReactModuleInfoProvider

class CalculatorPackage : TurboReactPackage() {
- override fun getModule(name: String?, reactContext: ReactApplicationContext): NativeModule? = null
+ override fun getModule(name: String?, reactContext: ReactApplicationContext): NativeModule? =
+   if (name == CalculatorModule.NAME) {
+     CalculatorModule(reactContext)
+   } else {
+     null
+   }

- override fun getReactModuleInfoProvider() = ReactModuleInfoProvider? = null
+ override fun getReactModuleInfoProvider() = ReactModuleInfoProvider {
+   mapOf(
+     CalculatorModule.NAME to ReactModuleInfo(
+       CalculatorModule.NAME,
+       CalculatorModule.NAME,
+       false, // canOverrideExistingModule
+       false, // needsEagerInit
+       true, // hasConstants
+       false, // isCxxModule
+       true // isTurboModule
+     )
+   )
+ }
}
```

</TabItem>
</Tabs>

Это последняя часть нативного кода для Android. Он определяет объект `TurboReactPackage`, который будет использоваться приложением для загрузки модуля.



### Окончательная структура




Окончательная структура должна выглядеть следующим образом:

```sh
TurboModulesGuide
├── MyApp
└── RTNCalculator
    ├── android
    │   ├── build.gradle
    │   └── src
    │       └── main
    │           └── java
    │               └── com
    │                   └── rtncalculator
    │                       ├── CalculatorPackage.java
    │                       └── CalculatorModule.java
    ├── generated
    ├── ios
    │   ├── RTNCalculator.h
    │   └── RTNCalculator.mm
    ├── js
    │   └── NativeCalculator.js
    ├── package.json
    └── rtn-calculator.podspec
```

## 5. Adding the Turbo Native Module to your App

Now you can install and use the Turbo Native Module in your app.

### Shared

First of all, we need to add the NPM package which contains the Component to the app. This can be done with the following command:

```sh
cd MyApp
yarn add ../RTNCalculator
```

This command will add the `RTNCalculator` module to the `node_modules` of your app.

### iOS

Then, you need to install the new dependencies in your iOS project. To do so, run these commands:

```sh
cd ios
RCT_NEW_ARCH_ENABLED=1 bundle exec pod install
```

This command will look for all the dependencies of the project and it will install the iOS ones. The `RCT_NEW_ARCH_ENABLED=1` instruct **Cocoapods** that it has to run some additional operations to run **Codegen**.

:::note
You may have to run `bundle install` once before you can use `RCT_NEW_ARCH_ENABLED=1 bundle exec pod install`. You won't need to run `bundle install` anymore, unless you need to change the Ruby dependencies.
:::

### Android

Android configuration requires to enable the **New Architecture**:

1. Open the `android/gradle.properties` file
2. Scroll down to the end of the file and switch the `newArchEnabled` property from `false` to `true`.

### JavaScript

Now you can use your Turbo Native Module calculator in your app!

Here's an example App.js file using the `add` method:

<Tabs groupId="final-app" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="flow">

```typescript title="App.js"
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 *
 * @format
 * @flow strict-local
 */
import React from 'react';
import {useState} from 'react';
import type {Node} from 'react';
import {
  SafeAreaView,
  StatusBar,
  Text,
  Button,
} from 'react-native';
import RTNCalculator from 'rtn-calculator/js/NativeCalculator';

const App: () => Node = () => {
  const [result, setResult] = useState<number | null>(null);
  return (
    <SafeAreaView>
      <StatusBar barStyle={'dark-content'} />
      <Text style={{marginLeft: 20, marginTop: 20}}>
        3+7={result ?? '??'}
      </Text>
      <Button
        title="Compute"
        onPress={async () => {
          const value = await RTNCalculator.add(3, 7);
          setResult(value);
        }}
      />
    </SafeAreaView>
  );
};
export default App;
```

</TabItem>
<TabItem value="typescript">

```typescript title="App.tsx"
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 *
 * @format
 */
import React from 'react';
import {useState} from 'react';
import {
  SafeAreaView,
  StatusBar,
  Text,
  Button,
} from 'react-native';
import RTNCalculator from 'rtn-calculator/js/NativeCalculator';

const App: () => JSX.Element = () => {
  const [result, setResult] = useState<number | null>(null);
  return (
    <SafeAreaView>
      <StatusBar barStyle={'dark-content'} />
      <Text style={{marginLeft: 20, marginTop: 20}}>
        3+7={result ?? '??'}
      </Text>
      <Button
        title="Compute"
        onPress={async () => {
          const value = await RTNCalculator?.add(3, 7);
          setResult(value ?? null);
        }}
      />
    </SafeAreaView>
  );
};
export default App;
```

</TabItem>
</Tabs>

Try this out to see your Turbo Native Module in action!

