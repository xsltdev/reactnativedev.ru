# Компоненты Fabric Native

!!!warning "Внимание"

    Эта документация все еще является **экспериментальной**, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в [обсуждении внутри рабочей группы](https://github.com/reactwg/react-native-new-architecture/discussions/8) для этой страницы.

    Более того, она содержит несколько **ручных шагов**. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

Нативный компонент Fabric — это нативный компонент, отображаемый на экране с помощью [Fabric Renderer](https://reactnative.dev/architecture/fabric-renderer). Использование Fabric Native Components вместо Legacy Native Components позволяет нам воспользоваться всеми [преимуществами](the-new-architecture-why.md) **Новой архитектуры**:

-   Сильно типизированные интерфейсы, согласованные на разных платформах.
-   Возможность писать код исключительно на C++ или интегрировать его с другим родным языком платформы, что снижает необходимость дублирования реализаций на разных платформах.
-   Использование JSI, интерфейса JavaScript для нативного кода, который обеспечивает более эффективное взаимодействие между нативным и JavaScript-кодом, чем мост.

Fabric Native Component создается, начиная со спецификации **JavaScript**. Затем [**Codegen**](the-new-architecture-pillars-codegen.md) создает некоторый код на C++ для подключения специфической для компонента логики (например, доступ к некоторым возможностям нативной платформы) к остальной инфраструктуре React Native. Код на C++ одинаков для всех платформ. После того как компонент должным образом связан со строительным кодом, он готов к импорту и использованию в приложении.

Следующий раздел шаг за шагом проведет вас через создание Fabric Native Component, ориентированного на последнюю версию React Native.

!!!warning ""

    Fabric Native Components работает только при включенной **Новой архитектуре**.

    Чтобы перейти на **Новую архитектуру**, следуйте [Руководству по миграции](new-architecture-intro.md)

## Как создать Fabric Native Components

Чтобы создать нативный компонент Fabric, необходимо выполнить следующие шаги:

1.  Определите набор спецификаций JavaScript.
2.  Настройте компонент так, чтобы **Codegen** мог создавать общий код и его можно было добавить в качестве зависимости для приложения.
3.  Напишите необходимый нативный код.

После выполнения этих шагов компонент готов к использованию в приложении. В руководстве показано, как добавить его в приложение, используя _autolinking_, и как ссылаться на него из кода JavaScript.

## 1. Настройка папки

Для того, чтобы компонент не был связан с приложением, хорошей идеей будет определить модуль отдельно от приложения, а затем добавить его в качестве зависимости в ваше приложение. Так же вы поступите и при написании нативных компонентов Fabric, которые позже можно будет выпустить в виде библиотек с открытым исходным кодом.

В этом руководстве вы собираетесь создать Fabric Native Component, который центрирует текст на экране.

Создайте новую папку на том же уровне приложения и назовите ее `RTNCenteredText`.

В этой папке создайте три подпапки: `js`, `ios` и `android`.

Конечный результат должен выглядеть следующим образом:

```sh
.
├── MyApp
└── RTNCenteredText
    ├── android
    ├── ios
    └── js
```

## 2. Спецификация JavaScript

Для **Новой архитектуры** требуются интерфейсы, специфицированные в типизированном диалекте JavaScript (либо [Flow](https://flow.org/), либо [TypeScript](https://scriptdev.ru/guide/)). **Codegen** использует эти спецификации для генерации кода на сильно типизированных языках, включая C++, Objective-C++ и Java.

Файл, содержащий эту спецификацию, должен удовлетворять двум требованиям:

1.  Файл **должен** иметь имя `<MODULE_NAME>NativeComponent`, с расширением `.js` или `.jsx` при использовании Flow, или `.ts`, или `.tsx` при использовании TypeScript. **Codegen** ищет только файлы, соответствующие этому шаблону.
2.  Файл должен экспортировать объект `HostComponent`.

Ниже приведены спецификации компонента `RTNCenteredText` как в Flow, так и в TypeScript. Создайте файл `RTNCenteredTextNativeComponent` с соответствующим расширением в папке `js`.

=== "Flow"

    ```typescript
    // @flow strict-local

    import type {ViewProps} from 'react-native/Libraries/Components/View/ViewPropTypes';
    import type {HostComponent} from 'react-native';
    import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';

    type NativeProps = $ReadOnly<{|
    ...ViewProps,
    text: ?string,
    // add other props here
    |}>;

    export default (codegenNativeComponent<NativeProps>(
    'RTNCenteredText',
    ): HostComponent<NativeProps>);
    ```

=== "TypeScript"

    ```typescript
    import type { ViewProps } from 'ViewPropTypes';
    import type { HostComponent } from 'react-native';
    import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';

    export interface NativeProps extends ViewProps {
    	text?: string;
    	// add other props here
    }

    export default codegenNativeComponent<NativeProps>(
    	'RTNCenteredText'
    ) as HostComponent<NativeProps>;
    ```

В начале файлов спецификаций находятся импорты. Наиболее важными импортами, необходимыми для каждого Fabric Native Component, являются:

-   `HostComponent`: тип, которому должен соответствовать экспортируемый компонент.
-   Функция `codegenNativeComponent`: отвечает за фактическую регистрацию компонента в среде выполнения JavaScript.

Вторая секция файлов содержит **предметы** компонента. [Props](intro-react.md) (сокращение от "properties") — это специфическая для компонента информация, которая позволяет вам настраивать компоненты React. В данном случае вы хотите управлять свойством `text` компонента.

Наконец, файл спецификации экспортирует возвращаемое значение общей функции `codegenNativeComponent`, вызываемой с передачей имени компонента.

!!!warning ""

    Файлы JavaScript импортируют типы из библиотек без установки соответствующего модуля node и установки его зависимостей. В результате этого у IDE могут возникнуть проблемы с разрешением утверждений импорта, и она может выводить ошибки и предупреждения.

    Они исчезнут, как только Fabric Native Component будет добавлен в качестве зависимости в приложение React Native.

## 3. Конфигурация компонента

Далее необходимо добавить некоторые конфигурации для [**Codegen**](the-new-architecture-pillars-codegen.md) и автоссылки.

Некоторые из этих конфигурационных файлов являются общими для iOS и Android, в то время как другие зависят от конкретной платформы.

### Общая конфигурация

Общая конфигурация — это файл `package.json`, который будет использоваться yarn при установке вашего модуля. Создайте файл `package.json` в корне каталога `RTNCenteredText`.

```json title="package.json"
{
    "name": "rtn-centered-text",
    "version": "0.0.1",
    "description": "Showcase a Fabric Native Component with a centered text",
    "react-native": "js/index",
    "source": "js/index",
    "files": [
        "js",
        "android",
        "ios",
        "rtn-centered-text.podspec",
        "!android/build",
        "!ios/build",
        "!**/__tests__",
        "!**/__fixtures__",
        "!**/__mocks__"
    ],
    "keywords": ["react-native", "ios", "android"],
    "repository": "https://github.com/<your_github_handle>/rtn-centered-text",
    "author": "<Your Name> <your_email@your_provider.com> (https://github.com/<your_github_handle>)",
    "license": "MIT",
    "bugs": {
        "url": "https://github.com/<your_github_handle>/rtn-centered-text/issues"
    },
    "homepage": "https://github.com/<your_github_handle>/rtn-centered-text#readme",
    "devDependencies": {},
    "peerDependencies": {
        "react": "*",
        "react-native": "*"
    },
    "codegenConfig": {
        "name": "RTNCenteredTextSpecs",
        "type": "components",
        "jsSrcsDir": "js"
    }
}
```

Верхняя часть файла содержит некоторую описательную информацию, такую как название компонента, его версия и исходные файлы. Обязательно обновите различные заполнители, которые обернуты в `<>`: замените все вхождения маркеров `<ваш_github_handle>`, `<ваше имя>` и `<your_email@your_provider.com>`.

Затем есть зависимости для этого пакета. Для этого руководства вам понадобятся `react` и `react-native`.

Наконец, конфигурация **Codegen** задается полем `codegenConfig`. Оно содержит массив библиотек, каждая из которых определяется тремя другими полями:

-   `name`: Имя библиотеки. По соглашению, вы должны добавить суффикс `Spec`.
-   `type`: Тип модуля, содержащегося в этом пакете. В данном случае это Fabric Native Component, поэтому следует использовать значение `components`.
-   `jsSrcsDir`: относительный путь для доступа к спецификации `js`, которая анализируется **Codegen**.

### iOS: Создание файла `.podspec`.

Для iOS вам нужно будет создать файл `rtn-centered-text.podspec`, который определит модуль как зависимость для вашего приложения. Он будет находиться в корне `RTNCenteredText`, рядом с папкой `ios`.

Файл будет выглядеть следующим образом:

```ruby title="rtn-centered-text.podspec"
require "json"

package = JSON.parse(File.read(File.join(__dir__, "package.json")))

Pod::Spec.new do |s|
  s.name            = "rtn-centered-text"
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

Файл `.podspec` должен быть дочерним по отношению к файлу `package.json`, а его имя — то, которое мы задали в свойстве `name` файла `package.json`: `rtn-centered-text`.

В первой части файла подготавливаются некоторые переменные, которые мы используем на протяжении всего файла. Затем идет раздел, содержащий некоторую информацию, используемую для конфигурирования стручка, такую как его имя, версия и описание.

Все требования для новой архитектуры были заключены в [`install_modules_dependencies`](https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L198). Она заботится об установке соответствующих зависимостей в зависимости от того, какая архитектура включена в данный момент. Она также автоматически устанавливает зависимость `React-Core` в старой архитектуре.

### Android: `build.gradle` и класс `ReactPackage`.

Чтобы подготовить Android к запуску **Codegen**, необходимо:

1. Обновить файл `build.gradle`.
2. Создать класс Java/Kotlin, реализующий интерфейс `ReactPackage`.

По завершении этих шагов папка `android` должна выглядеть следующим образом:

```title="Android Folder Structure"
android
├── build.gradle
└── src
    └── main
        └── java
            └── com
                └── rtncenteredtext
                    └── CenteredTextPackage.java
```

#### Файл `build.gradle`.

Сначала создайте файл `build.gradle` в папке `android` со следующим содержимым:

=== "Java"

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
    namespace "com.rtncenteredtext"

    defaultConfig {
    	minSdkVersion safeExtGet('minSdkVersion', 21)
    	targetSdkVersion safeExtGet('targetSdkVersion', 33)
    	buildConfigField("boolean", "IS_NEW_ARCHITECTURE_ENABLED", "true")
    }
    }

    repositories {
    mavenCentral()
    google()
    }

    dependencies {
    implementation 'com.facebook.react:react-native'
    }
    ```

=== "Kotlin"

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
    namespace "com.rtncenteredtext"

    defaultConfig {
    	minSdkVersion safeExtGet('minSdkVersion', 21)
    	targetSdkVersion safeExtGet('targetSdkVersion', 33)
    	buildConfigField("boolean", "IS_NEW_ARCHITECTURE_ENABLED", "true")
    }
    }

    repositories {
    mavenCentral()
    google()
    }

    dependencies {
    implementation 'com.facebook.react:react-native'
    }
    ```

#### Класс `ReactPackage`

Затем вам нужен класс, реализующий интерфейс `ReactPackage`. Для запуска процесса **Codegen** не обязательно полностью реализовывать класс Package: достаточно пустой реализации, чтобы приложение приняло модуль как правильную зависимость React Native и попыталось сгенерировать код подмостков.

Создайте папку `android/src/main/java/com/rtncenteredtext` и внутри нее создайте файл `CenteredTextPackage.java`.

=== "Java"

    ```java title="CenteredTextPackage.java"
    package com.rtncenteredtext;

    import com.facebook.react.ReactPackage;
    import com.facebook.react.bridge.NativeModule;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.uimanager.ViewManager;

    import java.util.Collections;
    import java.util.List;

    public class CenteredTextPackage implements ReactPackage {

    	@Override
    	public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    		return Collections.emptyList();
    	}

    	@Override
    	public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    		return Collections.emptyList();
    	}

    }
    ```

=== "Kotlin"

    ```kotlin title="CenteredTextPackage.kt"
    package com.rtncenteredtext

    import com.facebook.react.ReactPackage
    import com.facebook.react.bridge.NativeModule
    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.uimanager.ViewManager

    class CenteredTextPackage : ReactPackage {
    override fun createViewManagers(reactContext: ReactApplicationContext): List<ViewManager<*, *>> =
    	emptyList()

    override fun createNativeModules(reactContext: ReactApplicationContext): List<NativeModule> =
    	emptyList()
    }
    ```

Интерфейс `ReactPackage` используется React Native для понимания того, какие нативные классы приложение должно использовать для `ViewManager` и `Native Modules`, экспортируемых библиотекой.

## 4. Нативный код

Последний шаг требует написания нативного кода для подключения JavaScript-части компонента к тому, что предлагается платформами. Этот процесс требует двух основных шагов:

1.  Запустите **Codegen**, чтобы посмотреть, что будет сгенерировано.
2.  Написать нативный код, который заставит это работать.

При разработке приложения React Native, использующего нативный компонент Fabric, ответственность за генерацию кода с помощью **Codegen** лежит на приложении. Однако при разработке Fabric Component в качестве библиотеки необходимо ссылаться на сгенерированный код, и полезно посмотреть, что будет сгенерировано приложением.

В качестве первого шага для iOS и Android в этом руководстве показано, как вручную выполнить сценарии, используемые **Codegen** для генерации необходимого кода. Дополнительную информацию о **Codegen** можно найти [здесь](the-new-architecture-pillars-codegen.md).

!!!warning ""

    Код, сгенерированный **Codegen** на этом шаге, не должен быть зафиксирован в системе версий. Приложения React Native могут генерировать код во время сборки приложения. Это позволяет приложению убедиться, что для всех библиотек сгенерирован код для правильной версии React Native.

### iOS

#### Генерация кода — iOS

Чтобы запустить Codegen для платформы iOS, откройте терминал и выполните следующую команду:

```sh
cd MyApp
yarn add ../RTNCenteredText
cd ..
node MyApp/node_modules/react-native/scripts/generate-codegen-artifacts.js \
  --path MyApp/ \
  --outputPath RTNCenteredText/generated/
```

Этот сценарий сначала добавляет модуль `RTNCenteredText` в приложение с помощью `yarn add`. Затем он вызывает **Codegen** с помощью сценария `generate-codegen-artifacts.js`.

Опция `--path` указывает путь к приложению, а опция `--outputPath` говорит скрипту, куда выводить сгенерированный код.

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
            └── react
                └── renderer
                    └── components
                        ├── RTNCenteredTextSpecs
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

Соответствующий путь для компонента — `generated/build/generated/ios/react/renderer/components/RTNCenteredTextSpecs`.
Эта папка содержит весь сгенерированный код, необходимый для вашего Компонента.

Более подробную информацию о сгенерированных файлах смотрите в разделе [Codegen](the-new-architecture-pillars-codegen.md).

!!!note ""

    При генерации кода строительных лесов с помощью **Codegen** iOS не очищает папку `build` автоматически. Если вы, например, изменили имя Spec, а затем снова запустили **Codegen**, старые файлы будут сохранены.

    Если это произойдет, не забудьте удалить папку `build` перед повторным запуском **Codegen**.

```
cd MyApp/ios
rm -rf build
```

#### Напишите нативный код для iOS

Теперь, когда код строительных лесов сгенерирован, пришло время написать нативный код для вашего Fabric-компонента.

Вам нужно создать три файла в папке `RTNCenteredText/ios`:

1.  `RTNCenteredTextManager.mm`, файл Objective-C++, который объявляет, что экспортирует компонент.
2.  `RTNCenteredText.h`, заголовочный файл для фактического представления.
3.  `RTNCenteredText.mm`, реализация представления.

##### RTNCenteredTextManager.mm

```objc title="RTNCenteredTextManager.mm"
#import <React/RCTLog.h>
#import <React/RCTUIManager.h>
#import <React/RCTViewManager.h>

@interface RTNCenteredTextManager : RCTViewManager
@end

@implementation RTNCenteredTextManager

RCT_EXPORT_MODULE(RTNCenteredText)

RCT_EXPORT_VIEW_PROPERTY(text, NSString)

@end
```

Этот файл является менеджером для компонента Fabric Native. Время выполнения React Native использует объекты менеджера для регистрации модулей, свойств и методов, чтобы сделать их доступными для стороны JavaScript.

Наиболее важным является вызов `RCT_EXPORT_MODULE`, который необходим для экспорта модуля, чтобы Fabric мог получить и инстанцировать его.

Затем необходимо раскрыть свойство `text` для нативного компонента Fabric. Это делается с помощью макроса `RCT_EXPORT_VIEW_PROPERTY`, указывающего имя и тип.

!!!info ""

    Существуют и другие макросы, которые можно использовать для экспорта пользовательских свойств, эмиттеров и других конструкций. Код, определяющий их, можно посмотреть [здесь](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Views/RCTViewManager.h).

##### RTNCenteredText.h

```objc title="RTNCenteredText.h"
#import <React/RCTViewComponentView.h>
#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface RTNCenteredText : RCTViewComponentView

@end

NS_ASSUME_NONNULL_END
```

Этот файл определяет интерфейс для представления `RTNCenteredText`. Здесь вы можете добавить любой собственный метод, который вы захотите вызвать у представления. В данном руководстве вам ничего не нужно, поэтому интерфейс пуст.

##### RTNCenteredText.mm

```cpp title="RTNCenteredText.mm"
#import "RTNCenteredText.h"

#import <react/renderer/components/RTNCenteredTextSpecs/ComponentDescriptors.h>
#import <react/renderer/components/RTNCenteredTextSpecs/EventEmitters.h>
#import <react/renderer/components/RTNCenteredTextSpecs/Props.h>
#import <react/renderer/components/RTNCenteredTextSpecs/RCTComponentViewHelpers.h>

#import "RCTFabricComponentsPlugins.h"

using namespace facebook::react;

@interface RTNCenteredText () <RCTRTNCenteredTextViewProtocol>
@end

@implementation RTNCenteredText {
  UIView *_view;
  UILabel *_label;
}

+ (ComponentDescriptorProvider)componentDescriptorProvider
{
  return concreteComponentDescriptorProvider<RTNCenteredTextComponentDescriptor>();
}

- (instancetype)initWithFrame:(CGRect)frame
{
  if (self = [super initWithFrame:frame]) {
    static const auto defaultProps = std::make_shared<const RTNCenteredTextProps>();
    _props = defaultProps;

    _view = [[UIView alloc] init];
    _view.backgroundColor = [UIColor redColor];

    _label = [[UILabel alloc] init];
    _label.text = @"Initial value";
    [_view addSubview:_label];

    _label.translatesAutoresizingMaskIntoConstraints = false;
    [NSLayoutConstraint activateConstraints:@[
      [_label.leadingAnchor constraintEqualToAnchor:_view.leadingAnchor],
      [_label.topAnchor constraintEqualToAnchor:_view.topAnchor],
      [_label.trailingAnchor constraintEqualToAnchor:_view.trailingAnchor],
      [_label.bottomAnchor constraintEqualToAnchor:_view.bottomAnchor],
    ]];

    _label.textAlignment = NSTextAlignmentCenter;

    self.contentView = _view;
  }

  return self;
}

- (void)updateProps:(Props::Shared const &)props oldProps:(Props::Shared const &)oldProps
{
  const auto &oldViewProps = *std::static_pointer_cast<RTNCenteredTextProps const>(_props);
  const auto &newViewProps = *std::static_pointer_cast<RTNCenteredTextProps const>(props);

  if (oldViewProps.text != newViewProps.text) {
    _label.text = [[NSString alloc] initWithCString:newViewProps.text.c_str() encoding:NSASCIIStringEncoding];
  }

  [super updateProps:props oldProps:oldProps];
}

@end

Class<RCTComponentViewProtocol> RTNCenteredTextCls(void)
{
  return RTNCenteredText.class;
}
```

Этот файл содержит фактическую реализацию представления.

Он начинается с некоторых импортов, которые требуют от вас чтения файлов, сгенерированных **Codegen**.

Компонент должен соответствовать определенному протоколу, сгенерированному **Codegen**, в данном случае `RCTRTNCenteredTextViewProtocol`.

Затем в файле определяется статический метод `(ComponentDescriptorProvider)componentDescriptorProvider`, который Fabric использует для получения поставщика дескрипторов для инстанцирования объекта.

Затем находится конструктор представления: метод `init`. В этом методе важно создать структуру `defaultProps`, используя тип `RTNCenteredTextProps` из **Codegen**. Ее нужно присвоить частному `_props`, чтобы правильно инициализировать Fabric Native Component. Оставшаяся часть инициализатора — это стандартный код Objective-C для создания представлений и их компоновки с помощью AutoLayout.

Последние две части — это метод `updateProps` и метод `RTNCenteredTextCls`.

Метод `updateProps` вызывается Fabric каждый раз, когда реквизит изменяется в JavaScript. Реквизиты, переданные в качестве параметров, преобразуются в соответствующий тип `RTNCenteredTextProps`, а затем используются для обновления родного кода, если это необходимо. Обратите внимание, что метод суперкласса `[super updateProps]` должен быть вызван в качестве последнего оператора этого метода; иначе структура `props` и `oldProps` будут иметь одинаковые значения, и вы не сможете использовать их для принятия решений и обновления компонента.

Наконец, `RTNCenteredTextCls` — это еще один статический метод, используемый для получения правильного экземпляра класса во время выполнения.

!!!warning ""

    В отличие от Legacy Native Components, Fabric требует ручной реализации метода `updateProps`. Недостаточно экспортировать свойства с помощью макросов `RCT_EXPORT_XXX` и `RCT_REMAP_XXX`.

### Android

Android следует некоторым шагам, схожим с iOS. Вы должны сгенерировать код, а затем написать нативный код, чтобы он работал.

#### Генерация кода — Android

Чтобы сгенерировать код, необходимо вручную вызвать **Codegen**. Это делается аналогично тому, что нужно сделать для iOS: сначала нужно добавить пакет в приложение, а затем вызвать скрипт.

```sh title="Running Codegen for Android"
cd MyApp
yarn add ../RTNCenteredText
cd android
./gradlew generateCodegenArtifactsFromSchema
```

Этот сценарий сначала добавляет пакет в приложение, как это делается в iOS. Затем, после перемещения в папку `android`, он вызывает задачу Gradle для генерации кода scaffolding.

!!!note ""

    Чтобы запустить **Codegen**, необходимо включить **Новую архитектуру** в приложении Android. Это можно сделать, открыв файлы `gradle.properties` и переключив свойство `newArchEnabled` с `false` на `true`.

Сгенерированный код хранится в папке `MyApp/node_modules/rtn-centered-text/android/build/generated/source/codegen` и имеет такую структуру:

```title="Android generated code"
codegen
├── java
│   └── com
│       └── facebook
│           └── react
│               └── viewmanagers
│                   ├── RTNCenteredTextManagerDelegate.java
│                   └── RTNCenteredTextManagerInterface.java
├── jni
│   ├── Android.mk
│   ├── CMakeLists.txt
│   ├── RTNCenteredText-generated.cpp
│   ├── RTNCenteredText.h
│   └── react
│       └── renderer
│           └── components
│               └── RTNCenteredText
│                   ├── ComponentDescriptors.h
│                   ├── EventEmitters.cpp
│                   ├── EventEmitters.h
│                   ├── Props.cpp
│                   ├── Props.h
│                   ├── ShadowNodes.cpp
│                   └── ShadowNodes.h
└── schema.json
```

Вы можете видеть, что содержимое папки `codegen/jni/react/renderer/components/RTNCenteredTextSpecs` похоже на файлы, созданные аналогом для iOS. Файлы `Android.mk` и `CMakeList.txt` настраивают Fabric Native Component в приложении, а файлы `RTNCenteredTextManagerDelegate.java` и `RTNCenteredTextManagerInterface.java` предназначены для использования в вашем менеджере.

Более подробную информацию о сгенерированных файлах смотрите в разделе [Codegen](./pillars-codegen).

#### Написание нативного кода Android

Нативный код для Android-части Fabric Native Components состоит из трех частей:

1.  `CenteredText.java`, который представляет фактическое представление.
2.  `CenteredTextManager.java` для инстанцирования представления.
3.  Наконец, необходимо заполнить реализацию `CenteredTextPackage.java`, созданную в предыдущем шаге.

Окончательная структура библиотеки Android должна выглядеть следующим образом.

```title="Android Folder Structure"
android
├── build.gradle
└── src
    └── main
        └── java
            └── com
                └── rtncenteredtext
                    ├── CenteredText.java
                    ├── CenteredTextManager.java
                    └── CenteredTextPackage.java
```

##### CenteredText.java

=== "Java"

    ```java title="CenteredText.java"
    package com.rtncenteredtext;

    import androidx.annotation.Nullable;
    import android.content.Context;
    import android.util.AttributeSet;
    import android.graphics.Color;

    import android.widget.TextView;
    import android.view.Gravity;

    public class CenteredText extends TextView {

    	public CenteredText(Context context) {
    		super(context);
    		this.configureComponent();
    	}

    	public CenteredText(Context context, @Nullable AttributeSet attrs) {
    		super(context, attrs);
    		this.configureComponent();
    	}

    	public CenteredText(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
    		super(context, attrs, defStyleAttr);
    		this.configureComponent();
    	}

    	private void configureComponent() {
    		this.setBackgroundColor(Color.RED);
    		this.setGravity(Gravity.CENTER_HORIZONTAL);
    	}
    }
    ```

=== "Kotlin"

    ```kotlin title="CenteredText.kt"
    package com.rtncenteredtext;

    import android.content.Context
    import android.graphics.Color
    import android.util.AttributeSet
    import android.view.Gravity
    import android.widget.TextView

    class CenteredText : TextView {
    constructor(context: Context?) : super(context) {
    	configureComponent()
    }

    constructor(context: Context?, attrs: AttributeSet?) : super(context, attrs) {
    	configureComponent()
    }

    constructor(context: Context?, attrs: AttributeSet?, defStyleAttr: Int) : super(context, attrs, defStyleAttr) {
    	configureComponent()
    }

    private fun configureComponent() {
    	setBackgroundColor(Color.RED)
    	gravity = Gravity.CENTER_HORIZONTAL
    }
    }
    ```

Этот класс представляет собой фактическое представление, которое Android будет отображать на экране. Он наследуется от `TextView` и настраивает основные аспекты себя с помощью частной функции `configureComponent()`.

##### CenteredTextManager.java

=== "Java"

    ```java title="CenteredTextManager.java"
    package com.rtncenteredtext;

    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;

    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.module.annotations.ReactModule;
    import com.facebook.react.uimanager.SimpleViewManager;
    import com.facebook.react.uimanager.ThemedReactContext;
    import com.facebook.react.uimanager.ViewManagerDelegate;
    import com.facebook.react.uimanager.annotations.ReactProp;
    import com.facebook.react.viewmanagers.RTNCenteredTextManagerInterface;
    import com.facebook.react.viewmanagers.RTNCenteredTextManagerDelegate;

    @ReactModule(name = CenteredTextManager.NAME)
    public class CenteredTextManager extends SimpleViewManager<CenteredText>
    		implements RTNCenteredTextManagerInterface<CenteredText> {

    	private final ViewManagerDelegate<CenteredText> mDelegate;

    	static final String NAME = "RTNCenteredText";

    	public CenteredTextManager(ReactApplicationContext context) {
    		mDelegate = new RTNCenteredTextManagerDelegate<>(this);
    	}

    	@Nullable
    	@Override
    	protected ViewManagerDelegate<CenteredText> getDelegate() {
    		return mDelegate;
    	}

    	@NonNull
    	@Override
    	public String getName() {
    		return CenteredTextManager.NAME;
    	}

    	@NonNull
    	@Override
    	protected CenteredText createViewInstance(@NonNull ThemedReactContext context) {
    		return new CenteredText(context);
    	}

    	@Override
    	@ReactProp(name = "text")
    	public void setText(CenteredText view, @Nullable String text) {
    		view.setText(text);
    	}
    }
    ```

=== "Kotlin"

    ```kotlin title="CenteredTextManager.kt"
    package com.rtncenteredtext

    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.module.annotations.ReactModule
    import com.facebook.react.uimanager.SimpleViewManager
    import com.facebook.react.uimanager.ThemedReactContext
    import com.facebook.react.uimanager.ViewManagerDelegate
    import com.facebook.react.uimanager.annotations.ReactProp
    import com.facebook.react.viewmanagers.RTNCenteredTextManagerInterface
    import com.facebook.react.viewmanagers.RTNCenteredTextManagerDelegate

    @ReactModule(name = CenteredTextManager.NAME)
    class CenteredTextManager(context: ReactApplicationContext) : SimpleViewManager<CenteredText>(), RTNCenteredTextManagerInterface<CenteredText> {
    private val delegate: RTNCenteredTextManagerDelegate<CenteredText> = RTNCenteredTextManagerDelegate(this)

    override fun getDelegate(): ViewManagerDelegate<CenteredText> = delegate

    override fun getName(): String = NAME

    override fun createViewInstance(context: ThemedReactContext): CenteredText = CenteredText(context)

    @ReactProp(name = "text")
    override fun setText(view: CenteredText, text: String?) {
    	view.text = text
    }

    companion object {
    	const val NAME = "RTNCenteredText"
    }
    }
    ```

`CenteredTextManager` — это класс, используемый React Native для инстанцирования нативного компонента. Это класс, который реализует интерфейсы, созданные **Codegen** (см. интерфейс `RTNCenteredTextManagerInterface` в пункте `implements`), и использует класс `RTNCenteredTextManagerDelegate`.

Он также отвечает за экспорт всех конструкций, необходимых React Native: сам класс аннотирован `@ReactModule`, а метод `setText` аннотирован `@ReactProp`.

##### CenteredTextPackage.java

Наконец, откройте файл `CenteredTextPackage.java` в папке `android/src/main/java/com/rtncenteredtext` и обновите его следующими строками

=== "Java"

    ```diff title="CenteredTextPackage.java update"
    package com.rtncenteredtext;

    import com.facebook.react.ReactPackage;
    import com.facebook.react.bridge.NativeModule;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.uimanager.ViewManager;

    import java.util.Collections;
    import java.util.List;

    public class CenteredTextPackage implements ReactPackage {

    	@Override
    	public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    +        return Collections.singletonList(new CenteredTextManager(reactContext));
    	}

    	@Override
    	public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    		return Collections.emptyList();
    	}

    }
    ```

=== "Kotlin"

    ```diff title="CenteredTextPackage.kt update"
    package com.rtncenteredtext

    import com.facebook.react.ReactPackage
    import com.facebook.react.bridge.NativeModule
    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.uimanager.ViewManager

    class CenteredTextPackage : ReactPackage {
    override fun createViewManagers(reactContext: ReactApplicationContext): List<ViewManager<*, *>> =
    -   emptyList()
    +   listOf(CenteredTextManager(reactContext))

    override fun createNativeModules(reactContext: ReactApplicationContext): List<NativeModule> =
    	emptyList()
    }
    ```

Добавленные строки создают новый объект `RTNCenteredTextManager`, чтобы среда выполнения React Native могла использовать его для рендеринга нашего Fabric Native Component.

## 5. Добавление компонента Fabric Native в ваше приложение

Это последний шаг, чтобы наконец-то увидеть, как ваш Fabric Native Component работает в вашем приложении.

### Shared

Прежде всего, необходимо добавить пакет NPM, содержащий компонент, в приложение. Это можно сделать с помощью следующей команды:

```sh
cd MyApp
yarn add ../RTNCenteredText
```

Эта команда добавляет компонент `RTNCenteredText` в `node_modules` вашего приложения.

Если пакет был ранее добавлен в ваше приложение, вам нужно будет обновить его:

```sh
cd MyApp
yarn upgrade rtn-centered-text
```

### iOS

Затем необходимо установить новые зависимости в ваш проект iOS. Для этого необходимо выполнить следующие команды:

```sh
cd ios
RCT_NEW_ARCH_ENABLED=1 bundle exec pod install
```

Эта команда устанавливает зависимости iOS для проекта. Флаг `RCT_NEW_ARCH_ENABLED=1` указывает **Cocoapods**, что ему необходимо выполнить некоторые дополнительные операции для запуска **Codegen**.

!!!note ""

    Возможно, вам придется запустить `bundle install` один раз, прежде чем вы сможете использовать `RCT_NEW_ARCH_ENABLED=1 bundle exec pod install`. Вам больше не понадобится запускать `bundle install`, если только вам не нужно изменить зависимости ruby.

### Android

Конфигурация Android требует включения **Новой архитектуры**.

1.  Откройте файл `android/gradle.properties`.
2.  Прокрутите вниз до конца файла и переключите свойство `newArchEnabled` с `false` на `true`.

### JavaScript

Наконец, вы можете использовать компонент в своем приложении JavaScript.

=== "Flow"

    ```typescript title="App.js"
    /**
     * Sample React Native App
     * https://github.com/facebook/react-native
     *
     * @format
     * @flow strict-local
     */
    import React from 'react';
    import type { Node } from 'react';
    import RTNCenteredText from 'rtn-centered-text/js/RTNCenteredTextNativeComponent';

    const App: () => Node = () => {
    	return (
    		<RTNCenteredText
    			text="Hello World!"
    			style={{ width: '100%', height: 30 }}
    		/>
    	);
    };
    export default App;
    ```

=== "TypeScript"

    ```typescript title="App.tsx"
    /**
     * Sample React Native App
     * https://github.com/facebook/react-native
     *
     * @format
     */
    import React from 'react';
    import RTNCenteredText from 'rtn-centered-text/js/RTNCenteredTextNativeComponent';

    const App: () => JSX.Element = () => {
    	return (
    		<RTNCenteredText
    			text="Hello World!"
    			style={{ width: '100%', height: 30 }}
    		/>
    	);
    };
    export default App;
    ```

Теперь вы можете запустить приложение React Native и увидеть ваш компонент на экране.
