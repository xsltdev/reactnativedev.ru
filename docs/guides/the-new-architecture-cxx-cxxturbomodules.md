# Модули C++ Turbo Native

!!!warning "Внимание"

    Эта документация все еще является **экспериментальной**, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в [обсуждении внутри рабочей группы](https://github.com/reactwg/react-native-new-architecture/discussions/8) для этой страницы.

    Более того, она содержит несколько **ручных шагов**. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

Это руководство показывает, как реализовать Turbo Native Module, используя только C++, что позволяет использовать одну и ту же реализацию на любой поддерживаемой платформе (Android, iOS, macOS или Windows).

Прежде чем продолжить работу с этим руководством, пожалуйста, прочитайте раздел [Turbo Native Modules](the-new-architecture-pillars-turbomodules.md). В качестве дополнительной справки мы подготовили пример для приложения RNTester ([NativeCxxModuleExample](https://github.com/facebook/react-native/tree/main/packages/rn-tester/NativeCxxModuleExample)) и пример запуска в репозитории нашего сообщества ([run/pure-cxx-module](https://github.com/react-native-community/RNNewArchitectureApp/tree/run/pure-cxx-module)).

!!!warning ""

    Модули C++ Turbo Native работают с включенной **Новой архитектурой**.

    Чтобы перейти на **Новую архитектуру**, следуйте [Руководству по миграции](new-architecture-intro.md)

## Как создать модуль C++ Turbo Native

Чтобы создать модуль C++ Turbo Native Module, вам необходимо:

1.  Определить спецификацию JavaScript.
2.  Настроить Codegen на генерацию строительных лесов.
3.  Зарегистрируйте нативный модуль.
4.  Напишите нативный код для завершения реализации модуля.

### Настройка тестового приложения для новой архитектуры

В качестве первого шага создайте новое приложение:

```sh
npx react-native init CxxTurboModulesGuide
cd CxxTurboModulesGuide
yarn install
```

На Android включите новую архитектуру, изменив файл `android/gradle.properties`:

```diff
- newArchEnabled=false
+ newArchEnabled=true
```

На iOS включите Новую архитектуру при запуске `pod install` в папке `ios`:

```sh
RCT_NEW_ARCH_ENABLED=1 bundle exec pod install
```

### Настройка папки турбо-модуля

Создайте папку `tm` внутри проекта. Она будет содержать все турбомодули C++ вашего приложения. Конечный результат должен выглядеть следующим образом:

```sh
CxxTurboModulesGuide
├── android
├── ios
├── js
└── tm
```

## 1. Спецификация JavaScript

Создайте следующую спецификацию в папке `tm`:

=== "TypeScript"

    ```typescript title="NativeSampleModule.ts"
    import type { TurboModule } from 'react-native/Libraries/TurboModule/RCTExport';
    // import type {TurboModule} from 'react-native'; in future versions
    import { TurboModuleRegistry } from 'react-native';

    export interface Spec extends TurboModule {
    	readonly reverseString: (input: string) => string;
    }

    export default TurboModuleRegistry.getEnforcing<Spec>(
    	'NativeSampleModule'
    );
    ```

=== "Flow"

    ```js title="NativeSampleModule.js"
    // @flow
    import type { TurboModule } from 'react-native/Libraries/TurboModule/RCTExport';
    // import type {TurboModule} from 'react-native'; in future versions
    import { TurboModuleRegistry } from 'react-native';

    export interface Spec extends TurboModule {
    	+reverseString: (input: string) => string;
    }

    export default (TurboModuleRegistry.getEnforcing<Spec>(
    	'NativeSampleModule'
    ): Spec);
    ```

## 2. Конфигурация Codegen

Далее необходимо добавить некоторую конфигурацию для [**Codegen**](the-new-architecture-pillars-codegen.md).

### Приложение

Обновите файл `package.json` вашего приложения следующими записями:

```json title="package.json"
{
    // ...
    "description": "React Native with Cxx Turbo Native Modules",
    "author": "<Your Name> <your_email@your_provider.com> (https://github.com/<your_github_handle>)",
    "license": "MIT",
    "homepage": "https://github.com/<your_github_handle>/#readme",
    // ...
    "codegenConfig": {
        "name": "AppSpecs",
        "type": "all",
        "jsSrcsDir": "tm",
        "android": {
            "javaPackageName": "com.facebook.fbreact.specs"
        }
    }
}
```

Он добавляет необходимые свойства, которые мы позже будем повторно использовать в файле iOS `podspec`, и настраивает **Codegen** на поиск спецификаций в папке `tm`.

!!!warning ""

    Модули C++ Turbo Native не автолинкуются и должны быть вручную включены в приложение с помощью описанных ниже шагов.

### iOS: Создайте файл `podspec`.

Для iOS вам потребуется создать файл `AppTurboModules.podspec` в папке `tm` - он будет выглядеть следующим образом:

```ruby title="AppTurboModules.podspec"
require "json"

package = JSON.parse(File.read(File.join(__dir__, "../package.json")))

Pod::Spec.new do |s|
  s.name            = "AppTurboModules"
  s.version         = package["version"]
  s.summary         = package["description"]
  s.description     = package["description"]
  s.homepage        = package["homepage"]
  s.license         = package["license"]
  s.platforms       = { :ios => "12.4" }
  s.author          = package["author"]
  s.source          = { :git => package["repository"], :tag => "#{s.version}" }
  s.source_files    = "**/*.{h,cpp}"
  s.pod_target_xcconfig = {
    "CLANG_CXX_LANGUAGE_STANDARD" => "c++17"
  }
  install_modules_dependencies(s)
end
```

Вам нужно добавить его в качестве зависимости в ваше приложение в `ios/Podfile`, например, после секции `use_react_native!(...)`:

```ruby
if ENV['RCT_NEW_ARCH_ENABLED'] == '1'
  pod 'AppTurboModules', :path => "./../tm"
end
```

### Android: `build.gradle`, `CMakeLists.txt`, `Onload.cpp`.

Для Android вам нужно будет создать файл `CMakeLists.txt` в папке `tm` - он будет выглядеть следующим образом:

```cmake
cmake_minimum_required(VERSION 3.13)
set(CMAKE_VERBOSE_MAKEFILE on)

add_compile_options(
        -fexceptions
        -frtti
        -std=c++17)

file(GLOB tm_SRC CONFIGURE_DEPENDS *.cpp)
add_library(tm STATIC ${tm_SRC})

target_include_directories(tm PUBLIC .)
target_include_directories(react_codegen_AppSpecs PUBLIC .)

target_link_libraries(tm
        jsi
        react_nativemodule_core
        react_codegen_AppSpecs)
```

Он определяет папку `tm` как источник нативного кода и устанавливает необходимые зависимости.

Вам нужно добавить его в качестве зависимости к вашему приложению в `android/app/build.gradle`, например, в самом конце этого файла:

```kotlin title="build.gradle"
android {
   externalNativeBuild {
       cmake {
           path "src/main/jni/CMakeLists.txt"
       }
   }
}
```

!!!warning ""

    Убедитесь, что выбрали правильный файл `android/app/build.gradle`, а не `android/build.gradle`.

## 3. Регистрация модуля

### iOS

Чтобы зарегистрировать турбо-нативный модуль C++ в вашем приложении, вам нужно обновить `ios/CxxTurboModulesGuide/AppDelegate.mm` со следующими записями:

```diff
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
+ #import <React/CoreModulesPlugins.h>
+ #import <ReactCommon/RCTTurboModuleManager.h>
+ #import <NativeSampleModule.h>

+ @interface AppDelegate () <RCTTurboModuleManagerDelegate> {}
+ @end

// ...

᠆ (Class)getModuleClassFromName:(const char *)name
{
  return RCTCoreModulesClassProvider(name);
}

+ #pragma mark RCTTurboModuleManagerDelegate

+ - (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:(const std::string &)name
+                                                       jsInvoker:(std::shared_ptr<facebook::react::CallInvoker>)jsInvoker
+ {
+   if (name == "NativeSampleModule") {
+     return std::make_shared<facebook::react::NativeSampleModule>(jsInvoker);
+   }
+   return nullptr;
+ }
```

Это приведет к созданию `NativeSampleModule`, связанного с именем `NativeSampleModule`, как определено в нашем файле спецификации JavaScript ранее.

### Android

Приложения Android по умолчанию не настроены на компиляцию нативного кода.

1.  Создайте папку `android/app/src/main/jni`.
2.  Скопируйте `CMakeLists.txt` и `Onload.cpp` из [node_modules/react-native/ReactAndroid/cmake-utils/default-app-setup](https://github.com/facebook/react-native/tree/main/packages/react-native/ReactAndroid/cmake-utils/default-app-setup) в папку `android/app/src/main/jni`.

Обновите `Onload.cpp` следующими записями:

```diff
// ...

#include <react/renderer/componentregistry/ComponentDescriptorProviderRegistry.h>
#include <rncli.h>
+ #include <NativeSampleModule.h>

// ...

std::shared_ptr<TurboModule> cxxModuleProvider(
    const std::string &name,
    const std::shared_ptr<CallInvoker> &jsInvoker) {
+ if (name == "NativeSampleModule") {
+   return std::make_shared<facebook::react::NativeSampleModule>(jsInvoker);
+ }
  return nullptr;
}

// ...
```

Обновите `CMakeLists.txt` следующими записями, например, в самом конце этого файла:

```diff
// ...

# This file includes all the necessary to let you build your application with the New Architecture.
include(${REACT_ANDROID_DIR}/cmake-utils/ReactNative-application.cmake)

+ # App needs to add and link against tm (TurboModules) folder
+ add_subdirectory(${REACT_ANDROID_DIR}/../../../tm/ tm_build)
+ target_link_libraries(${CMAKE_PROJECT_NAME} tm)
```

Это приведет к созданию `NativeSampleModule`, связанного с именем `NativeSampleModule`, определенным ранее в нашем файле спецификации JavaScript.

## 4. Нативный код на C++

На последнем этапе вам нужно будет написать нативный код, чтобы соединить JavaScript со своими родными платформами. Этот процесс требует двух основных шагов:

-   Запустите **Codegen** и посмотрите, что он сгенерирует.
-   Напишите свой родной код, реализующий сгенерированные интерфейсы.

### Запустите Codegen

!!!info ""

    Следуйте руководству [Codegen](the-new-architecture-pillars-codegen.md) для получения общей информации.

На iOS Codegen запускается каждый раз при выполнении в папке `ios`:

```sh
RCT_NEW_ARCH_ENABLED=1 bundle exec pod install
```

Вы можете просмотреть сгенерированные файлы `AppSpecsJSI.h` и `AppSpecsJSI-generated.cpp` в папке `CxxTurboModulesGuide/ios/build/generated/ios`.

Эти файлы имеют префикс `AppSpecs`, так как он соответствует параметру `codegenConfig.name`, добавленному ранее в `package.json`.

На Android Codegen запускается каждый раз при выполнении:

```sh
yarn android
```

Вы можете просмотреть сгенерированные файлы `AppSpecsJSI.h` и `AppSpecsJSI-generated.cpp` в папке `CxxTurboModulesGuide/android/app/build/generated/source/codegen/jni`.

Вам нужно запустить codegen заново только в том случае, если вы изменили спецификацию JavaScript.

Функция C++, сгенерированная для нашего файла спецификации JavaScript, выглядит следующим образом:

```cpp
virtual jsi::String reverseString(jsi::Runtime &rt, jsi::String input) = 0;
```

Вы можете напрямую работать с типами нижнего уровня `jsi::` - но для удобства нативные модули C++ Turbo автоматически "перемыкают" типы в `std::`.

### Реализация

Теперь создайте файл `NativeSampleModule.h` со следующим содержимым:

!!!note ""

    Из-за текущих различий в настройках CMake и CocoaPod нам потребуется немного творчества, чтобы включить правильный заголовок Codegen на каждой платформе.

```cpp
#pragma once

#if __has_include(<React-Codegen/AppSpecsJSI.h>) // CocoaPod headers on Apple
#include <React-Codegen/AppSpecsJSI.h>
#elif __has_include("AppSpecsJSI.h") // CMake headers on Android
#include "AppSpecsJSI.h"
#endif
#include <memory>
#include <string>

namespace facebook::react {

class NativeSampleModule : public NativeSampleModuleCxxSpec<NativeSampleModule> {
 public:
  NativeSampleModule(std::shared_ptr<CallInvoker> jsInvoker);

  std::string reverseString(jsi::Runtime& rt, std::string input);
};

} // namespace facebook::react
```

В этом случае вы можете использовать любой тип C++, который `мостит` к `jsi::String` - по умолчанию или [пользовательский тип](the-new-architecture-cxx-custom-types.md). Вы не можете указать несовместимый тип, такой как `bool`, `float` или `std::vector<>`, так как он не `переходит` к `jsi::String` и, следовательно, приводит к ошибке компиляции.

Теперь добавьте файл `NativeSampleModule.cpp` с реализацией для него:

```cpp
#include "NativeSampleModule.h"

namespace facebook::react {

NativeSampleModule::NativeSampleModule(std::shared_ptr<CallInvoker> jsInvoker)
    : NativeSampleModuleCxxSpec(std::move(jsInvoker)) {}

std::string NativeSampleModule::reverseString(jsi::Runtime& rt, std::string input) {
  return std::string(input.rbegin(), input.rend());
}

} // namespace facebook::react
```

Поскольку мы добавили новые файлы C++, запускаемые в папке `ios`:

```sh
RCT_NEW_ARCH_ENABLED=1 bundle exec pod install
```

для iOS. В Xcode они появляются под целью `Pods` в подпапке `Development Pods \ TurboModules`.

Теперь вы должны иметь возможность компилировать ваше приложение как на Android, так и на iOS.

```sh
CxxTurboModulesGuide
├── android
│   └── app
│       │── src
│       │   └── main
│       │       └── jni
│       │           ├── CMakeLists.txt
│       │           └── OnLoad.cpp
│       └── build.gradle (updated)
├── ios
│   └── CxxTurboModulesGuide
│       └── AppDelegate.mm (updated)
├── js
│   └── App.tsx|jsx (updated)
└── tm
    ├── CMakeLists.txt
    ├── NativeSampleModule.h
    ├── NativeSampleModule.cpp
    ├── NativeSampleModule.ts|js
    └── TurboModules.podspec
```

## 5. Добавление модуля C++ Turbo Native в ваше приложение

Для демонстрационных целей мы можем обновить `App.tsx|jsx` нашего приложения следующими записями:

```diff
//...
import {
  Colors,
  DebugInstructions,
  Header,
  LearnMoreLinks,
  ReloadInstructions,
} from 'react-native/Libraries/NewAppScreen';
+ import NativeSampleModule from './tm/NativeSampleModule';
//...
        <View
          style={{
            backgroundColor: isDarkMode ? Colors.black : Colors.white,
          }}>
+         <Section title="Cxx TurboModule">
+          NativeSampleModule.reverseString(...) ={' '}
+          {NativeSampleModule.reverseString(
+            'the quick brown fox jumps over the lazy dog'
+          )}
+         </Section>;
          <Section title="Step One">
            Edit <Text style={styles.highlight}>App.tsx</Text> to change this
            screen and then come back to see your edits.
          </Section>
//...
```

Запустите приложение, чтобы увидеть ваш нативный модуль C++ Turbo в действии!

## App TurboModuleProvider [Необязательно]

Вы можете избежать дублирования кода при добавлении нескольких модулей C++ Turbo Native Modules, объявив AppTurboModuleProvider:

```cpp title="AppTurboModuleProvider.h"
#pragma once

#include <ReactCommon/TurboModuleBinding.h>
#include <memory>
#include <string>

namespace facebook::react {

class AppTurboModuleProvider {
 public:
  std::shared_ptr<TurboModule> getTurboModule(
      const std::string& name,
      std::shared_ptr<CallInvoker> jsInvoker) const;
};

} // namespace facebook::react
```

И реализовать его:

```cpp title="AppTurboModuleProvider.cpp"
#include "AppTurboModuleProvider.h"
#include "NativeSampleModule.h"

namespace facebook::react {

std::shared_ptr<TurboModule> AppTurboModuleProvider::getTurboModule(
    const std::string& name,
    std::shared_ptr<CallInvoker> jsInvoker) const {
  if (name == "NativeSampleModule") {
    return std::make_shared<facebook::react::NativeSampleModule>(jsInvoker);
  }
  // Other C++ Turbo Native Modules for you app
  return nullptr;
}

} // namespace facebook::react
```

А затем повторно использовать его в `OnLoad.cpp` для Android и `AppDelegate.mm` для iOS, например, через:

```cpp
static facebook::react::AppTurboModuleProvider appTurboModuleProvider;
return appTurboModuleProvider.getTurboModule(name, jsInvoker);
```

в соответствующих функциях.

## Вызов API, специфичных для ОС

Вы все еще можете вызывать специфические для ОС функции в блоке компиляции (например, `NS/CF` API на Apple или `Win32/WinRT` API на Windows) при условии, что сигнатуры методов используют только `std::` или `jsi::` типы.

Для API, специфичных для Apple, вам нужно изменить расширение вашего файла реализации с `.cpp` на `.mm`, чтобы иметь возможность использовать API `NS/CF`.

## Расширение модулей C++ Turbo Native

Если вам нужно поддерживать некоторые типы, которые пока не поддерживаются, посмотрите [это другое руководство](the-new-architecture-cxx-custom-types.md).
