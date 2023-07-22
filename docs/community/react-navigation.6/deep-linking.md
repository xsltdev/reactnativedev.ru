---
description: В этом руководстве описывается, как настроить приложение для работы с глубокими ссылками на различных платформах
---

# Глубокая связь

В этом руководстве описывается, как настроить приложение для работы с глубокими ссылками на различных платформах. Для обработки входящих ссылок необходимо решить две задачи:

1.  Если приложение ранее не было открыто, то глубокая ссылка должна установить начальное состояние
2.  Если приложение уже было открыто, глубокая ссылка должна обновить состояние, чтобы отразить входящую ссылку.

React Native предоставляет [`Linking`](https://reactnative.dev/docs/linking) для получения уведомлений о входящих ссылках. React Navigation может интегрироваться с модулем `Linking` для автоматической обработки глубоких ссылок. В Web-версии React Navigation может интегрироваться с API `history` браузера для обработки URL на стороне клиента. Более подробно о том, как настроить ссылки в React Navigation, читайте в [configuring links](configuring-links.md).

Хотя вам не обязательно использовать свойство `linking` из React Navigation, и вы можете самостоятельно работать с глубокими ссылками, используя API `Linking` и осуществляя навигацию оттуда, это будет значительно сложнее, чем использование свойства `linking`, которое обрабатывает многие граничные случаи за вас. Поэтому мы не рекомендуем реализовывать его самостоятельно.

Ниже мы рассмотрим необходимые настройки для того, чтобы интеграция глубоких ссылок работала.

## Настройка с проектами Expo

Во-первых, необходимо указать схему URL для вашего приложения. Она соответствует строке перед `://` в URL, поэтому если ваша схема - `mychat`, то ссылка на ваше приложение будет `mychat://`. Вы можете зарегистрировать схему в файле `app.json`, добавив строку под ключом scheme:

```json
{
    "expo": {
        "scheme": "mychat"
    }
}
```

Далее установим `expo-linking`, который понадобится нам для получения префикса глубокой ссылки:

```bash
npx expo install expo-linking
```

Затем настроим React Navigation на использование `scheme` для разбора входящих глубоких ссылок:

```js
import * as Linking from 'expo-linking';

const prefix = Linking.createURL('/');

function App() {
    const linking = {
        prefixes: [prefix],
    };

    return (
        <NavigationContainer
            linking={linking}
            fallback={<Text>Loading...</Text>}
        >
            {/* content */}
        </NavigationContainer>
    );
}
```

Необходимость использования `Linking.createURL` заключается в том, что схема будет отличаться в зависимости от того, где находится клиентское приложение - в клиентском приложении или в автономном приложении.

Схема, указанная в `app.json`, применима только к автономным приложениям. В клиентском приложении Expo можно установить глубокую ссылку, используя `exp://ADDRESS:PORT/--/`, где `ADDRESS` часто имеет значение `127.0.0.1`, а `PORT` часто имеет значение `19000` - URL выводится при запуске `expo start`. Функция `Linking.createURL` абстрагируется от этого, чтобы не указывать их вручную.

Если вы используете универсальные ссылки, то необходимо добавить в префиксы и свой домен:

```js
const linking = {
    prefixes: [
        Linking.createURL('/'),
        'https://app.example.com',
    ],
};
```

## Настройка на "голых" проектах React Native

### Настройка на iOS

Настроим нативное приложение для iOS так, чтобы оно открывалось по схеме URI `mychat://`.

Для этого необходимо подключить `RCTLinking` к проекту, выполнив описанные здесь действия. Чтобы иметь возможность прослушивать входящие ссылки на приложение, необходимо добавить следующие строки в `AppDelegate.m` в проекте:

```objc
// Add the header at the top of the file:
#import <React/RCTLinkingManager.h>

// Add this inside `@implementation AppDelegate` above `@end`:
- (BOOL)application:(UIApplication *)application
   openURL:(NSURL *)url
   options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
  return [RCTLinkingManager application:application openURL:url options:options];
}
```

Если ваше приложение использует [Universal Links](https://developer.apple.com/ios/universal-links/), то необходимо добавить следующий код:

```objc
// Add this inside `@implementation AppDelegate` above `@end`:
- (BOOL)application:(UIApplication *)application continueUserActivity:(nonnull NSUserActivity *)userActivity
 restorationHandler:(nonnull void (^)(NSArray<id<UIUserActivityRestoring>> * _Nullable))restorationHandler
{
 return [RCTLinkingManager application:application
                  continueUserActivity:userActivity
                    restorationHandler:restorationHandler];
}
```

Теперь необходимо добавить схему в конфигурацию проекта.

Проще всего это сделать с помощью пакета `uri-scheme`, выполнив следующую команду:

```bash
npx uri-scheme add mychat --ios
```

Если вы хотите сделать это вручную, откройте проект (например, `SimpleApp/ios/SimpleApp.xcworkspace`) в Xcode. Выберите проект в боковой панели и перейдите на вкладку "Информация". Прокрутите вниз до раздела "Типы URL" и добавьте один. В новом типе URL установите идентификатор и схему URL на желаемую схему URL.

![Информация о типах URL в проекте Xcode с добавлением mychat](xcode-linking.png)

Для того чтобы универсальные ссылки работали в вашем приложении, необходимо также настроить [Associated Domains](https://developer.apple.com/documentation/Xcode/supporting-associated-domains) на вашем сервере.

#### Гибридные React Native и нативные iOS-приложения

Если вы используете React Navigation в гибридном приложении - приложении для iOS, в котором есть как Swift/ObjC, так и React Native, - возможно, в вашем `Podfile` не хватает подспецифика `RCTLinkingIOS`, который по умолчанию устанавливается в новые проекты React Native. Чтобы добавить его, убедитесь, что ваш `Podfile` выглядит следующим образом:

```pod
 pod 'React', :path => '../node_modules/react-native', :subspecs => [
    . . . // other subspecs
    'RCTLinkingIOS',
    . . .
  ]
```

### Настройка в Android

Для настройки внешней привязки в Android можно создать новое намерение в манифесте.

Проще всего это сделать с помощью пакета `uri-scheme`: `npx uri-scheme add mychat --android`.

Если вы хотите добавить его вручную, откройте файл `SimpleApp/android/app/src/main/AndroidManifest.xml` и внесите следующие изменения:

1.  Установите `launchMode` для `MainActivity` в `singleTask`, чтобы получить намерение на существующую `MainActivity` (это значение используется по умолчанию, так что, возможно, вам не придется ничего менять).
2.  Добавьте новый [`intent-filter`](http://developer.android.com/training/app-indexing/deep-linking.html#adding-filters) внутри записи `MainActivity` с действием типа `VIEW`:

```xml
<activity
    android:name=".MainActivity"
    android:launchMode="singleTask"
>
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category
            android:name="android.intent.category.LAUNCHER"
        />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category
            android:name="android.intent.category.DEFAULT"
        />
        <category
            android:name="android.intent.category.BROWSABLE"
        />
        <data android:scheme="mychat" />
    </intent-filter>
</activity>
```

Аналогично универсальным ссылкам на iOS, на Android также можно использовать домен для связи приложения с вашим сайтом с помощью [проверки Android App Links](https://developer.android.com/training/app-links/verify-site-associations). Для начала необходимо настроить файл `AndroidManifest.xml`:

1.  Добавьте `android:autoVerify="true"` в запись `<intent-filter>`.
2.  Добавьте `scheme` и `host` вашего домена в новую запись `<data>` внутри `<intent-filter>`.

После добавления они должны выглядеть следующим образом:

```xml
<activity
    android:name=".MainActivity"
    android:launchMode="singleTask"
>
    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.MAIN" />
        <category
            android:name="android.intent.category.LAUNCHER"
        />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category
            android:name="android.intent.category.DEFAULT"
        />
        <category
            android:name="android.intent.category.BROWSABLE"
        />
        <data android:scheme="mychat" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category
            android:name="android.intent.category.DEFAULT"
        />
        <category
            android:name="android.intent.category.BROWSABLE"
        />
        <data android:scheme="http" />
        <data android:scheme="https" />
        <data android:host="www.example.com" />
    </intent-filter>
</activity>
```

Затем необходимо [объявить связь](https://developer.android.com/training/app-links/verify-site-associations#web-assoc) между вашим сайтом и фильтрами намерений, разместив JSON-файл Digital Asset Links.

## Тестирование глубоких ссылок

Перед тестированием глубоких ссылок убедитесь, что вы пересобрали и установили приложение в эмуляторе/симуляторе/устройстве.

Если вы тестируете приложение на iOS, запустите его:

```bash
npx react-native run-ios
```

Если вы тестируете на Android, выполните команду:

```bash
npx react-native run-android
```

Если вы используете Expo managed workflow и тестируете на Expo client, то перестраивать приложение не нужно. Однако необходимо использовать правильный адрес и порт, которые выводятся при запуске `expo start`, например, `exp://127.0.0.1:19000/--/`.

Если вы хотите протестировать свою схему в приложении Expo, вам необходимо пересобрать свое автономное приложение, выполнив команду `expo build:ios -t simulator` или `expo build:android`, и установить полученные исполняемые файлы.

### Тестирование с помощью `npx uri-scheme`

Пакет `uri-scheme` - это инструмент командной строки, который можно использовать для тестирования глубоких ссылок как на iOS, так и на Android. Его можно использовать следующим образом:

```bash
npx uri-scheme open [your deep link] --[ios|android]
```

Например:

```bash
npx uri-scheme open "mychat://chat/jane" --ios
```

Или при использовании клиента Expo:

```bash
npx uri-scheme open "exp://127.0.0.1:19000/--/chat/jane" --ios
```

### Тестирование с помощью `xcrun` на iOS

Команда `xcrun` может быть использована следующим образом для тестирования глубоких ссылок с помощью симулятора iOS:

```bash
xcrun simctl openurl booted [your deep link]
```

Например:

```bash
xcrun simctl openurl booted "mychat://chat/jane"
```

### Тестирование с помощью `adb` на Android

Команда `adb` может быть использована следующим образом для тестирования глубоких ссылок с помощью эмулятора Android или подключенного устройства:

```bash
adb shell am start -W -a android.intent.action.VIEW -d [your deep link] [your android package name]
```

Например:

```bash
adb shell am start -W -a android.intent.action.VIEW -d "mychat://chat/jane" com.simpleapp
```

Или при использовании клиента Expo:

```bash
adb shell am start -W -a android.intent.action.VIEW -d "exp://127.0.0.1:19000/--/chat/jane" host.exp.exponent
```

## Интеграции сторонних программ

React Native `Linking` - не единственный способ работы с глубокими ссылками. Возможно, вы захотите интегрировать и другие сервисы, такие как [Firebase Dynamic Links](https://firebase.google.com/docs/dynamic-links), [Branch](https://help.branch.io/developers-hub/docs/react-native) и т.д., которые предоставляют свой собственный API для получения уведомлений о входящих ссылках.

Для этого необходимо переопределить способ подписки React Navigation на входящие ссылки. Для этого можно предоставить собственные функции [`getInitialURL`](navigation-container.md#linkinggetinitialurl) и [`subscribe`](navigation-container.md#linkingsubscribe):

```js
const linking = {
    prefixes: ['myapp://', 'https://myapp.com'],

    // Custom function to get the URL which was used to open the app
    async getInitialURL() {
        // First, you would need to get the initial URL from your third-party integration
        // The exact usage depend on the third-party SDK you use
        // For example, to get the initial URL for Firebase Dynamic Links:
        const {
            isAvailable,
        } = utils().playServicesAvailability;

        if (isAvailable) {
            const initialLink = await dynamicLinks().getInitialLink();

            if (initialLink) {
                return initialLink.url;
            }
        }

        // As a fallback, you may want to do the default deep link handling
        const url = await Linking.getInitialURL();

        return url;
    },

    // Custom function to subscribe to incoming links
    subscribe(listener) {
        // Listen to incoming links from Firebase Dynamic Links
        const unsubscribeFirebase = dynamicLinks().onLink(
            ({ url }) => {
                listener(url);
            }
        );

        // Listen to incoming links from deep linking
        const linkingSubscription = Linking.addEventListener(
            'url',
            ({ url }) => {
                listener(url);
            }
        );

        return () => {
            // Clean up the event listeners
            unsubscribeFirebase();
            linkingSubscription.remove();
        };
    },

    config: {
        // Deep link configuration
    },
};
```

Аналогично приведенному выше примеру, вы можете интегрировать любой API, предоставляющий возможность получения начального URL и подписки на новые входящие URL с помощью опций `getInitialURL` и `subscribe`.

## Ссылки

-   [Deep linking](https://reactnavigation.org/docs/deep-linking)
