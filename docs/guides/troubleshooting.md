# Устранение неполадок

Вот некоторые общие проблемы, с которыми вы можете столкнуться при настройке React Native. Если вы столкнулись с чем-то, что не указано здесь, попробуйте [поиск проблемы на GitHub](https://github.com/facebook/react-native/issues/).

## Порт уже используется

Пакет [Metro bundler](https://facebook.github.io/metro/) работает на порту 8081. Если другой процесс уже использует этот порт, вы можете либо завершить этот процесс, либо изменить порт, который использует бандлер.

### Завершение процесса на порту 8081.

Выполните следующую команду, чтобы найти идентификатор процесса, который прослушивает порт 8081:

<!-- 0001.part.md -->

```shell
sudo lsof -i :8081
```

<!-- 0002.part.md -->

Затем выполните следующие действия, чтобы завершить процесс:

<!-- 0003.part.md -->

```shell
kill -9 <PID>
```

<!-- 0004.part.md -->

В Windows вы можете найти процесс, использующий порт 8081, используя [Resource Monitor](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows) и остановить его с помощью диспетчера задач.

### Использование порта, отличного от 8081

Вы можете настроить bundler на использование порта, отличного от 8081, с помощью параметра `port`:

<!-- 0005.part.md -->

```shell
npx react-native start --port=8088
```

<!-- 0006.part.md -->

Вам также нужно будет обновить свои приложения, чтобы загрузить пакет JavaScript с нового порта. При запуске на устройстве из Xcode это можно сделать, обновив вхождения `8081` на выбранный вами порт в файле `ios/__App_Name__.xcodeproj/project.pbxproj`.

## Ошибка блокировки NPM

Если вы столкнулись с ошибкой типа `npm WARN locking Error: EACCES` при использовании React Native CLI, попробуйте выполнить следующее:

<!-- 0007.part.md -->

```shell
sudo chown -R $USER ~/.npm
sudo chown -R $USER /usr/local/lib/node_modules
```

<!-- 0008.part.md -->

## Недостающие библиотеки для React

Если вы добавили React Native вручную в свой проект, убедитесь, что вы включили все соответствующие зависимости, которые вы используете, такие как `RCTText.xcodeproj`, `RCTImage.xcodeproj`. Далее, двоичные файлы, созданные на основе этих зависимостей, должны быть связаны с двоичным файлом вашего приложения. Используйте раздел `Linked Frameworks and Binaries` в настройках проекта Xcode. Более подробные шаги приведены здесь: [Linking Libraries](linking-libraries-ios.md#content).

Если вы используете CocoaPods, убедитесь, что вы добавили React вместе с подспецификациями в `Podfile`. Например, если вы используете API `<Text />`, `<Image />` и `fetch()`, вам нужно добавить их в ваш `Podfile`:

```
pod 'React', :path => '../node_modules/react-native', :subspecs => [
	'RCTText',
	'RCTImage',
	'RCTNetwork',
	'RCTWebSocket',
]
```

Далее убедитесь, что вы выполнили `pod install` и что в вашем проекте с установленным React был создан каталог `Pods/`. CocoaPods проинструктирует вас использовать сгенерированный файл `.xcworkspace`, чтобы впредь иметь возможность использовать эти установленные зависимости.

### React Native не компилируется при использовании в качестве CocoaPod.

Существует плагин CocoaPods под названием [cocoapods-fix-react-native](https://github.com/orta/cocoapods-fix-react-native), который обрабатывает любые потенциальные пост-исправления исходного кода из-за различий при использовании менеджера зависимостей.

### Слишком длинный список аргументов: рекурсивное расширение заголовка не удалось.

В настройках сборки проекта `User Search Header Paths` и `Header Search Paths` — это два конфига, которые указывают, где Xcode должен искать указанные в коде заголовочные файлы `#import`. Для Pods, CocoaPods использует массив определенных папок по умолчанию. Убедитесь, что этот конкретный конфиг не перезаписан, и что ни одна из настроенных папок не является слишком большой. Если одна из папок будет большой, Xcode попытается рекурсивно перебрать весь каталог и в какой-то момент выдаст ошибку.

Чтобы вернуть настройки сборки `User Search Header Paths` и `Header Search Paths` к значениям по умолчанию, установленным CocoaPods, выберите запись в панели Build Settings и нажмите delete. Это приведет к удалению пользовательского переопределения и возврату к значениям по умолчанию CocoaPod.

<!-- 0009.part.md -->

## Транспорты недоступны

React Native реализует полифилл для WebSockets. Эти [полифиллы](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Core/InitializeCore.js) инициализируются как часть модуля react-native, который вы включаете в свое приложение через `import React from 'react'`. Если вы загружаете другой модуль, требующий WebSockets, например [Firebase](https://github.com/facebook/react-native/issues/3645), обязательно загрузите/требуйте его после react-native:

```js
import React from 'react';
import Firebase from 'firebase';
```

## Исключение, не реагирующее на команду Shell Command

Если вы столкнулись с исключением ShellCommandUnresponsiveException, таким как:

```
Execution failed for task ':app:installDebug'.
	com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsponsiveException
```

Попробуйте [понизить версию Gradle до 1.2.3](https://github.com/facebook/react-native/issues/2720) в `android/build.gradle`.

## react-native init зависает

Если вы столкнулись с проблемой, когда запуск `npx react-native init` зависает в вашей системе, попробуйте запустить его снова в режиме verbose и обратитесь к [#2797](https://github.com/facebook/react-native/issues/2797) для выявления общих причин:

<!-- 0010.part.md -->

```shell
npx react-native init --verbose
```

<!-- 0011.part.md -->

Когда вы отлаживаете процесс или вам нужно узнать немного больше об ошибке, вы можете использовать опцию verbose, чтобы вывести больше журналов и информации для выяснения проблемы.

Выполните следующую команду в корневом каталоге.

<!-- 0012.part.md -->

```shell
npx react-native run-android --verbose
```

<!-- 0013.part.md -->

## Невозможно запустить менеджер пакетов react-native (на Linux)

### Случай 1: Ошибка "code": "ENOSPC", "errno": "ENOSPC".

Проблема вызвана количеством директорий, которые может контролировать [inotify](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers) (используется watchman в Linux). Чтобы решить эту проблему, выполните в окне терминала следующую команду

<!-- 0014.part.md -->

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

<!-- 0015.part.md -->

### Ошибка: spawnSync ./gradlew EACCES

Если вы столкнулись с проблемой, когда выполнение команды `npm run android` на macOS приводит к вышеуказанной ошибке, попробуйте выполнить команду `sudo chmod +x android/gradlew`, чтобы сделать файлы `gradlew` исполняемыми.

<!-- 0016.part.md -->
