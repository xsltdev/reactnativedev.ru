# Компоненты Fabric как унаследованные нативные компоненты

!!!warning "Внимание"

    Эта документация все еще является **экспериментальной**, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в [обсуждении внутри рабочей группы](https://github.com/reactwg/react-native-new-architecture/discussions/8) для этой страницы.

    Более того, она содержит несколько **ручных шагов**. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

!!!info ""

    Создание обратно совместимого Fabric Native Component требует знаний о том, как создать Legacy Native Component. Чтобы вспомнить эти понятия, ознакомьтесь с этим [руководством](the-new-architecture-pillars-fabric-components.md).

    Fabric Native Components работают только при правильной настройке новой архитектуры. Если у вас уже есть библиотека, которую вы хотите перевести на новую архитектуру, ознакомьтесь также с [руководством по миграции](new-architecture-intro.md).

Создание обратно совместимого Fabric Native Component позволяет вашим пользователям продолжать использовать вашу библиотеку независимо от архитектуры, которую они используют. Создание такого компонента требует нескольких шагов:

1.  Настройте библиотеку так, чтобы зависимости были подготовлены для правильной установки как для старой, так и для новой архитектуры.
2.  Обновить кодовую базу таким образом, чтобы типы Новой архитектуры не компилировались, если они недоступны.
3.  Унифицируйте API JavaScript так, чтобы пользовательский код не нуждался в изменениях.

!!!info ""

    В данном руководстве мы будем использовать следующую **терминологию**:

    -   **Legacy Native Components** — для обозначения компонентов, работающих на старой архитектуре React Native.
    -   **Fabric Native Components** — Компоненты, которые были адаптированы для работы с новым нативным рендерером Fabric. Для краткости их можно назвать **Fabric Components**.

!!!warning "Внимание"

    Fabric Native Components работает только при включенной **Новой архитектуре**. Чтобы перейти на **новую архитектуру**, следуйте [руководству по миграции](new-architecture-intro.md)

В то время как последний шаг одинаков для всех платформ, первые два шага отличаются для iOS и Android.

## Настройте зависимости нативных компонентов Fabric.

### iOS

Платформа Apple устанавливает Fabric Native Components, используя [Cocoapods](https://cocoapods.org) в качестве менеджера зависимостей.

Если вы уже используете функцию [`install_module_dependencies`](https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L198), то **делать ничего не нужно**. Эта функция уже позаботилась об установке нужных зависимостей, когда включена Новая архитектура, и об их отсутствии, когда она не включена.

В противном случае, `podspec` вашего Fabric Native Component должен выглядеть следующим образом:

```ruby
require "json"

package = JSON.parse(File.read(File.join(__dir__, "package.json")))

folly_compiler_flags = '-DFOLLY_NO_CONFIG -DFOLLY_MOBILE=1 -DFOLLY_USE_LIBCPP=1 -Wno-comma -Wno-shorten-64-to-32'

Pod::Spec.new do |s|
  # Default fields for a valid podspec
  s.name            = "<FC Name>"
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
      "OTHER_CPLUSPLUSFLAGS" => "-DFOLLY_NO_CONFIG -DFOLLY_MOBILE=1 -DFOLLY_USE_LIBCPP=1",
      "CLANG_CXX_LANGUAGE_STANDARD" => "c++17"
  }

  s.dependency "React-RCTFabric"
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

- folly_compiler_flags = '-DFOLLY_NO_CONFIG -DFOLLY_MOBILE=1 -DFOLLY_USE_LIBCPP=1 -Wno-comma -Wno-shorten-64-to-32'

Pod::Spec.new do |s|
  # Default fields for a valid podspec
  s.name            = "<FC Name>"
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
-  # The following lines are required by the New Architecture.
-  s.compiler_flags = folly_compiler_flags + " -DRCT_NEW_ARCH_ENABLED=1"
-  s.pod_target_xcconfig    = {
-      "HEADER_SEARCH_PATHS" => "\"$(PODS_ROOT)/boost\"",
-      "OTHER_CPLUSPLUSFLAGS" => "-DFOLLY_NO_CONFIG -DFOLLY_MOBILE=1 -DFOLLY_USE_LIBCPP=1",
-      "CLANG_CXX_LANGUAGE_STANDARD" => "c++17"
-  }
-
-  s.dependency "React-RCTFabric"
-  s.dependency "React-Codegen"
-  s.dependency "RCT-Folly"
-  s.dependency "RCTRequired"
-  s.dependency "RCTTypeSafety"
-  s.dependency "ReactCommon/turbomodule/core"
end
```

### Android

Чтобы создать нативный компонент, который может работать с обеими архитектурами, необходимо настроить Gradle на выбор того, какие файлы должны быть скомпилированы в зависимости от выбранной архитектуры. Этого можно достичь, используя **различные наборы исходных текстов** в конфигурации Gradle.

!!!note ""

    Обратите внимание, что в настоящее время это рекомендуемый подход. Хотя он может привести к некоторому дублированию кода, он обеспечит максимальную совместимость с обеими архитектурами. Как уменьшить дублирование, вы увидите в следующем разделе.

Чтобы настроить Fabric Native Component так, чтобы он выбирал нужный набор источников, необходимо обновить файл `build.gradle` следующим образом:

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

1.  Первые строки определяют функцию, которая возвращает, включена ли Новая архитектура или нет.
2.  Строка `buildConfigField` определяет булево поле конфигурации сборки под названием `IS_NEW_ARCHITECTURE_ENABLED`, и инициализирует его с помощью функции, объявленной в первом шаге. Это позволит вам проверить во время выполнения, указал ли пользователь свойство `newArchEnabled` или нет.
3.  Последние строки используют функцию, объявленную в первом шаге, чтобы решить, какие наборы исходников нам нужно собрать, в зависимости от выбранной архитектуры.

## Обновление кодовой базы

### iOS

Второй шаг — это указание Xcode не компилировать все строки, использующие типы и файлы новой архитектуры, когда мы собираем приложение со старой архитектурой.

Fabric Native Component требует заголовочный файл и файл реализации для добавления фактического `View` в модуль.

Например, заголовочный файл `RNMyComponentView.h` может выглядеть следующим образом:

```objectivec title='RNMyComponentView.h'
#import <React/RCTViewComponentView.h>
#import <UIKit/UIKit.h>

#ifndef NativeComponentExampleComponentView_h
#define NativeComponentExampleComponentView_h

NS_ASSUME_NONNULL_BEGIN

@interface RNMyComponentView : RCTViewComponentView
@end

NS_ASSUME_NONNULL_END

#endif /* NativeComponentExampleComponentView_h */
```

Вместо этого файл реализации `RNMyComponentView.mm` может выглядеть следующим образом:

```objectivec title='RNMyComponentView.mm'
#import "RNMyComponentView.h"

// <react/renderer imports>

#import "RCTFabricComponentsPlugins.h"

using namespace facebook::react;

@interface RNMyComponentView () <RCTMyComponentViewViewProtocol>

@end

@implementation RNMyComponentView {
    UIView * _view;
}

+ (ComponentDescriptorProvider)componentDescriptorProvider
{
    // ... return the descriptor ...
}

- (instancetype)initWithFrame:(CGRect)frame
{
  // ... initialize the object ...
}

- (void)updateProps:(Props::Shared const &)props oldProps:(Props::Shared const &)oldProps
{
  // ... set up the props ...

  [super updateProps:props oldProps:oldProps];
}

Class<RCTComponentViewProtocol> MyComponentViewCls(void)
{
  return RNMyComponentView.class;
}

@end
```

Чтобы убедиться, что Xcode пропускает эти файлы, мы можем обернуть **обои** из них в какую-нибудь прагму компиляции `#ifdef RCT_NEW_ARCH_ENABLED`. Например, заголовочный файл может измениться следующим образом:

```diff
+ #ifdef RCT_NEW_ARCH_ENABLED
#import <React/RCTViewComponentView.h>
#import <UIKit/UIKit.h>

// ... rest of the header file ...

#endif /* NativeComponentExampleComponentView_h */
+ #endif
```

Эти же две строки должны быть добавлены в файл реализации, как первая и последняя строки.

В приведенном выше фрагменте используется тот же флаг `RCT_NEW_ARCH_ENABLED`, что и в предыдущем разделе. Когда этот флаг не установлен, Xcode пропускает строки внутри `#ifdef` во время компиляции и не включает их в скомпилированный двоичный файл. Скомпилированный двоичный файл будет содержать объект `RNMyComponentView.o`, но это будет пустой объект.

### Android

Поскольку мы не можем использовать блоки условной компиляции в Android, мы определим два разных набора исходных текстов. Это позволит создать обратно совместимый TurboModule с соответствующим исходным кодом, который загружается и компилируется в зависимости от используемой архитектуры.

Таким образом, вам необходимо:

1.  Создать Legacy Native Component по пути `src/oldarch`. Смотрите [это руководство](native-components-android.md), чтобы узнать, как создать Legacy Native Component.
2.  Создайте нативный компонент Fabric по пути `src/newarch`. См. [это руководство](the-new-architecture-pillars-fabric-components.md), чтобы узнать, как создать Fabric Native Component.

а затем поручите Gradle решить, какую реализацию выбрать.

Некоторые файлы могут быть общими для Legacy и Fabric Component: их следует создать или переместить в папку, которая загружается обеими архитектурами. К таким файлам относятся:

-   `<MyComponentView>.java`, который инстанцирует и конфигурирует Android View для обоих компонентов.
-   файл `<MyComponentView>ManagerImpl.java`, содержащий логику ViewManager, которая может использоваться совместно Legacy и Fabric Component.
-   файл `<MyComponentView>Package.java`, используемый для загрузки компонента.

Окончательная структура папок выглядит следующим образом:

```sh
my-component
├── android
│   ├── build.gradle
│   └── src
│       ├── main
│       │   ├── AndroidManifest.xml
│       │   └── java
│       │       └── com
│       │           └── mycomponent
│       │               ├── MyComponentView.java
│       │               ├── MyComponentViewManagerImpl.java
│       │               └── MyComponentViewPackage.java
│       ├── newarch
│       │   └── java
│       │       └── com
│       │           └── MyComponentViewManager.java
│       └── oldarch
│           └── java
│               └── com
│                   └── MyComponentViewManager.java
├── ios
├── js
└── package.json
```

Код, который должен находиться в файле `MyComponentViewManagerImpl.java` и который может быть разделен между нативным компонентом и нативным компонентом Fabric, например:

=== "Java"

    ```java title="example of MyComponentViewManager.java"
    package com.mycomponent;

    import androidx.annotation.Nullable;
    import com.facebook.react.uimanager.ThemedReactContext;

    public class MyComponentViewManagerImpl {

    	public static final String NAME = "MyComponent";

    	public static MyComponentView createViewInstance(ThemedReactContext context) {
    		return new MyComponentView(context);
    	}

    	public static void setFoo(MyComponentView view, String param) {
    		// implement the logic of the foo function using the view and the param passed.
    	}
    }
    ```

=== "Kotlin"

    ```kotlin title="example of MyComponentViewManager.kt"
    package com.mycomponent

    import com.facebook.react.uimanager.ThemedReactContext

    object MyComponentViewManagerImpl {
    const val NAME = "MyComponent"
    fun createViewInstance(context: ThemedReactContext?) = MyComponentView(context)

    fun setFoo(view: MyComponentView, param: String) {
    	// implement the logic of the foo function using the view and the param passed.
    }
    }
    ```

Затем нативный компонент и нативный компонент Fabric могут быть обновлены с помощью функции, объявленной в общем менеджере.

Например, для нативного компонента:

=== "Java"

    ```java title="Native Component using the ViewManagerImpl"
    public class MyComponentViewManager extends SimpleViewManager<MyComponentView> {

    	ReactApplicationContext mCallerContext;

    	public MyComponentViewManager(ReactApplicationContext reactContext) {
    		mCallerContext = reactContext;
    	}

    	@Override
    	public String getName() {
    		// static NAME property from the shared implementation
    		return MyComponentViewManagerImpl.NAME;
    	}

    	@Override
    	public MyComponentView createViewInstance(ThemedReactContext context) {
    		// static createViewInstance function from the shared implementation
    		return MyComponentViewManagerImpl.createViewInstance(context);
    	}

    	@ReactProp(name = "foo")
    	public void setFoo(MyComponentView view, String param) {
    		// static custom function from the shared implementation
    		MyComponentViewManagerImpl.setFoo(view, param);
    	}

    }
    ```

=== "Kotlin"

    ```kotlin title="Native Component using the ViewManagerImpl"
    class MyComponentViewManager(var context: ReactApplicationContext) : SimpleViewManager<MyComponentView>() {
    // Use the static NAME property from the shared implementation
    override fun getName() = MyComponentViewManagerImpl.NAME

    public override fun createViewInstance(context: ThemedReactContext): MyComponentView =
    	// static createViewInstance function from the shared implementation
    	MyComponentViewManagerImpl.createViewInstance(context)

    @ReactProp(name = "foo")
    fun setFoo(view: MyComponentView, param: String) {
    	// static custom function from the shared implementation
    	MyComponentViewManagerImpl.setFoo(view, param)
    }
    }
    ```

И для компонента Fabric Native Component:

=== "Java"

    ```java title="Fabric Component using the ViewManagerImpl"
    // Use the static NAME property from the shared implementation
    @ReactModule(name = MyComponentViewManagerImpl.NAME)
    public class MyComponentViewManager extends SimpleViewManager<MyComponentView>
    		implements MyComponentViewManagerInterface<MyComponentView> {

    	private final ViewManagerDelegate<MyComponentView> mDelegate;

    	public MyComponentViewManager(ReactApplicationContext context) {
    		mDelegate = new MyComponentViewManagerDelegate<>(this);
    	}

    	@Nullable
    	@Override
    	protected ViewManagerDelegate<MyComponentView> getDelegate() {
    		return mDelegate;
    	}

    	@NonNull
    	@Override
    	public String getName() {
    		// static NAME property from the shared implementation
    		return MyComponentViewManagerImpl.NAME;
    	}

    	@NonNull
    	@Override
    	protected MyComponentView createViewInstance(@NonNull ThemedReactContext context) {
    		// static createViewInstance function from the shared implementation
    		return MyComponentViewManagerImpl.createViewInstance(context);
    	}

    	@Override
    	@ReactProp(name = "foo")
    	public void setFoo(MyComponentView view, @Nullable String param) {
    		// static custom function from the shared implementation
    		MyComponentViewManagerImpl.setFoo(view, param);
    	}
    }
    ```

=== Kotlin

    ```kotlin title="Fabric Component using the ViewManagerImpl"
    // Use the static NAME property from the shared implementation
    @ReactModule(name = MyComponentViewManagerImpl.NAME)
    class MyComponentViewManager(context: ReactApplicationContext) : SimpleViewManager<MyComponentView>(), MyComponentViewManagerInterface<MyComponentView> {
    private val delegate: ViewManagerDelegate<MyComponentView> = MyComponentViewManagerDelegate(this)

    override fun getDelegate(): ViewManagerDelegate<MyComponentView> = delegate

    // Use the static NAME property from the shared implementation
    override fun getName(): String = MyComponentViewManagerImpl.NAME

    override fun createViewInstance(context: ThemedReactContext): MyComponentView =
    	// static createViewInstance function from the shared implementation
    	MyComponentViewManagerImpl.createViewInstance(context)

    @ReactProp(name = "foo")
    override fun setFoo(view: MyComponentView, text: String) {
    	// static custom function from the shared implementation
    	MyComponentViewManagerImpl.setFoo(view, param);
    }
    }
    ```

Пошаговый пример того, как этого добиться, можно найти в [этом репозитории](https://github.com/react-native-community/RNNewArchitectureLibraries/tree/feat/back-fabric-comp).

## Унификация спецификаций JavaScript

!!!warning "Внимание"

    Fabric Native Components работает только при включенной **Новой архитектуре**. Чтобы перейти на **новую архитектуру**, следуйте [руководству по миграции](new-architecture-intro.md)

Последний шаг позволяет убедиться, что JavaScript ведет себя прозрачно для выбранной архитектуры.

Для нативного компонента Fabric источником истины является файл спецификации `<Ваш модуль>NativeComponent.js` (или `.ts`). Приложение обращается к файлу спецификации следующим образом:

```ts
import MyComponent from 'your-component/src/index';
```

Поскольку `codegenNativeComponent` вызывает `requireNativeComponent` под капотом, нам нужно реэкспортировать наш компонент, чтобы избежать его многократной регистрации.

=== "Flow"

    ```ts
    // @flow
    export default require('./MyComponentNativeComponent')
    	.default;
    ```

=== "TypeScript"

    ```ts
    export default require('./MyComponentNativeComponent')
    	.default;
    ```
