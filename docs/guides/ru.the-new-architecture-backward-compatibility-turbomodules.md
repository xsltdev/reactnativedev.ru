

---
id: backward-compatibility-turbomodules
title: Turbo Modules as Legacy Native Modules
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

import constants from '@site/core/TabsConstants';

import BetaTS from './\_markdown_beta_ts_support.mdx';

import NewArchitectureWarning from './\_markdown-new-architecture-warning.mdx';

<NewArchitectureWarning/>

:::info
Создание обратно совместимого Turbo Native Module требует знания того, как создать Legacy Native Module. Чтобы вспомнить эти понятия, посмотрите это [руководство](pillars-turbomodules).




Турбомодули работают только при правильной настройке Новой архитектуры. Если у вас уже есть библиотека, которую вы хотите перевести на новую архитектуру, посмотрите также [руководство по миграции](../new-architecture-intro).

:::




Создание TurboModule с обратной совместимостью позволяет вашим пользователям продолжать использовать вашу библиотеку, независимо от архитектуры, которую они используют. Создание такого модуля требует нескольких шагов:




1. Сконфигурируйте библиотеку так, чтобы зависимости были правильно настроены как для старой, так и для новой архитектуры.

1. Обновить кодовую базу таким образом, чтобы типы Новой архитектуры не компилировались, если они недоступны.

1. Унифицируйте JavaScript API так, чтобы пользовательский код не нуждался в изменениях.




:::info




В данном руководстве мы будем использовать следующую **терминологию**:




- **Legacy Native Modules** - для обозначения модулей, работающих на старой архитектуре React Native.

- **Нативные модули Turbo** - Модули, которые были адаптированы для работы с новой системой нативных модулей. Для краткости вы можете встретить их под названием **Turbo Modules**.




:::

<BetaTS />

В то время как последний шаг одинаков для всех платформ, первые два шага отличаются для iOS и Android.



## Настройте зависимости модуля Turbo Native.




### <a name="dependencies-ios" />iOS




