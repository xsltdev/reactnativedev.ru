---
description: Linking предоставляет вам общий интерфейс для взаимодействия с входящими и исходящими ссылками приложений
---

# Linking

**`Linking`** предоставляет вам общий интерфейс для взаимодействия с входящими и исходящими ссылками приложений.

Каждая ссылка (URL) имеет схему URL, некоторые сайты имеют префикс `https://` или `http://`, а `http` является схемой URL. Для краткости будем называть ее схемой.

Помимо `https`, вы, скорее всего, знакомы со схемой `mailto`. Когда вы открываете ссылку со схемой `mailto`, ваша операционная система открывает установленное почтовое приложение. Аналогично существуют схемы для совершения телефонных звонков и отправки SMS.

Подобно использованию схемы `mailto`, можно ссылаться на другие приложения, используя пользовательские схемы `url`. Например, когда вы получаете письмо **Magic Link** от Slack, кнопка **Launch Slack** представляет собой тег якоря с `href`, который выглядит примерно так: `slack://secret/magic-login/other-secret`. Как и в случае со Slack, вы можете указать операционной системе, что хотите работать с пользовательской схемой. Когда приложение Slack открывается, оно получает URL-адрес, который был использован для его открытия. Это часто называют _глубоким связыванием_.

Пользовательская схема URL — это не единственный способ открыть ваше приложение на мобильном устройстве. Вы не хотите использовать пользовательскую схему URL в ссылках в письме, потому что тогда ссылки будут нерабочими на рабочем столе. Вместо этого вы хотите использовать обычные ссылки `https`, например `https://www.myapp.io/records/1234546`. И на мобильном телефоне вы хотите, чтобы эта ссылка открывала ваше приложение. В Android это называется **Deep Links** (Universal Links — iOS).

## Встроенные схемы URL {#built-in-url-schemes}

Как уже упоминалось во введении, существуют некоторые схемы URL для основной функциональности, которые существуют на каждой платформе. Приведенный ниже список не является исчерпывающим, но охватывает наиболее часто используемые схемы.

| Схема            | Описание                                                     | iOS | Android |
| ---------------- | ------------------------------------------------------------ | --- | ------- |
| `mailto`         | Открывает почтовое приложение, eg: `mailto: support@expo.io` | ✅  | ✅      |
| `tel`            | Открывает приложение телефон, eg: `tel:+123456789`           | ✅  | ✅      |
| `sms`            | Открывает смс приложение, eg: `sms:+123456789`               | ✅  | ✅      |
| `https` / `http` | Открывает браузер, eg: `https://expo.io`                     | ✅  | ✅      |

## Включение глубоких ссылок {#enabling-deep-links}

