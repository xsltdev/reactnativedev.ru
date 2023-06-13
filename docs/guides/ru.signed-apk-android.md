

---
id: signed-apk-android
title: Publishing to Google Play Store
---

Android требует, чтобы все приложения перед установкой были подписаны цифровым сертификатом. Для распространения приложения Android через [Google Play store](https://play.google.com/store) оно должно быть подписано ключом выпуска, который затем должен использоваться для всех последующих обновлений. С 2017 года Google Play может автоматически управлять подписанием релизов благодаря функции [App Signing by Google Play](https://developer.android.com/studio/publish/app-signing#app-signing-google-play). Однако прежде чем бинарный файл приложения будет загружен в Google Play, его необходимо подписать ключом выгрузки. Страница [Signing Your Applications](https://developer.android.com/tools/publishing/app-signing.html) в документации Android Developers подробно описывает эту тему. Это руководство вкратце описывает процесс, а также перечисляет шаги, необходимые для упаковки пакета JavaScript.



:::info

Если вы используете Expo, прочитайте руководство Expo для [Deploying to App Stores](https://docs.expo.dev/distribution/app-stores/), чтобы создать и отправить ваше приложение в Google Play Store. Это руководство работает с любым приложением React Native для автоматизации процесса развертывания.

:::




## Генерация ключа выгрузки




Вы можете сгенерировать закрытый ключ подписи с помощью `keytool`.




### Windows




В Windows `keytool` должен быть запущен из `C:\Program Files\Java\jdkx.x.x_x\bin`, от имени администратора.

    keytool -genkeypair -v -storetype PKCS12 -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

Эта команда запрашивает у вас пароли для хранилища ключей и ключа, а также поля Distinguished Name для вашего ключа. Затем создается хранилище ключей в виде файла под названием `my-upload-key.keystore`.



Хранилище ключей содержит один ключ, действительный в течение 10000 дней. Псевдоним - это имя, которое вы будете использовать позже при подписании вашего приложения, поэтому не забудьте обратить внимание на псевдоним.




### macOS




На macOS, если вы не уверены, где находится папка JDK bin, выполните следующую команду, чтобы найти ее:

    /usr/libexec/java_home

Он выведет каталог JDK, который будет выглядеть примерно так:

    /Library/Java/JavaVirtualMachines/jdkX.X.X_XXX.jdk/Contents/Home

Перейдите в этот каталог с помощью команды `cd /your/jdk/path` и используйте команду keytool с правами sudo, как показано ниже.

    sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

:::осторожно
Помните, что файл keystore должен быть приватным. Если вы потеряли загруженный ключ или он был взломан, вам следует [следовать этим инструкциям] (https://support.google.com/googleplay/android-developer/answer/7384423#reset).

:::




## Настройка переменных Gradle




1. Поместите файл `my-upload-key.keystore` в директорию `android/app` в папке проекта.

2. Отредактируйте файл `~/.gradle/gradle.properties` или `android/gradle.properties` и добавьте следующее (замените `*****` на правильный пароль keystore, псевдоним и пароль ключа),

```
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

Это будут глобальные переменные Gradle, которые мы позже сможем использовать в конфигурации Gradle для подписи нашего приложения.



:::примечание Примечание об использовании git

Сохранение вышеуказанных переменных Gradle в `~/.gradle/gradle.properties` вместо `android/gradle.properties` предотвращает их проверку в git. Возможно, вам придется создать файл `~/.gradle/gradle.properties` в домашнем каталоге вашего пользователя, прежде чем вы сможете добавить переменные.

:::




:::примечание Примечание о безопасности

Если вам не нравится хранить пароли в открытом виде, и вы работаете под управлением macOS, вы также можете [хранить свои учетные данные в приложении Keychain Access] (https://pilloxa.gitlab.io/posts/safer-passwords-in-gradle/). Тогда вы можете пропустить две последние строки в `~/.gradle/gradle.properties`.

:::




## Добавление конфигурации подписания в конфигурацию Gradle вашего приложения




Последний шаг конфигурации, который необходимо сделать, это настроить подписание релизных сборок с помощью ключа выгрузки. Отредактируйте файл `android/app/build.gradle` в папке вашего проекта и добавьте конфиг подписания,

```groovy
...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

## Генерация релиза AAB



Выполните следующую команду в терминале:

```shell
npx react-native build-android --mode=release
```

Эта команда использует `bundleRelease` от Gradle, который собирает весь JavaScript, необходимый для запуска вашего приложения, в AAB ([Android App Bundle](https://developer.android.com/guide/app-bundle)). Если вам нужно изменить способ компоновки пакета JavaScript и/или ресурсов drawable (например, если вы изменили имена файлов/папок по умолчанию или общую структуру проекта), посмотрите на `android/app/build.gradle`, чтобы увидеть, как вы можете обновить его, чтобы отразить эти изменения.



:::примечание

Убедитесь, что `gradle.properties` не включает `org.gradle.configureondemand=true`, так как это заставит сборку релиза пропустить упаковку JS и активов в бинарный файл приложения.

:::




Созданный AAB можно найти в папке `android/app/build/outputs/bundle/release/app-release.aab`, и он готов к загрузке в Google Play.




Для того чтобы Google Play мог принимать формат AAB, необходимо настроить App Signing by Google Play для вашего приложения на консоли Google Play Console. Если вы обновляете существующее приложение, которое не использует App Signing by Google Play, ознакомьтесь с разделом [миграция](#migrating-old-android-react-native-apps-to-use-app-signing-by-google-play), чтобы узнать, как выполнить это изменение конфигурации.




## Тестирование релизной сборки вашего приложения




Прежде чем загружать релизную сборку в Play Store, убедитесь, что вы тщательно ее протестировали. Сначала удалите все предыдущие версии приложения, которые у вас уже установлены. Установите его на устройство с помощью следующей команды в корне проекта:

```shell
npx react-native run-android --mode=release
```

Обратите внимание, что `--режим release` доступен только в том случае, если вы настроили подписание, как описано выше.



Вы можете завершить все запущенные экземпляры bundler, так как весь ваш фреймворк и код JavaScript собран в активах APK.




## Публикация в другие магазины




По умолчанию сгенерированный APK содержит нативный код для процессорных архитектур `x86`, `x86_64`, `ARMv7a` и `ARM64-v8a`. Это облегчает обмен APK, которые работают почти на всех устройствах Android. Однако у этого есть и обратная сторона: на любом устройстве останется неиспользуемый нативный код, что приведет к неоправданно большому размеру APK.




Вы можете создать APK для каждого процессора, добавив следующую строку в файл `android/app/build.gradle`:

```diff
android {

    splits {
        abi {
            reset()
            enable true
            universalApk false
            include "armeabi-v7a", "arm64-v8a", "x86", "x86_64"
        }
    }

}
```

Загрузите эти файлы в маркеты, поддерживающие таргетинг по устройствам, такие как [Amazon AppStore](https://developer.amazon.com/docs/app-submission/device-filtering-and-compatibility.html) или [F-Droid](https://f-droid.org/en/), и пользователи автоматически получат соответствующие APK. Если вы хотите загрузить на другие рынки, такие как [APKFiles](https://www.apkfiles.com/), которые не поддерживают несколько APK для одного приложения, измените строку `universalApk false` на `true`, чтобы создать универсальный APK по умолчанию с двоичными файлами для обоих процессоров.



Обратите внимание, что вам также придется настроить различные коды версий, как [предложено на этой странице](https://developer.android.com/studio/build/configure-apk-splits#configure-APK-versions) из официальной документации Android.




## Включение Proguard для уменьшения размера APK (необязательно)




Proguard - это инструмент, который может немного уменьшить размер APK. Он делает это путем удаления частей байткода React Native Java (и его зависимостей), которые ваше приложение не использует.




:::предостережение Важно

Обязательно тщательно протестируйте свое приложение, если вы включили Proguard. Proguard часто требует конфигурации, специфичной для каждой нативной библиотеки, которую вы используете. Смотрите `app/proguard-rules.pro`.

:::




Чтобы включить Proguard, отредактируйте `android/app/build.gradle`:

```groovy
/**
 * Run Proguard to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
```

## Миграция старых приложений Android React Native для использования функции App Signing by Google Play



Если вы переходите с предыдущей версии React Native, скорее всего, ваше приложение не использует функцию App Signing by Google Play. Мы рекомендуем вам включить ее, чтобы воспользоваться такими преимуществами, как автоматическое разделение приложений. Чтобы перейти от старого способа подписания, вам нужно начать с [генерации нового ключа загрузки](#generating-an-upload-key), а затем заменить конфигурацию подписи релиза в `android/app/build.gradle`, чтобы использовать ключ загрузки вместо ключа релиза (см. раздел о [добавлении конфигурации подписи в gradle](#adding-signing-config-to-your-apps-gradle-config)). После этого вы должны следовать [инструкциям с сайта Google Play Help](https://support.google.com/googleplay/android-developer/answer/7384423), чтобы отправить ваш оригинальный ключ релиза в Google Play.




## Разрешения по умолчанию




По умолчанию, разрешение `INTERNET` добавлено в ваше приложение Android, так как практически все приложения используют его. Разрешение `SYSTEM_ALERT_WINDOW` добавлено в ваш Android APK в режиме отладки, но оно будет удалено в продакшене.

