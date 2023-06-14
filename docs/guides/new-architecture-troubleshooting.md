# Устранение неполадок

!!!warning "Внимание"

    Эта документация все еще является экспериментальной, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в обсуждении внутри рабочей группы для этой страницы.

    Более того, она содержит несколько ручных шагов. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

Эта страница содержит решения распространенных проблем, с которыми вы можете столкнуться при переходе на новую архитектуру.

## Проблемы сборки Xcode

Если сборка XCode Build завершилась неудачей с:

**Command PhaseScriptExecution failed with a nonzero exit code**.

Эта ошибка указывает на то, что сценарий codegen, внедренный в конвейер сборки Xcode, завершился раньше времени. Вы можете получить это либо для своей собственной библиотеки, либо для одной из основных библиотек RN (FBReactNativeSpec, rncore).

Откройте `~/Library/Developer/Xcode/DerivedData`. и найдите папку с именем вашего рабочего пространства Xcode ("RNTesterPods-AAAA", где "AAAA" - это строка символов). Внутри этой папки перейдите в Build → Intermediates.noindex → Pods.build → Debug-iphonesimulator (или эквивалент для вашего устройства iOS, если применимо). Внутри ищите папку с именем библиотеки codegen, в которой произошла ошибка сценария. Журналы фазы сценария можно найти в папке DerivedSources, в файле с именем `codegen-LibraryName.log`. Этот вывод журнала должен прояснить источник ошибки.

## CocoaPods и сброс узла

Интеграция CocoaPods будет часто обновляться по мере развертывания новой архитектуры, и после одного из таких изменений может оказаться, что ваше рабочее пространство находится в сломанном состоянии. Вы можете очистить любые изменения, связанные с кодогеном, выполнив некоторые из этих шагов:

1.  Выполните команду `pod deintegrate` в каталоге ios (или там, где находится ваш Podfile) и повторно выполните команду `pod install` (или `arch -x86_64 pod install`, в случае Mac M1).
2.  Удалите `Podfile.lock` и снова запустите `pod install` (или `arch -x86_64 pod install`, в случае Mac M1).
3.  Удалите `node_modules` и повторно запустите `yarn install`.
4.  Удалите артефакты codegen и повторно запустите `pod install` (или `arch -x86_64 pod install`, в случае Mac M1), затем очистите и соберите ваш проект Xcode.

## Folly Version

Так получилось, что версия Folly, используемая в вашем podspec, должна соответствовать версии, используемой в React Native на данный момент. Если после запуска `pod install` вы увидите следующую ошибку:

```
[!] CocoaPods could not find compatible versions for pod "RCT-Folly":
```

...возможно, у вас несоответствие версий. Посмотрите на свой файл `node_modules/react-native/React/FBReactNativeSpec/FBReactNativeSpec.podspec` и обратите внимание на используемую там `folly_version`. Вернитесь к своему собственному podspec и установите `folly_version` в соответствие.

## Сборка Android завершается с ошибкой `OutOfMemoryException`.

Если ваши сборки Gradle для Android завершаются с ошибкой: `OutOfMemoryException: Out of memory: Java heap space.` или подобные ошибки, связанные с нехваткой памяти, возможно, вам нужно увеличить память, выделенную для JVM.

Это можно сделать, отредактировав файл `gradle.properties` в папке `android/gradle.properties`:

```diff
 # Specifies the JVM arguments used for the daemon process.
 # The setting is particularly useful for tweaking memory settings.
 # Default value: -Xmx1024m -XX:MaxPermSize=256m
-# org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
+org.gradle.jvmargs=-Xmx4g -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
```

Обязательно откомментируйте строку и установите предпочтительный размер памяти с помощью параметра `-Xmx`. Минимально требуется 2 Гб, рекомендуется 4 Гб.

## Android NDK и Mac с процессорами M1 Apple Silicon

Нам известно о ряде несовместимостей между Android NDK и Mac на процессорах M1 ([здесь](https://github.com/android/ndk/issues/1299) и [здесь](https://github.com/android/ndk/issues/1410)).

Поскольку New Architectecture полагается на NDK, вы можете столкнуться с проблемами во время сборки.

Версии React Native 0.70 и 0.71 содержат исправления для этих сбоев сборки, и мы предлагаем вам обновляться до этих версий по мере возможности.
