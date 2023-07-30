---
description: На этой странице представлен обзор как построены Hermes и React Native
---

# Bundled Hermes

На этой странице представлен обзор как построены Hermes и React Native.

Если вы ищете инструкции по использованию Hermes в вашем приложении, вы можете найти их на этой странице: [использование Hermes](../guides/hermes.md)

!!!warning "Осторожно"

    Пожалуйста, обратите внимание, что эта страница является техническим погружением и предназначена для пользователей, которые создают расширения поверх Hermes или React Native. Обычным пользователям React Native не нужно знать глубокую информацию о взаимодействии React Native и Hermes.

## Что такое 'Bundled Hermes'

Начиная с React Native 0.69.0, каждая версия React Native будет **собираться вместе** с версией Hermes. Мы называем эту модель распространения **Bundled Hermes**.

Начиная с версии 0.69, у вас всегда будет JS-движок, который был создан и протестирован вместе с каждой версией React Native, которую вы можете использовать.

## Почему мы перешли на 'Bundled Hermes'

Исторически сложилось так, что React Native и Hermes следовали двум **различным процессам выпуска** с различной версионностью. Наличие разных релизов с разными номерами создавало путаницу в экосистеме OSS, где было неясно, совместима ли конкретная версия Hermes с конкретной версией React Native (например, вам нужно было знать, что Hermes 0.11.0 совместим только с React Native 0.68.0 и т.д.).

