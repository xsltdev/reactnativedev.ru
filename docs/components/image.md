# Image

Компонент React для отображения различных типов изображений, включая сетевые изображения, статические ресурсы, временные локальные изображения и изображения с локального диска, такие как рулон камеры.

Этот пример показывает получение и отображение изображения из локального хранилища, а также из сети и даже из данных, предоставленных в схеме uri `'data:'`.

!!!warning ""

    Обратите внимание, что для изображений сети и данных необходимо вручную указать размеры изображения!

## Примеры

<div data-snack-id="@bndby/image-example-1" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Вы также можете добавить `стиль` к изображению:

<div data-snack-id="@bndby/image-example-2" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Поддержка GIF и WebP на Android

При создании собственного нативного кода GIF и WebP не поддерживаются по умолчанию на Android.

Вам потребуется добавить некоторые дополнительные модули в `android/app/build.gradle`, в зависимости от потребностей вашего приложения.

```groovy
dependencies {
  // If your app supports Android versions before Ice Cream Sandwich (API level 14)
  implementation 'com.facebook.fresco:animated-base-support:1.3.0'

  // For animated GIF support
  implementation 'com.facebook.fresco:animated-gif:2.5.0'

  // For WebP support, including animated WebP
  implementation 'com.facebook.fresco:animated-webp:2.5.0'
  implementation 'com.facebook.fresco:webpsupport:2.5.0'

  // For WebP support, without animations
  implementation 'com.facebook.fresco:webpsupport:2.5.0'
}
```

!!!warning ""

    Примечание: указанная выше версия может не обновляться вовремя. Пожалуйста, проверьте [`ReactAndroid/gradle.properties`](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/gradle.properties) в основном репозитории, чтобы узнать, какая версия fresco используется в конкретной версии тегов.

## Свойства

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `accessible`

При значении `true` указывает, что изображение является элементом доступности.

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `accessibilityLabel`

Текст, который считывается программой чтения с экрана, когда пользователь взаимодействует с изображением.

| Type   |
| ------ |
| string |

### `alt`

Строка, определяющая альтернативное текстовое описание изображения, которое будет считываться программой чтения с экрана при взаимодействии с ним пользователя. Использование этого параметра автоматически пометит этот элемент как доступный.

| Type   |
| ------ |
| string |

### `blurRadius`

blurRadius: радиус размытия фильтра размытия, добавленного к изображению.

| Type   |
| ------ |
| number |

!!!warning ""

    Совет: На IOS вам потребуется увеличить `blurRadius` более чем на `5`.

### `capInsets` :simple-ios:

