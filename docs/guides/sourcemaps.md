# Source Maps

Карты исходников позволяют сопоставить преобразованный файл с исходным файлом. Основное назначение карт исходных текстов - облегчить отладку и помочь в исследовании проблем, возникающих в релизных сборках.

Без исходных карт при возникновении ошибки в сборке релиза вы увидите стектрейс следующего вида:

```text
TypeError: Cannot read property 'data' of undefined
  at anonymous(app:///index.android.bundle:1:4277021)
  at call(native)
  at p(app:///index.android.bundle:1:227785)
```

При создании карт исходного кода трассировка стека будет включать путь, имя файла и номер строки исходного файла:

```text
TypeError: Cannot read property 'data' of undefined
  at anonymous(src/modules/notifications/Permission.js:15:requestNotificationPermission)
  at call(native)
  at p(node_modules/regenerator-runtime/runtime.js:64:Generator)
```

Это позволяет сортировать проблемы с релизом, используя расшифровываемую трассировку стека.

Следуйте приведенным ниже инструкциям, чтобы начать работу с картами источников.

## Включите карты источников на Android

### Hermes

!!!info ""

    По умолчанию карты исходников строятся в режиме Release, если не установлен `hermesFlagsRelease`. В этом случае карты источников должны быть включены.

Для этого убедитесь, что в файле `android/app/build.gradle` вашего приложения установлено следующее.

```groovy
project.ext.react = [
    enableHermes: true,
    hermesFlagsRelease: ["-O", "-output-source-map"], // plus whichever flag was required to set this away from default
]
```

Если все сделано правильно, вы должны увидеть местоположение исходной карты во время вывода Metro build.

```text
Writing bundle output to:, android/app/build/generated/assets/react/release/index.android.bundle
Writing sourcemap output to:, android/app/build/intermediates/sourcemaps/react/release/index.android.bundle.packager.map
```

Сборки разработки не создают пакет и поэтому уже имеют символы, но если сборка разработки имеет пакет, вы можете использовать `hermesFlagsDebug`, как описано выше, чтобы включить карты исходников.

## Включите карты источников на iOS

По умолчанию карты источников отключены. Чтобы включить их, необходимо определить переменную окружения `SOURCEMAP_FILE`.

Для этого в Xcode перейдите к этапу Build Phase - "Bundle React Native code and images".

В верхней части файла рядом с другими экспортами добавьте запись для `SOURCEMAP_FILE` в предпочтительное местоположение и имя. Как показано ниже:

```
export SOURCEMAP_FILE="$(pwd)/../main.jsbundle.map";

export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

Если все сделано правильно, вы должны увидеть местоположение исходной карты во время вывода Metro build.

```text
Writing bundle output to:, Build/Intermediates.noindex/ArchiveIntermediates/application/BuildProductsPath/Release-iphoneos/main.jsbundle
Writing sourcemap output to:, Build/Intermediates.noindex/ArchiveIntermediates/application/BuildProductsPath/Release-iphoneos/main.jsbundle.map
```

## Символизация вручную

!!!info ""

    Вы можете прочитать о ручной символизации трассы стека на странице [symbolication](symbolication.md).