И Hermes, и React Native используют код JSI ([Hermes здесь](https://github.com/facebook/hermes/tree/main/API/jsi/jsi) и [React Native здесь](https://github.com/facebook/react-native/tree/main/ReactCommon/jsi/jsi)). Если две копии JSI рассинхронизируются, сборка Hermes будет несовместима со сборкой React Native. Подробнее об этом [проблема несовместимости ABI здесь](https://github.com/react-native-community/discussions-and-proposals/issues/257).

Чтобы решить эту проблему, мы расширили процесс выпуска React Native для загрузки и сборки Hermes и убедились, что при сборке Hermes используется только одна копия JSI.

Благодаря этому мы можем выпускать версию Hermes всякий раз, когда выпускаем версию React Native, и быть уверенными, что созданный нами движок Hermes **полностью совместим** с выпускаемой нами версией React Native. Мы отправляем эту версию Hermes вместе с версией React Native, которую мы делаем, отсюда и название _Bundled Hermes_.

## Как это повлияет на разработчиков приложений

Как уже упоминалось во введении, если вы являетесь разработчиком приложений, это изменение **не должно затронуть** вас напрямую.

В следующих параграфах описано, какие изменения мы сделали под капотом, и объяснены некоторые обоснования, в целях прозрачности.

### Пользователи iOS

На iOS мы переместили используемый вами `hermes-engine`.

До выхода React Native 0.69 пользователи загружали pod (здесь вы можете найти [podspec](https://github.com/CocoaPods/Specs/blob/master/Specs/5/d/0/hermes-engine/0.11.0/hermes-engine.podspec.json)).

В React Native 0.69 пользователи вместо этого будут использовать podspec, который определен в файле `sdks/hermes-engine/hermes-engine.podspec` в NPM-пакете `react-native`.
Этот podspec полагается на предварительно собранный tarball Hermes, который мы загружаем в Maven и на React Native GitHub Release, как часть процесса выпуска React Native (т.е. [см. активы этого выпуска](https://github.com/facebook/react-native/releases/tag/v0.70.4)).

### Android Users

На Android мы собираемся обновить файл [`android/app/build.gradle`](https://github.com/facebook/react-native/blob/main/template/android/app/build.gradle) в шаблоне по умолчанию следующим образом:

```diff
dependencies {
    // ...

    if (enableHermes) {
+       implementation("com.facebook.react:hermes-engine:+") {
+           exclude group:'com.facebook.fbjni'
+       }
-       def hermesPath = "../../node_modules/hermes-engine/android/";
-       debugImplementation files(hermesPath + "hermes-debug.aar")
-       releaseImplementation files(hermesPath + "hermes-release.aar")
    } else {
        implementation jscFlavor
    }
}
```

До версии React Native 0.69 пользователи будут использовать `hermes-debug.aar` и `hermes-release.aar` из NPM-пакета `hermes-engine`.

В версии React Native 0.69 пользователи будут использовать многовариантные артефакты Android, доступные в папке `android/com/facebook/react/hermes-engine/` в NPM-пакете `react-native`.

Также обратите внимание, что мы собираемся [убрать зависимость](https://github.com/facebook/react-native/blob/c418bf4c8fe8bf97273e3a64211eaa38d836e0a0/package.json#L105) от `hermes-engine` полностью в одной из будущих версий React Native.

#### Пользователи Android на новой архитектуре

В связи с характером нашей настройки сборки нативного кода (т.е. как мы используем NDK), пользователи новой архитектуры будут **собирать Hermes из исходников**.

Это выравнивает механизм сборки React Native и Hermes для пользователей на новой архитектуре (они будут собирать оба фреймворка из исходников).
Это означает, что такие пользователи Android могут испытать снижение производительности во время сборки при первой сборке.

Вы можете найти инструкции по оптимизации времени сборки и уменьшению влияния на вашу сборку на этой странице: [Speeding up your Build phase](../guides/build-speed.md).

#### Пользователи Android на новой архитектуре, построенной на Windows

Пользователям, создающим React Native App с новой архитектурой на машинах Windows, необходимо выполнить эти дополнительные шаги, чтобы сборка работала корректно:

-   Убедитесь, что [окружение настроено правильно](../guides/getting-started.md), с Android SDK и node.
-   Установите [cmake](https://community.chocolatey.org/packages/cmake) с помощью Chocolatey.
-   Установите либо:
    -   [Build Tools for Visual Studio 2022](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022).
    -   [Visual Studio 22 Community Edition](https://visualstudio.microsoft.com/vs/community/) — Достаточно выбрать только десктопную разработку на C++.
-   Убедитесь, что [Visual Studio Command Prompt](https://docs.microsoft.com/en-us/visualstudio/ide/reference/command-prompt-powershell?view=vs-2022) настроен правильно. Это необходимо для того, чтобы в командной строке была настроена соответствующая переменная окружения компилятора C++.
-   Запустите приложение с помощью `npx react-native run-android` внутри Visual Studio Command Prompt.

### Могут ли пользователи по-прежнему использовать другой движок?

Да, пользователи могут включать/выключать Hermes (с помощью переменной `enableHermes` на Android, `hermes_enabled` на iOS). Изменение 'Bundled Hermes' повлияет только на **как Hermes собирается и комплектуется** для вас.

Начиная с версии React Native 0.70, по умолчанию для `enableHermes`/`hermes_enabled` используется значение `true`.

## Как это повлияет на разработчиков контрибьюторов и расширений

Если вы являетесь соавтором React Native или создаете расширение поверх React Native или Hermes, пожалуйста, читайте дальше, поскольку мы объясним, как работает Bundled Hermes.

### Как работает Bundled Hermes под капотом?

Этот механизм основан на **загрузке tarball** с исходным кодом Hermes из репозитория `facebook/hermes` внутри репозитория `facebook/react-native`. У нас есть аналогичный механизм для других нативных зависимостей (Folly, Glog и т.д.), и мы хотим, чтобы Hermes следовал той же схеме.

При сборке React Native из `main` мы будем получать tarball `main` из facebook/hermes и собирать его как часть процесса сборки React Native.

При сборке React Native из релизной ветки (например, `0.69-stable`) мы будем использовать **тег** в репозитории Hermes для **синхронизации кода** между двумя репозиториями. Конкретное имя используемого тега будет храниться в файле `sdks/.hermesversion` внутри React Native в релизной ветке (например, [this is the file](https://github.com/facebook/react-native/blob/0.69-stable/sdks/.hermesversion) на релизной ветке 0.69).

В некотором смысле этот подход можно сравнить с **git-подмодулем**.

Если вы собираете поверх Hermes, вы можете полагаться на эти теги, чтобы понять, какая версия Hermes была использована при сборке React Native, поскольку версия React Native указана в имени тега (например, `hermes-2022-05-20-RNv0.69.0-ee8941b8874132b8f83e4486b63ed5c19fc3f111`).

#### Детали реализации Android

Чтобы реализовать это на Android, мы добавили новую сборку внутри `/ReactAndroid/hermes-engine` в React Native, которая позаботится о сборке Hermes и упаковке для потребления (См. [здесь для большего контекста](https://github.com/facebook/react-native/pull/33396)).

Теперь вы можете запустить сборку движка Hermes, вызвав команду:

```bash
// Build a debug version of Hermes
./gradlew :ReactAndroid:hermes-engine:assembleDebug
// Build a release version of Hermes
./gradlew :ReactAndroid:hermes-engine:assembleRelease
```

из ветки React Native `main`.

Вам не потребуется устанавливать дополнительные инструменты (такие как `cmake`, `ninja` или `python3`) на вашей машине, поскольку мы настроили сборку на использование NDK-версий этих инструментов.

На стороне потребителя Gradle мы также отправили небольшое улучшение: мы перешли от `releaseImplementation` и `debugImplementation` к `implementation`. Это стало возможным благодаря тому, что новый артефакт `hermes-engine` Android является **variant aware** и будет правильно сопоставлять отладочную сборку движка с отладочной сборкой вашего приложения. Здесь не требуется никакой пользовательской конфигурации (даже если вы используете `staging` или другие типы сборки).

Однако это сделало эту строку необходимой в шаблоне:

```
exclude group:'com.facebook.fbjni'
```

Это необходимо, поскольку React Native потребляет `fbjni`, используя подход без префаба (т.е. распаковывая `.aar` и извлекая `.so` файлы). Hermes-engine и другие библиотеки используют prefab для потребления fbjni. Мы рассматриваем возможность [решения этой проблемы](https://github.com/facebook/react-native/pull/33397) в будущем, чтобы импорт Hermes был единственным.

#### Детали реализации iOS

Реализация iOS полагается на ряд скриптов, которые находятся в следующих местах:

-   [`/scripts/hermes`](https://github.com/facebook/react-native/tree/main/scripts/hermes). Эти скрипты содержат логику для загрузки тарбола Hermes, его распаковки и настройки сборки iOS. Они вызываются во время `pod install`, если поле `hermes_enabled` установлено в `true`.
-   [`/sdks/hermes-engine`](https://github.com/facebook/react-native/tree/main/sdks/hermes-engine). Эти скрипты содержат логику сборки, которая фактически собирает Hermes. Они были скопированы и адаптированы из репозитория `facebook/hermes` для правильной работы в React Native. В частности, скрипты в папке `utils` отвечают за сборку Hermes для всех платформ Mac.

Hermes собирается в рамках задания `build_hermes_macos` на CircleCI. В качестве артефакта задание создаст тарбол, который будет загружен подспеком `hermes-engine` при использовании опубликованного релиза React Native ([вот пример артефактов, созданных для React Native 0.69 в `build_hermes_macos`](https://app.circleci.com/pipelines/github/facebook/react-native/13679/workflows/5172f8e4-6b02-4ccb-ab97-7cb954911fae/jobs/258701/artifacts)).

##### Готовый Hermes

Если для используемой версии React Native нет готовых артефактов (т.е. вы работаете с React Native из ветки `main`), то Hermes придется собирать из исходников. Сначала компилятор Hermes, `hermesc`, будет собран для macOS во время `pod install`, затем сам Hermes будет собран как часть конвейера сборки Xcode с помощью скрипта `build-hermes-xcode.sh`.

##### Сборка Hermes из источника

Hermes всегда собирается из исходного кода при использовании React Native из ветки `main`. Если вы используете стабильную версию React Native, вы можете заставить Hermes быть собранным из исходного кода, установив параметр `CI` в `true` при использовании CocoaPods: `CI=true pod install`.

##### Символы отладки

Предварительно собранные артефакты для Hermes по умолчанию не содержат отладочных символов (dSYMs). В будущем мы планируем распространять эти отладочные символы для каждого выпуска. До тех пор, если вам нужны отладочные символы для Hermes, вам нужно будет собрать Hermes из исходного кода. В каталоге сборки будет создан `hermes.framework.dSYM` вместе с каждым из фреймворков Hermes.

### Я боюсь, что эти изменения повлияют на меня

Мы хотели бы подчеркнуть, что это, по сути, организационное изменение _где_ собирается Hermes и _как_ синхронизируется код между двумя репозиториями. Это изменение должно быть полностью прозрачным для наших пользователей.

Раньше мы выпускали релиз Hermes для конкретной версии React Native (например, [`v0.11.0 for RN0.68.x`](https://github.com/facebook/hermes/releases/tag/v0.11.0)).

С 'Bundled Hermes' вы можете вместо этого полагаться на тег, который будет представлять версию, используемую при обрезании конкретной версии React Native.