Платформа Apple устанавливает Turbo Native Modules, используя [Cocoapods](https://cocoapods.org) в качестве менеджера зависимостей.




Если вы уже используете функцию [`install_module_dependencies`](https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L198), то **делать ничего не нужно**. Эта функция уже позаботилась об установке нужных зависимостей, когда включена Новая архитектура, и избегает их, когда она не включена.




В противном случае, `podspec` вашего Turbo Native Module должен выглядеть следующим образом:

```ruby
require "json"

package = JSON.parse(File.read(File.join(__dir__, "package.json")))

folly_compiler_flags = '-DFOLLY_NO_CONFIG -DFOLLY_MOBILE=1 -DFOLLY_USE_LIBCPP=1 -Wno-comma -Wno-shorten-64-to-32'

Pod::Spec.new do |s|
  # Default fields for a valid podspec
  s.name            = "<TM Name>"
  s.version         = package["version"]
  s.summary         = package["description"]
  s.description     = package["description"]
  s.homepage        = package["homepage"]
  s.license         = package["license"]
  s.platforms       = { :ios => "11.0" }
  s.author          = package["author"]
  s.source          = { :git => package["repository"], :tag => "#{s.version}" }

  s.source_files    = "ios/**/*.{h,m,mm,swift}"
  # React Native Core dependency
  s.dependency "React-Core"

  # The following lines are required by the New Architecture.
  s.compiler_flags = folly_compiler_flags + " -DRCT_NEW_ARCH_ENABLED=1"
  s.pod_target_xcconfig    = {
      "HEADER_SEARCH_PATHS" => "\"$(PODS_ROOT)/boost\"",
      "CLANG_CXX_LANGUAGE_STANDARD" => "c++17"
  }

  s.dependency "React-Codegen"
  s.dependency "RCT-Folly"
  s.dependency "RCTRequired"
  s.dependency "RCTTypeSafety"
  s.dependency "ReactCommon/turbomodule/core"

end
```

Вам следует устанавливать дополнительные зависимости, когда Новая архитектура включена, и избегать их установки, когда она не включена.
Для этого можно воспользоваться командой [`install_modules_dependencies`](https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L198). Обновите файл `.podspec` следующим образом:

```diff
require "json"

package = JSON.parse(File.read(File.join(__dir__, "package.json")))

-folly_compiler_flags = '-DFOLLY_NO_CONFIG -DFOLLY_MOBILE=1 -DFOLLY_USE_LIBCPP=1 -Wno-comma -Wno-shorten-64-to-32'

Pod::Spec.new do |s|
  # Default fields for a valid podspec
  s.name            = "<TM Name>"
  s.version         = package["version"]
  s.summary         = package["description"]
  s.description     = package["description"]
  s.homepage        = package["homepage"]
  s.license         = package["license"]
  s.platforms       = { :ios => "11.0" }
  s.author          = package["author"]
  s.source          = { :git => package["repository"], :tag => "#{s.version}" }

  s.source_files    = "ios/**/*.{h,m,mm,swift}"
  # React Native Core dependency
+  install_modules_dependencies(s)
-  s.dependency "React-Core"
-
-  # The following lines are required by the New Architecture.
-  s.compiler_flags = folly_compiler_flags + " -DRCT_NEW_ARCH_ENABLED=1"
-  s.pod_target_xcconfig    = {
-      "HEADER_SEARCH_PATHS" => "\"$(PODS_ROOT)/boost\"",
-      "CLANG_CXX_LANGUAGE_STANDARD" => "c++17"
-  }
-
-  s.dependency "React-Codegen"
-  s.dependency "RCT-Folly"
-  s.dependency "RCTRequired"
-  s.dependency "RCTTypeSafety"
-  s.dependency "ReactCommon/turbomodule/core"
end
```

### Android



Чтобы создать модуль, который может работать с обеими архитектурами, необходимо настроить Gradle на выбор того, какие файлы должны быть скомпилированы в зависимости от выбранной архитектуры. Этого можно достичь, используя **различные наборы исходных текстов** в конфигурации Gradle.




:::примечание

Обратите внимание, что в настоящее время это рекомендуемый подход. Хотя он может привести к некоторому дублированию кода, он обеспечит максимальную совместимость с обеими архитектурами. Как уменьшить дублирование, вы увидите в следующем разделе.

:::




Чтобы настроить модуль Turbo Native так, чтобы он выбирал нужный набор исходников, необходимо обновить файл `build.gradle` следующим образом:

```diff title="build.gradle"
+// Add this function in case you don't have it already
+ def isNewArchitectureEnabled() {
+    return project.hasProperty("newArchEnabled") && project.newArchEnabled == "true"
+}


// ... other parts of the build file

defaultConfig {
        minSdkVersion safeExtGet('minSdkVersion', 21)
        targetSdkVersion safeExtGet('targetSdkVersion', 31)
+        buildConfigField("boolean", "IS_NEW_ARCHITECTURE_ENABLED", isNewArchitectureEnabled().toString())
+    }
+
+    sourceSets {
+        main {
+            if (isNewArchitectureEnabled()) {
+                java.srcDirs += ['src/newarch']
+            } else {
+                java.srcDirs += ['src/oldarch']
+            }
+        }
    }
}
```

Эти изменения делают три основные вещи:



1. Первые строки определяют функцию, которая возвращает, включена ли Новая архитектура или нет.

2. Строка `buildConfigField` определяет булево поле конфигурации сборки под названием `IS_NEW_ARCHITECTURE_ENABLED`, и инициализирует его с помощью функции, объявленной в первом шаге. Это позволит вам проверить во время выполнения, указал ли пользователь свойство `newArchEnabled` или нет.

3. Последние строки используют функцию, объявленную в первом шаге, чтобы решить, какие наборы исходников нам нужно собрать, в зависимости от выбранной архитектуры.




## Обновление кодовой базы




### iOS




Вторым шагом будет указание Xcode не компилировать все строки, использующие типы и файлы новой архитектуры, когда мы собираем приложение со старой архитектурой.




Для этого нужно изменить два файла. Файл реализации модуля, который обычно представляет собой файл `<ваш модуль>.mm`, и заголовок модуля, который обычно представляет собой файл `<ваш модуль>.h`.




Этот файл реализации структурирован следующим образом:




- Несколько операторов `#import`, среди которых есть файл `<GeneratedSpec>.h`.

- Реализация модуля, использующая различные макросы `RCT_EXPORT_xxx` и `RCT_REMAP_xxx`.

- Функция `getTurboModule:`, которая использует тип `<MyModuleSpecJSI>`, сгенерированный The New Architecture.




Цель **задачи** состоит в том, чтобы убедиться, что `Turbo Native Module` все еще собирается со старой архитектурой. Для этого мы можем обернуть `#import "<GeneratedSpec>.h"` и функцию `getTurboModule:` в директиву компиляции `#ifdef RCT_NEW_ARCH_ENABLED`, как показано в следующем примере:

```diff
#import "<MyModuleHeader>.h"
+ #ifdef RCT_NEW_ARCH_ENABLED
#import "<GeneratedSpec>.h"
+ #endif

// ... rest of your module

+ #ifdef RCT_NEW_ARCH_ENABLED
 - (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:
    (const facebook::react::ObjCTurboModule::InitParams &)params
 {
    return std::make_shared<facebook::react::<MyModuleSpecJSI>>(params);
 }
+ #endif

@end
```

Аналогично нужно поступить и с заголовочным файлом. Добавьте следующие строки в нижнюю часть заголовка вашего модуля. Вам нужно сначала импортировать заголовок, а затем, если включена Новая архитектура, сделать его соответствующим протоколу Spec.

```diff
#import <React/RCTBridgeModule.h>
+ #ifdef RCT_NEW_ARCH_ENABLED
+ #import <YourModuleSpec/YourModuleSpec.h>
+ #endif

@interface YourModule: NSObject <RCTBridgeModule>

@end

+ #ifdef RCT_NEW_ARCH_ENABLED
+ @interface YourModule () <YourModuleSpec>

+ @end
+ #endif

```

В этом фрагменте используется тот же флаг `RCT_NEW_ARCH_ENABLED`, что и в предыдущем [разделе](#dependencies-ios). Когда этот флаг не установлен, Xcode пропускает строки внутри `#ifdef` во время компиляции и не включает их в скомпилированный двоичный файл.



### Android




Поскольку мы не можем использовать условные блоки компиляции в Android, мы определим два разных набора исходников. Это позволит создать обратно совместимый Turbo Native модуль с соответствующим исходным кодом, который загружается и компилируется в зависимости от используемой архитектуры.




Таким образом, вам необходимо:




1. Создать Legacy Native Module по пути `src/oldarch`. См. [это руководство](../native-modules-intro), чтобы узнать, как создать Legacy Native Module.

2. Создайте Turbo Native Module по пути `src/newarch`. Смотрите [это руководство](./pillars-turbomodules), чтобы узнать, как создать Turbo Native Module




а затем поручите Gradle решить, какую реализацию выбрать.




Некоторые файлы могут быть общими для Legacy Native Module и Turbo Native Module: они должны быть созданы или перемещены в папку, которая загружается обеими архитектурами. К таким файлам относятся:




- файл `<MyModule>Package.java`, используемый для загрузки модуля.

- файл `<MyTurboModule>Impl.java`, куда мы можем поместить код, который должен выполнять как Legacy Native Module, так и Turbo Native Module.




Окончательная структура папок выглядит следующим образом:

```sh
my-module
├── android
│   ├── build.gradle
│   └── src
│       ├── main
│       │   ├── AndroidManifest.xml
│       │   └── java
│       │       └── com
│       │           └── mymodule
│       │               ├── MyModuleImpl.java
│       │               └── MyModulePackage.java
│       ├── newarch
│       │   └── java
│       │       └── com
│       │           └── MyModule.java
│       └── oldarch
│           └── java
│               └── com
│                   └── MyModule.java
├── ios
├── js
└── package.json
```

Код, который должен находиться в `MyModuleImpl.java`, и который может быть общим для Legacy Native Module и Turbo Native Module, например:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="example of MyModuleImpl.java"
package com.mymodule;

import androidx.annotation.NonNull;
import com.facebook.react.bridge.Promise;
import java.util.Map;
import java.util.HashMap;

public class MyModuleImpl {

    public static final String NAME = "MyModule";

    public void foo(double a, double b, Promise promise) {
        // implement the logic for foo and then invoke promise.resolve or
        // promise.reject.
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="example of MyModuleImpl.kt"
package com.mymodule;

import com.facebook.react.bridge.Promise

class MyModuleImpl {
  fun foo(a: Double, b: Double, promise: Promise) {
    // implement the logic for foo and then invoke
    // promise.resolve or promise.reject.
  }

  companion object {
    const val NAME = "MyModule"
  }
}
```

</TabItem>
</Tabs>

Затем можно обновить Legacy Native Module и Turbo Native Module, выполнив следующие действия:



1. Создайте частный экземпляр класса `MyModuleImpl`.

2. Инициализируйте экземпляр в конструкторе модуля.

3. Используйте частный экземпляр в методах модуля.




Например, для нативного модуля Legacy:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="Native Module using the Impl module"
public class MyModule extends ReactContextBaseJavaModule {

    // declare an instance of the implementation
    private MyModuleImpl implementation;

    MyModule(ReactApplicationContext context) {
        super(context);
        // initialize the implementation of the module
        implementation = MyModuleImpl();
    }

    @Override
    public String getName() {
        // NAME is a static variable, so we can access it using the class name.
        return MyModuleImpl.NAME;
    }

    @ReactMethod
    public void foo(int a, int b, Promise promise) {
        // Use the implementation instance to execute the function.
        implementation.foo(a, b, promise);
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="Native Module using the Impl module"
class MyModule(context: ReactApplicationContext) : ReactContextBaseJavaModule(context) {
  // declare an instance of the implementation and use it in all the methods
  private var implementation: MyModuleImpl = MyModuleImpl()

  override fun getName(): String = MyModuleImpl.NAME

  @ReactMethod
  fun foo(a: Double, b: Double, promise: Promise) {
    // Use the implementation instance to execute the function.
    implementation.foo(a, b, promise)
  }
}
```

</TabItem>
</Tabs>

И для модуля Turbo Native:

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="TurboModule using the Impl module"
public class MyModule extends MyModuleSpec {
    // declare an instance of the implementation
    private MyModuleImpl implementation;

    MyModule(ReactApplicationContext context) {
        super(context);
        // initialize the implementation of the module
        implementation = MyModuleImpl();
    }

    @Override
    @NonNull
    public String getName() {
        // NAME is a static variable, so we can access it using the class name.
        return MyModuleImpl.NAME;
    }

    @Override
    public void foo(double a, double b, Promise promise) {
        // Use the implementation instance to execute the function.
        implementation.foo(a, b, promise);
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="TurboModule using the Impl module"
class MyModule(reactContext: ReactApplicationContext) : MyModuleSpec(reactContext) {
  // declare an instance of the implementation and use it in all the methods
  private var implementation: MyModuleImpl = MyModuleImpl()

  override fun getName(): String = MyModuleImpl.NAME

  override fun foo(a: Double, b: Double, promise: Promise) {
    // Use the implementation instance to execute the function.
    implementation.foo(a, b, promise)
  }
}
```

</TabItem>
</Tabs>

Пошаговый пример того, как этого добиться, можно найти в [этом репозитории](https://github.com/react-native-community/RNNewArchitectureLibraries/tree/feat/back-turbomodule).



## Унификация спецификаций JavaScript

<BetaTS />

Последний шаг позволяет убедиться, что JavaScript ведет себя прозрачно для выбранной архитектуры.



Для Turbo Native Module источником истины является файл спецификации `Native<MyModule>.js` (или `.ts`). Приложение обращается к файлу спецификации следующим образом:

```ts
import MyModule from 'your-module/src/index';
```

Поскольку `TurboModuleRegistry.get` использует старый Native Modules API под капотом, нам нужно реэкспортировать наш модуль, чтобы избежать его многократной регистрации.



<Tabs groupId="turbomodule-backward-compatibility" queryString

      defaultValue={constants.defaultTurboModuleSpecLanguage}

      values={constants.turboModuleSpecLanguages}>

<TabItem value="Flow">

```ts
// @flow
export default require('./Native<MyModule>').default;
```

</TabItem>
<TabItem value="TypeScript">

```ts
export default require('./Native<MyModule>').default;
```

</TabItem>
</Tabs>