При изменении размера изображения углы размера, указанного `capInsets`, останутся фиксированного размера, но центральное содержимое и границы изображения будут растянуты. Это полезно для создания изменяемых по размеру закругленных кнопок, теней и других изменяемых по размеру активов. Дополнительная информация в [официальной документации Apple](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImage_Class/index.html#//apple_ref/occ/instm/UIImage/resizableImageWithCapInsets).

| Type            |
| --------------- |
| [Rect](rect.md) |

### `crossOrigin`

Строка ключевого слова, указывающая режим CORS, который следует использовать при получении ресурса изображения. Работает аналогично атрибуту crossorigin в HTML.

-   `anonymous`: Нет обмена учетными данными пользователя в запросе изображения.
-   `use-credentials`: Устанавливает значение заголовка `Access-Control-Allow-Credentials` на `true` в запросе изображения.

| Type                                     | Default       |
| ---------------------------------------- | ------------- |
| enum(`'anonymous'`, `'use-credentials'`) | `'anonymous'` |

### `defaultSource`

Статическая картинка для отображения во время загрузки источника изображения.

| Type                                |
| ----------------------------------- |
| [ImageSource](image.md#imagesource) |

!!!warning ""

    **Примечание:** На Android в отладочных сборках игнорируется исходный реквизит по умолчанию.

### `fadeDuration` :simple-android:

Продолжительность анимации затухания в миллисекундах.

| Type   | Default |
| ------ | ------- |
| number | `300`   |

### `height`

Высота компонента изображения.

| Type   |
| ------ |
| number |

### `loadingIndicatorSource`

| Type                                                     |
| -------------------------------------------------------- |
| [ImageSource](image.md#imagesource) (`uri` only), number |

### `onError`

Вызывается при ошибке загрузки.

| Type                                |
| ----------------------------------- |
| (`{nativeEvent: {error} }`) => void |

### `onLayout`

Вызывается при монтировании и при смене раскладки.

| Type                                                |
| --------------------------------------------------- |
| `({nativeEvent: [LayoutEvent]layoutevent}) => void` |

### `onLoad`

Вызывается при успешном завершении загрузки.

**Пример:** `onLoad={({nativeEvent: {source: {width, height}}}) => setImageRealSize({width, height})}`.

| Type                                                          |
| ------------------------------------------------------------- |
| ({nativeEvent: [ImageLoadEvent]image#imageloadevent}) => void |

### `onLoadEnd`

Вызывается при успешной или неудачной загрузке.

| Type       |
| ---------- |
| () => void |

### `onLoadStart`

Вызывается при начале загрузки.

**Пример:** `onLoadStart={() => this.setState({loading: true})}`.

| Type       |
| ---------- |
| () => void |

### `onPartialLoad` :simple-ios:

Вызывается, когда частичная загрузка изображения завершена. Определение того, что является "частичной загрузкой", зависит от конкретного загрузчика, но это предназначено для прогрессивной загрузки JPEG.

| Type       |
| ---------- |
| () => void |

### `onProgress`

Вызывается при выполнении загрузки.

| Type                                        |
| ------------------------------------------- |
| (`{nativeEvent: {loaded, total} }`) => void |

### `progressiveRenderingEnabled` :simple-android:

Если `true`, включает [прогрессивную потоковую передачу jpeg](https://frescolib.org/docs/progressive-jpegs).

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `resizeMethod` :simple-android:

Механизм, который должен использоваться для изменения размера изображения, когда размеры изображения отличаются от размеров представления изображения. По умолчанию используется `auto`.

-   `auto`: Используйте эвристику для выбора между `resize` и `scale`.
-   `resize`: Программная операция, которая изменяет закодированное изображение в памяти до того, как оно будет декодировано. Это следует использовать вместо `scale`, когда изображение намного больше, чем вид.
-   `scale`: Изображение рисуется уменьшенным или увеличенным. По сравнению с `resize`, `scale` работает быстрее (обычно с аппаратным ускорением) и создает изображения более высокого качества. Этот параметр следует использовать, если изображение меньше, чем вид. Его также следует использовать, если изображение немного больше, чем вид.

Более подробно о `resize` и `scale` можно [узнать на сайте](http://frescolib.org/docs/resizing).

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'resize'`, `'scale'`) | `'auto'` |

### `referrerPolicy`

Строка, указывающая, какой реферер использовать при получении ресурса. Устанавливает значение для заголовка `Referrer-Policy` в запросе изображения. Работает аналогично атрибуту `referrerpolicy` в HTML.

| Type                                                                                                                                                                                     | Default                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| enum(`'no-referrer'`, `'no-referrer-when-downgrade'`, `'origin'`, `'origin-when-cross-origin'`, `'same-origin'`, `'strict-origin'`, `'strict-origin-when-cross-origin'`, `'unsafe-url'`) | `'strict-origin-when-cross-origin'` |

### `resizeMode`

Определяет, как изменить размер изображения, если рамка не соответствует размерам необработанного изображения. По умолчанию имеет значение `cover`.

-   `cover`: Масштабируйте изображение равномерно (сохраняя соотношение сторон изображения) так, чтобы
    -   оба размера (ширина и высота) изображения были равны или больше соответствующего размера представления (минус подложка)
    -   по крайней мере одно измерение масштабированного изображения будет равно соответствующему измерению представления (минус padding).
-   `contain`: Масштабировать изображение равномерно (сохраняя соотношение сторон изображения) так, чтобы оба размера (ширина и высота) изображения были равны или меньше соответствующего размера представления (минус подложка).
-   `stretch`: Масштабировать ширину и высоту независимо, это может изменить соотношение сторон src.
-   `repeat`: Повторять изображение, чтобы покрыть рамку представления. Изображение сохранит свой размер и соотношение сторон, если только оно не больше, чем вид, в этом случае оно будет равномерно уменьшено, чтобы оно поместилось в вид.
-   `center`: Центрирует изображение в представлении по обоим измерениям. Если изображение больше, чем вид, равномерно уменьшите его масштаб так, чтобы оно поместилось в вид.

| Type                                                              | Default   |
| ----------------------------------------------------------------- | --------- |
| enum(`'cover'`, `'contain'`, `'stretch'`, `'repeat'`, `'center'`) | `'cover'` |

### `source`

Источник изображения (либо удаленный URL, либо локальный файловый ресурс).

Этот реквизит также может содержать несколько удаленных URL, указанных вместе с их шириной и высотой и, возможно, с аргументами scale/другими URI. Родная сторона будет выбирать лучший `uri` для отображения, основываясь на измеренном размере контейнера изображения. Свойство `cache` может быть добавлено для управления тем, как сетевой запрос взаимодействует с локальным кэшем. (Для получения дополнительной информации смотрите [Cache Control for Images](../guides/images.md#cache-control-ios-only)).

В настоящее время поддерживаются следующие форматы: `png`, `jpg`, `jpeg`, `bmp`, `gif`, `webp`, `psd` (только для iOS). Кроме того, iOS поддерживает несколько форматов изображений RAW. Актуальный список поддерживаемых моделей камер см. [в документации Apple](https://support.apple.com/en-ca/HT208967).

| Type                                |
| ----------------------------------- |
| [ImageSource](image.md#imagesource) |

### `src`

Строка, представляющая удаленный URL изображения. Этот параметр имеет приоритет перед параметром `source`.

**Пример:** `src={'https://reactnative.dev/img/tiny_logo.png'}`.

| Type   |
| ------ |
| string |

### `srcSet`

Строка, представляющая собой разделенный запятыми список возможных источников изображений-кандидатов. Каждый источник изображения содержит URL-адрес изображения и дескриптор плотности пикселей. Если дескриптор не указан, по умолчанию используется дескриптор `1x`.

Если `srcSet` не содержит дескриптора `1x`, значение в `src` используется в качестве источника изображения с дескриптором `1x` (если он предоставлен).

Этот реквизит имеет приоритет над реквизитами `src` и `source`.

**Пример:** `srcSet={'https://reactnative.dev/img/tiny_logo.png 1x, https://reactnative.dev/img/header_logo.svg 2x'}`.

| Type   |
| ------ |
| string |

### `style`

| Type                                                                                                                                                                    |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Image Style Props](image-style-props.md#props), [Layout Props](layout-props.md#props), [Shadow Props](shadow-props.md#props), [Transforms](../api/transforms.md#props) |

### `testID`

Уникальный идентификатор для этого элемента, который будет использоваться в сценариях тестирования автоматизации пользовательского интерфейса.

| Type   |
| ------ |
| string |

### `tintColor`

Изменяет цвет всех непрозрачных пикселей на `tintColor`.

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |

### `width`

Ширина компонента изображения.

| Type   |
| ------ |
| number |

## Методы

### `abortPrefetch()` :simple-android:

```ts
static abortPrefetch(requestId: number);
```

Прервать предварительный запрос.

**Параметры:**

| Name                                                       | Type   | Description                             |
| ---------------------------------------------------------- | ------ | --------------------------------------- |
| requestId <div class="label basic required">Required</div> | number | Request id as returned by `prefetch()`. |

### `getSize()`

```ts
static getSize(
  uri: string,
  success: (width: number, height: number) => void,
  failure?: (error: any) => void,
): any;
```

Получение ширины и высоты (в пикселях) изображения перед его отображением. Этот метод может не сработать, если изображение не может быть найдено или не удалось загрузить.

Для получения размеров изображения может потребоваться сначала загрузить или скачать изображение, после чего оно будет кэшировано. Это означает, что в принципе вы можете использовать этот метод для предварительной загрузки изображений, однако он не оптимизирован для этой цели и в будущем может быть реализован таким образом, чтобы не полностью загружать/скачивать данные изображения. Правильный, поддерживаемый способ предварительной загрузки изображений будет предоставлен в виде отдельного API.

**Параметры:**

| Name             | Type     | Description                                                                                          |
| ---------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| uri Required     | string   | The location of the image.                                                                           |
| success Required | function | The function that will be called if the image was successfully found and width and height retrieved. |
| failure          | function | The function that will be called if there was an error, such as failing to retrieve the image.       |

### `getSizeWithHeaders()`

```ts
static getSizeWithHeaders(
  uri: string,
  headers: {[index: string]: string},
  success: (width: number, height: number) => void,
  failure?: (error: any) => void,
): any;
```

Получение ширины и высоты (в пикселях) изображения перед его отображением с возможностью предоставления заголовков для запроса. Этот метод может не сработать, если изображение не может быть найдено или не удалось загрузить. Он также не работает для ресурсов статических изображений.

Для получения размеров изображения может потребоваться сначала загрузить или скачать изображение, после чего оно будет кэшировано. Это означает, что в принципе вы можете использовать этот метод для предварительной загрузки изображений, однако он не оптимизирован для этой цели и в будущем может быть реализован таким образом, чтобы не полностью загружать/скачивать данные изображения. Правильный, поддерживаемый способ предварительной загрузки изображений будет предоставлен в виде отдельного API.

**Параметры:**

| Name             | Type     | Description                                                                                          |
| ---------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| uri Required     | string   | The location of the image.                                                                           |
| headers Required | object   | The headers for the request.                                                                         |
| success Required | function | The function that will be called if the image was successfully found and width and height retrieved. |
| failure          | function | The function that will be called if there was an error, such as failing to retrieve the image.       |

### `prefetch()`

```ts
await Image.prefetch(url);
```

Предварительная выборка удаленного изображения для последующего использования путем загрузки его в дисковый кэш. Возвращает обещание, которое разрешается в булево значение.

**Параметры:**

| Name         | Type                      | Description                                            |
| ------------ | ------------------------- | ------------------------------------------------------ |
| url Required | string                    | The remote location of the image.                      |
| callback     | function :simple-android: | The function that will be called with the `requestId`. |

### `queryCache()`

```ts
static queryCache(
  urls: string[],
): Promise<Record<string, 'memory' | 'disk' | 'disk/memory'>>;
```

Выполните опрос кэша. Возвращает обещание, которое разрешается в отображение от URL к состоянию кэша, например "диск", "память" или "диск/память". Если запрашиваемого URL нет в связке, это означает, что его нет в кэше.

**Параметры:**

| Name          | Type  | Description                                |
| ------------- | ----- | ------------------------------------------ |
| urls Required | array | List of image URLs to check the cache for. |

### `resolveAssetSource()`

```ts
static resolveAssetSource(source: ImageSourcePropType): {
  height: number;
  width: number;
  scale: number;
  uri: string;
};
```

Переводит ссылку на актив в объект, который имеет свойства `uri`, `scale`, `width` и `height`.

**Параметры:**

| Name            | Type                                        | Description                                                                  |
| --------------- | ------------------------------------------- | ---------------------------------------------------------------------------- |
| source Required | [ImageSource](image.md#imagesource), number | A number (opaque type returned by `require('./foo.png')`) or an ImageSource. |

## Определения типа

### `ImageCacheEnum` :simple-ios:

Перечисление, которое можно использовать для установки обработки кэша или стратегии для потенциально кэшируемых ответов.

| Type                                                               | Default     |
| ------------------------------------------------------------------ | ----------- |
| enum(`'default'`, `'reload'`, `'force-cache'`, `'only-if-cached'`) | `'default'` |

-   `default`: Использовать стратегию по умолчанию родной платформы.
-   `reload`: Данные для URL будут загружены из исходного источника. Никакие существующие данные кэша не должны использоваться для удовлетворения запроса на загрузку URL.
-   `force-cache`: Существующие кэшированные данные будут использованы для удовлетворения запроса, независимо от их возраста или срока действия. Если в кэше нет существующих данных, соответствующих запросу, данные загружаются из исходного источника.
-   `only-if-cached`: Существующие данные в кэше будут использованы для удовлетворения запроса, независимо от их возраста или срока действия. Если в кэше, соответствующем запросу на загрузку URL, нет существующих данных, попытка загрузить данные из исходного источника не предпринимается, и загрузка считается неудачной.

### ImageLoadEvent

Объект, возвращаемый в обратном вызове `onLoad`.

| Type   |
| ------ |
| object |

**Свойства:**

| Name   | Type   | Description       |
| ------ | ------ | ----------------- |
| source | object | The source object |

#### Исходный объект

**Свойства:**

| Name   | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| width  | number | The width of loaded image.                                   |
| height | number | The height of loaded image.                                  |
| uri    | string | A string representing the resource identifier for the image. |

### ImageSource

| Type                             |
| -------------------------------- |
| object, array of objects, number |

**Свойства (при передаче в виде объекта или массива объектов):**.

| Name               | Type                                          | Description                                                                                                                                                                          |
| ------------------ | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| uri                | string                                        | A string representing the resource identifier for the image, which could be an http address, a local file path, or the name of a static image resource.                              |
| width              | number                                        | Can be specified if known at build time, in which case the value will be used to set the default `<Image/>` component dimension.                                                     |
| height             | number                                        | Can be specified if known at build time, in which case the value will be used to set the default `<Image/>` component dimension.                                                     |
| scale              | number                                        | Used to indicate the scale factor of the image. Defaults to `1.0` if unspecified, meaning that one image pixel equates to one display point / DIP.                                   |
| bundle:simple-ios: | string                                        | The iOS asset bundle which the image is included in. This will default to `[NSBundle mainBundle]` if not set.                                                                        |
| method             | string                                        | The HTTP Method to use. Defaults to `'GET'` if not specified.                                                                                                                        |
| headers            | object                                        | An object representing the HTTP headers to send along with the request for a remote image.                                                                                           |
| body               | string                                        | The HTTP body to send with the request. This must be a valid UTF-8 string, and will be sent exactly as specified, with no additional encoding (e.g. URL-escaping or base64) applied. |
| cache:simple-ios:  | [ImageCacheEnum](image.md#imagecacheenum-ios) | Determines how the requests handles potentially cached responses.                                                                                                                    |

**При передаче числа:**

-   `number` — непрозрачный тип, возвращаемый чем-то вроде `require('./image.jpg')`.
