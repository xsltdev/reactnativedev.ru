# Работа в сетью

Многие мобильные приложения нуждаются в загрузке ресурсов с удаленного URL. Вы можете сделать POST-запрос к REST API, или вам может понадобиться получить фрагмент статического контента с другого сервера.

## Использование Fetch

React Native предоставляет [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) для ваших сетевых нужд. Fetch покажется вам знакомым, если вы ранее использовали `XMLHttpRequest` или другие сетевые API. За дополнительной информацией вы можете обратиться к руководству MDN по [Using Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch).

### Выполнение запросов

Чтобы получить содержимое из произвольного URL, вы можете передать URL для получения:

```ts
fetch('https://mywebsite.com/mydata.json');
```

Fetch также принимает необязательный второй аргумент, который позволяет вам настроить HTTP-запрос. Возможно, вы захотите указать дополнительные заголовки или сделать POST-запрос:

```ts
fetch('https://mywebsite.com/endpoint/', {
    method: 'POST',
    headers: {
        Accept: 'application/json',
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        firstParam: 'yourValue',
        secondParam: 'yourOtherValue',
    }),
});
```

Посмотрите в [Fetch Request docs](https://developer.mozilla.org/docs/Web/API/Request) полный список свойств.

### Обработка ответа

Приведенные выше примеры показывают, как можно сделать запрос. Во многих случаях вы захотите что-то сделать с ответом.

Работа в сети по своей сути является асинхронной операцией. Метод Fetch возвращает [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), что упрощает написание кода, который работает асинхронно:

```ts
const getMoviesFromApi = () => {
    return fetch('https://reactnative.dev/movies.json')
        .then((response) => response.json())
        .then((json) => {
            return json.movies;
        })
        .catch((error) => {
            console.error(error);
        });
};
```

Вы также можете использовать синтаксис `async` / `await` в приложении React Native:

```ts
const getMoviesFromApiAsync = async () => {
    try {
        const response = await fetch(
            'https://reactnative.dev/movies.json'
        );
        const json = await response.json();
        return json.movies;
    } catch (error) {
        console.error(error);
    }
};
```

Не забудьте перехватить все ошибки, которые могут быть вызваны `fetch`, иначе они будут удалены без звука.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-fetch-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-fetch-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

!!!warning ""

    По умолчанию в iOS 9.0 или более поздней версии применяется технология App Transport Secruity (ATS). ATS требует, чтобы любое HTTP-соединение использовало HTTPS. Если вам нужно получить данные из URL-адреса с открытым текстом (который начинается с `http`), вам сначала нужно [добавить исключение ATS](integration-with-existing-apps.md#test-your-integration). Если вы заранее знаете, к каким доменам вам понадобится доступ, безопаснее добавить исключения только для этих доменов; если домены неизвестны до момента выполнения, вы можете [полностью отключить ATS](publishing-to-app-store.md#1-enable-app-transport-security). Однако обратите внимание, что с января 2017 года [Apple при проверке App Store будет требовать разумного обоснования отключения ATS](https://forums.developer.apple.com/thread/48979). Дополнительную информацию см. в [документации Apple](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33).

!!!warning ""

    В Android, начиная с API Level 28, трафик чистого текста также блокируется по умолчанию. Это поведение можно переопределить, установив [`android:usesCleartextTraffic`](https://developer.android.com/guide/topics/manifest/application-element#usesCleartextTraffic) в файле манифеста приложения.

## Использование других сетевых библиотек

API [XMLHttpRequest API](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) встроен в React Native. Это означает, что вы можете использовать сторонние библиотеки, такие как [frisbee](https://github.com/niftylettuce/frisbee) или [axios](https://github.com/axios/axios), которые зависят от него, или вы можете использовать XMLHttpRequest API напрямую, если вы предпочитаете.

```ts
var request = new XMLHttpRequest();
request.onreadystatechange = (e) => {
    if (request.readyState !== 4) {
        return;
    }

    if (request.status === 200) {
        console.log('success', request.responseText);
    } else {
        console.warn('error');
    }
};

request.open('GET', 'https://mywebsite.com/endpoint/');
request.send();
```

!!!warning ""

    Модель безопасности для XMLHttpRequest отличается от веб-модели, поскольку в нативных приложениях нет понятия [CORS](https://ru.wikipedia.org/wiki/Cross-origin_resource_sharing).

## Поддержка WebSocket

React Native также поддерживает [WebSockets](https://developer.mozilla.org/docs/Web/API/WebSocket), протокол, который обеспечивает полнодуплексные каналы связи через одно TCP-соединение.

```ts
var ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
    // connection opened
    ws.send('something'); // send a message
};

ws.onmessage = (e) => {
    // a message was received
    console.log(e.data);
};

ws.onerror = (e) => {
    // an error occurred
    console.log(e.message);
};

ws.onclose = (e) => {
    // connection closed
    console.log(e.code, e.reason);
};
```

## Известные проблемы с `fetch` и аутентификацией на основе cookie

Следующие опции в настоящее время не работают с `fetch`.

-   `redirect:manual`
-   `credentials:omit`

---

-   Наличие одноименных заголовков на Android приведет к тому, что будет присутствовать только последний. Временное решение можно найти [здесь](https://github.com/facebook/react-native/issues/18837#issuecomment-398779994).
-   Аутентификация на основе cookie в настоящее время нестабильна. Вы можете просмотреть некоторые из поднятых проблем [здесь](https://github.com/facebook/react-native/issues/23185).
-   Как минимум на iOS, при перенаправлении через `302`, если присутствует заголовок `Set-Cookie`, cookie не устанавливается должным образом. Поскольку перенаправление не может быть обработано вручную, это может привести к сценарию, при котором возникают бесконечные запросы, если перенаправление является результатом истекшей сессии.

## Настройка NSURLSession на iOS

Для некоторых приложений может быть целесообразно предоставить пользовательскую `NSURLSessionConfiguration` для базовой `NSURLSession`, которая используется для сетевых запросов в приложении React Native, работающем на iOS. Например, может потребоваться установить пользовательскую строку агента пользователя для всех сетевых запросов, исходящих от приложения, или снабдить `NSURLSession` эфемерной `NSURLSessionConfiguration`. Функция `RCTSetCustomNSURLSessionConfigurationProvider` позволяет выполнить такую настройку. Не забудьте добавить следующий импорт в файл, в котором будет вызываться `RCTSetCustomNSURLSessionConfigurationProvider`:

```objectivec
#import <React/RCTHTTPRequestHandler.h>
```

`RCTSetCustomNSURLSessionConfigurationProvider` должен вызываться на ранней стадии жизненного цикла приложения, чтобы он был легко доступен, когда это необходимо, например, React:

```objectivec
-(void)application:(__unused UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

  // set RCTSetCustomNSURLSessionConfigurationProvider
  RCTSetCustomNSURLSessionConfigurationProvider(^NSURLSessionConfiguration *{
     NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
     // configure the session
     return configuration;
  });

  // set up React
  _bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:launchOptions];
}
```
