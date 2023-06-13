

---
id: new-architecture-app-intro
title: Prerequisites for Applications
---

import NewArchitectureWarning from './\_markdown-new-architecture-warning.mdx';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem';

import constants from '@site/core/TabsConstants';

<NewArchitectureWarning/>

Есть несколько предварительных условий, которые должны быть выполнены до того, как Новая архитектура будет включена в вашем приложении.



## Обновление до последней версии React Native




React Native выпустила поддержку Новой архитектуры в релизе `0.68.0`.




Это руководство написано с расчетом на то, что вы используете [**последний** релиз React Native](https://github.com/facebook/react-native/releases/latest).




Вы можете найти инструкции по обновлению на странице [обновление до новых версий](/docs/upgrading).




## Использовать Hermes




Hermes - это JavaScript-движок с открытым исходным кодом, оптимизированный для React Native. Hermes включен по умолчанию, и вы должны явно отключить его, если хотите использовать JSC.




Мы настоятельно рекомендуем использовать Hermes в вашем приложении. С включенным Hermes вы можете использовать отладчик JavaScript в Flipper для прямой отладки вашего кода JavaScript.




Пожалуйста, [следуйте инструкциям на странице Hermes](hermes), чтобы узнать, как включить/выключить Hermes.




:::осторожно




**iOS:** Если вы откажетесь от использования Hermes, вам нужно будет заменить `HermesExecutorFactory` на `JSCExecutorFactory` во всех примерах, используемых в остальной части этого руководства.




:::




## iOS - сборка проекта




После обновления проекта вам необходимо внести несколько изменений:




1. Установите нужную версию iOS. Откройте `Podfile` и примените это изменение:

```diff
- platform :ios, '11.0'
+ platform :ios, '12.4'
```

2. Создайте файл `.xcode.env` для экспорта местоположения NODE_BINARY. Перейдите в папку `ios` и выполните эту команду:

```sh
echo 'export NODE_BINARY=$(command -v node)' > .xcode.env
```

Если вам это нужно, вы также можете открыть этот файл и заменить `$(command -v node)` на путь к исполняемому файлу node.
React Native также поддерживает локальную версию этого файла `.xcode.env.local`. Этот файл не синхронизируется с репозиторием, чтобы вы могли настроить свою локальную установку, если она отличается от установки непрерывной интеграции или командной установки.




2. Исправьте изменение API в файле `AppDelegate.m`. Откройте этот файл и примените это изменение:

```diff
#if DEBUG
-       return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];
+       return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index"];
#else
```

## iOS - использование Objective-C++ (расширение `.mm`)



Модули Turbo Native могут быть написаны с использованием Objective-C или C++. Чтобы поддержать оба варианта, все исходные файлы в пространстве пользовательского проекта, включающие код C++, должны использовать расширение файла `.mm`. Это расширение соответствует Objective-C++, варианту языка, который позволяет использовать комбинацию C++ и Objective-C в исходных файлах.




:::important




**Используйте Xcode для переименования существующих файлов**, чтобы обеспечить сохранение ссылок на файлы в вашем проекте. Возможно, вам потребуется очистить папку сборки (_Project → Clean Build Folder_) перед повторной сборкой приложения. Если файл переименован вне Xcode, вам может понадобиться щелкнуть на старой ссылке на файл `.m` и найти новый файл.




:::




Например, если вы используете шаблон, структура вашего проекта для iOS должна выглядеть следующим образом:

```
AppName
├── ...
├── ios
│   ├── Podfile
│   ├── Podfile.lock
│   ├── Pods
│   │   └── ...
│   ├── AppName
│   │   ├── AppDelegate.h
│   │   ├── AppDelegate.mm
│   │   ├── Images.xcassets
│   │   ├── Info.plist
│   │   ├── LaunchScreen.storyboard
│   │   └── main.m
│   ├── AppName.xcodeproj
│   ├── AppName.xcworkspace
│   ├── AppNameTests
│   └── build
└── ...
```

Все файлы `.m` во внутренней папке `AppName` должны быть переименованы из `.m` в `.mm`. Если у вас есть пакеты, содержащие код Objective-C на том же уровне папки `AppName`, их тоже следует переименовать из `.m` в `.mm`.



## iOS - Заставьте ваш AppDelegate соответствовать `RCTAppDelegate`.




Последним шагом в настройке iOS для новой архитектуры является расширение базового класса, предоставляемого React Native, под названием `RCTAppDelegate`.




Этот класс предоставляет базовую реализацию для всех необходимых функций новой архитектуры. Если вам нужно настроить некоторые из них, вы можете переопределить эти методы, вызвать `[super methodNameWith:parameters:];`, собрав возвращаемое значение, и настроить те части, которые вам нужно настроить.




1. Откройте файл `ios/<AppName>/AppDelegate.h` и обновите его следующим образом:

```diff
- #import <React/RCTBridgeDelegate.h>
+ #import <RCTAppDelegate.h>
#import <UIKit/UIKit.h>

- @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate>
+ @interface AppDelegate : RCTAppDelegate

- @property (nonatomic, strong) UIWindow *window;

@end
```

2. Откройте файл `ios/<AppName>/AppDelegate.mm` (помните, что сначала нужно переименовать `AppDelegate.m` в `AppDelegate.mm`) и замените его содержимое на следующее:

```objc
#import "AppDelegate.h"
#import <React/RCTBundleURLProvider.h>

@implementation AppDelegate
  - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  self.moduleName = @"NameOfTheApp";
  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}

- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
{
#if DEBUG
  return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index"];
#else
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
}

- (BOOL)concurrentRootEnabled
{
  return true;
}

@end
```

:::примечание
Имя `модуля` должно быть той же строкой, которая используется в вызове `[RCTRootView initWithBridge:moduleName:initialProperties]` в оригинальном файле `AppDelegate.mm`.

:::




## iOS - Запустите pod install

```bash
// Run pod install with the flags
RCT_NEW_ARCH_ENABLED=1 pod install
```

## Android - предварительные условия



Если вы успешно обновили свой проект до React Native `0.71.0`, вы **уже выполнили** все предварительные условия для использования новой архитектуры на Android.




Вам нужно будет только обновить файл `android/gradle.properties` следующим образом:

```diff
# Use this property to enable support to the new architecture.
# This will allow you to use TurboModules and the Fabric render in
# your application. You should enable this flag either if you want
# to write custom TurboModules/Fabric components OR use libraries that
# are providing them.
-newArchEnabled=false
+newArchEnabled=true
```

Вот и все!



Теперь пора запустить ваше приложение для Android, чтобы убедиться, что все работает правильно:

```bash
yarn react-native run-android
```

Теперь в журнале вашего терминала Metro вы увидите следующий журнал, подтверждающий, что Fabric работает правильно:

```
BUNDLE ./App.js
LOG Running "App" with {"fabric":true,"initialProps":{"concurrentRoot": "true"},"rootTag":1}
```