!!!info "Проекты только с нативным кодом"

    Следующий раздел относится только к проектам с открытым кодом. Если вы используете управляемый рабочий процесс Expo, смотрите руководство по [Linking](https://docs.expo.dev/guides/linking/) в документации Expo для соответствующей альтернативы.

Если вы хотите включить глубокие ссылки в своем приложении, ознакомьтесь с приведенным ниже руководством:

=== "Android"

    !!!warning ""

    	Инструкции по добавлению поддержки глубоких ссылок на Android см. в разделе [Enabling Deep Links for App Content — Add Intent Filters for Your Deep Links](http://developer.android.com/training/app-indexing/deep-linking.html#adding-filters).

    Если вы хотите получить намерение в существующем экземпляре MainActivity, вы можете установить `launchMode` MainActivity на `singleTask` в `AndroidManifest.xml`. Дополнительную информацию см. в документации [`<activity>`](http://developer.android.com/guide/topics/manifest/activity-element.html).

    ```xml
    <activity
    	android:name=".MainActivity"
    	android:launchMode="singleTask">
    ```

=== "iOS"

    !!!warning "ПРИМЕЧАНИЕ"

    	На iOS вам нужно добавить папку `LinkingIOS` в пути поиска заголовков, как описано в шаге 3 [здесь](../guides/linking-libraries-ios.md#step-3). Если вы также хотите слушать входящие ссылки на приложения во время выполнения вашего приложения, вам нужно добавить следующие строки в ваш `*AppDelegate.m`:

    ```objectivec
    // iOS 9.x or newer
    #import <React/RCTLinkingManager.h>

    - (BOOL)application:(UIApplication *)application
    openURL:(NSURL *)url
    options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
    return [RCTLinkingManager application:application openURL:url options:options];
    }
    ```

    Если вы используете iOS 8.x или более старую версию, вместо этого можно использовать следующий код:

    ```objectivec
    // iOS 8.x or older
    #import <React/RCTLinkingManager.h>

    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
    sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
    {
    return [RCTLinkingManager application:application openURL:url
    					sourceApplication:sourceApplication annotation:annotation];
    }
    ```

    Если ваше приложение использует [Universal Links](https://developer.apple.com/ios/universal-links/), вам также необходимо добавить следующий код:

    ```objectivec
    - (BOOL)application:(UIApplication *)application continueUserActivity:(nonnull NSUserActivity *)userActivity
    restorationHandler:(nonnull void (^)(NSArray<id<UIUserActivityRestoring>> * _Nullable))restorationHandler
    {
    return [RCTLinkingManager application:application
    				continueUserActivity:userActivity
    					restorationHandler:restorationHandler];
    }
    ```

## Работа с глубокими ссылками {#handling-deep-links}

Существует два способа обработки URL-адресов, открывающих ваше приложение.

### 1. Если приложение уже открыто, оно переводится на передний план и запускается событие Linking 'url'

Вы можете обрабатывать эти события с помощью `Linking.addEventListener('url', callback)` — он вызывает `callback({url})` с URL ссылки

### 2. Если приложение еще не открыто, оно открывается, и в качестве начальногоURL передается url

Вы можете обрабатывать эти события с помощью `Linking.getInitialURL()` — он возвращает Promise, который разрешается в URL, если он есть.

## Пример {#example}

### Открытые ссылки и глубокие ссылки (универсальные ссылки) {#open-links-and-deep-links-universal-links}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-linking-example-1" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-linking-example-1" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Открыть пользовательские настройки {#open-custom-settings}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-linking-example-2" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-linking-example-2" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Получить глубокую ссылку {#get-the-deep-link}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-linking-example-3" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-linking-example-3" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Отправка интенций (Android) {#send-intents-android}

=== "TypeScript"

    <div data-snack-id="@bndby/linking-example-send-intents-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/linking-example-send-intents-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы {#methods}

### addEventListener()

```ts
static addEventListener(
  type: 'url',
  handler: (event: {url: string}) => void,
): EmitterSubscription;
```

Добавьте обработчик изменений `Linking`, прослушивая тип события `url` и предоставляя обработчик.

### canOpenURL()

```ts
static canOpenURL(url: string): Promise<boolean>;
```

Определите, может ли установленное приложение обрабатывать заданный URL.

Метод возвращает объект `Promise`. Когда определяется, можно ли обрабатывать данный URL, обещание разрешается, и первым параметром является то, можно ли его открыть.

Обещание `Promise` будет отклонено на Android, если невозможно проверить, можно ли открыть URL, или при работе с Android 11 (SDK 30), если вы не указали соответствующие запросы намерения в `AndroidManifest.xml`. Аналогично на iOS обещание будет отклонено, если вы не добавили конкретную схему в ключ `LSApplicationQueriesSchemes` в `Info.plist` (см. ниже).

**Параметры:**

| Имя                 | Тип      | Описание                |
| ------------------- | -------- | ----------------------- |
| `url` (обязательно) | `string` | URL-адрес для открытия. |

!!!warning ""

    Для веб-адресов, протокол (`"http://"`, `"https://"`) должен быть установлен соответствующим образом!

!!!warning ""

    Этот метод имеет ограничения на iOS 9+. Из [официальной документации Apple](https://developer.apple.com/documentation/uikit/uiapplication/1622952-canopenurl):

    -   Если ваше приложение связано с более ранней версией iOS, но работает в iOS 9.0 или более поздней, вы можете вызвать этот метод не более 50 раз. После достижения этого лимита последующие вызовы всегда приводят к `false`. Если пользователь переустанавливает или обновляет приложение, iOS сбрасывает лимит.

    Начиная с iOS 9, ваше приложение также должно предоставить ключ `LSApplicationQueriesSchemes` в файле `Info.plist`, иначе `canOpenURL()` всегда будет иметь значение `false`.

!!!warning ""

    При использовании Android 11 (SDK 30) вы должны указать намерения для схем, которые вы хотите обрабатывать, в `AndroidManifest.xml`. Список общих интентов можно найти [здесь](https://developer.android.com/guide/components/intents-common).

    Например, для работы со схемами `https` необходимо добавить в манифест следующее:

    ```xml
    <manifest>
    	<queries>
    		<intent>
    			<action
    				android:name="android.intent.action.VIEW"
    			/>
    			<data android:scheme="https" />
    		</intent>
    	</queries>
    </manifest>
    ```

### getInitialURL()

```ts
static getInitialURL(): Promise<string | null>;
```

Если запуск приложения был инициирован ссылкой на приложение, будет указан url ссылки, в противном случае будет указан `null`.

!!!warning ""

    Для поддержки глубокого связывания на Android см. http://developer.android.com/training/app-indexing/deep-linking.html#handling-intents.

!!!warning ""

    `getInitialURL` может вернуть `null` при включенной отладке. Отключите отладчик, чтобы убедиться, что он будет передан.

### openSettings()

```ts
static openSettings(): Promise<void>;
```

Откройте приложение "Настройки" и отобразите пользовательские настройки приложения, если они есть.

### openURL()

```ts
static openURL(url: string): Promise<any>;
```

Попробуйте открыть заданный `url` с помощью любого из установленных приложений.

Вы можете использовать другие URL, например, местоположение (например, `"geo:37.484847,-122.148386"` на Android или `"http://maps.apple.com/?ll=37.484847,-122.148386"` на iOS), контакт или любой другой URL, который можно открыть с помощью установленных приложений.

Метод возвращает объект `Promise`. Если пользователь подтверждает диалог открытия или `url` открывается автоматически, обещание выполняется. Если пользователь отменяет открытый диалог или для `url` нет зарегистрированных приложений, обещание отклоняется.

**Параметры:**

| Имя                 | Тип      | Описание                |
| ------------------- | -------- | ----------------------- |
| `url` (обязательно) | `string` | URL-адрес для открытия. |

!!!warning ""

    Этот метод завершится неудачей, если система не знает, как открыть указанный URL. Если вы передаете не-http(s) URL, лучше сначала проверить `canOpenURL()`.

!!!warning ""

    Для веб-адресов, протокол (`"http://"`, `"https://"`) должен быть установлен соответствующим образом!

!!!warning ""

    Этот метод может вести себя по-разному в симуляторе, например, ссылки `"tel:"` не могут быть обработаны в симуляторе iOS, так как нет доступа к приложению dialer.

### sendIntent() :simple-android:

```ts
static sendIntent(
  action: string,
  extras?: Array<{key: string; value: string | number | boolean}>,
): Promise<void>;
```

Запустите намерение Android с помощью дополнительных функций.

**Параметры:**

| Имя                    | Тип                                                           |
| ---------------------- | ------------------------------------------------------------- |
| `action` (обязательно) | `string`                                                      |
| `extras`               | массив из `{key: string, value: string \| number \| boolean}` |
