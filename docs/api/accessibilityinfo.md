# AccessibilityInfo

Иногда полезно знать, есть ли на устройстве активное устройство для чтения с экрана. API `AccessibilityInfo` предназначен для этой цели. Вы можете использовать его для запроса текущего состояния устройства чтения экрана, а также для регистрации, чтобы получать уведомления об изменении состояния устройства чтения экрана.

## Пример

<div data-snack-id="@bndby/accessibilityinfo-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `addEventListener()`

```tsx
static addEventListener(
  eventName: AccessibilityChangeEventName | AccessibilityAnnouncementEventName,
  handler: (
    event: AccessibilityChangeEvent | AccessibilityAnnouncementFinishedEvent,
  ) => void,
): EmitterSubscription;
```

Добавьте обработчик события. Поддерживаемые события:

| Событие                                                                              | Описание                                                                                                                                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `accessibilityServiceChanged`<br/><div class="label two-lines android">Android</div> | Срабатывает при включении некоторых служб, таких как TalkBack, других вспомогательных технологий Android и служб доступности сторонних производителей. Аргументом обработчика события является булево значение. Это булево значение равно `true`, если некоторые службы доступности включены, и `false` в противном случае. |
| `announcementFinished`<br/><div class="label two-lines ios">iOS</div>                | Срабатывает, когда устройство чтения с экрана закончило делать объявление. Аргументом обработчика события является словарь с такими ключами:<ul><li>`announcement`: Строка, объявленная устройством чтения с экрана.</li><li>`success`: Булево значение, указывающее, было ли объявление успешно сделано.</li></ul>         |
| `boldTextChanged`<br/><div class="label two-lines ios">iOS</div>                     | Срабатывает при изменении состояния переключателя полужирного текста. Аргументом обработчика события является булево значение. Булево равно `true`, если полужирный текст включен, и `false` в противном случае.                                                                                                            |
| `grayscaleChanged`<br/><div class="label two-lines ios">iOS</div>                    | Срабатывает при изменении состояния переключателя шкалы серого. Аргументом обработчика события является булево значение. Булево равно `true`, если включена шкала серого, и `false` в противном случае.                                                                                                                     |
| `invertColorsChanged`<br/><div class="label two-lines ios">iOS</div>                 | Срабатывает при изменении состояния переключателя инвертирования цветов. Аргументом обработчика события является булево значение. Булево равно `true`, если инвертирование цветов включено, и `false` в противном случае.                                                                                                   |
| `reduceMotionChanged`                                                                | Срабатывает при изменении состояния переключателя уменьшения движения. Аргументом обработчика события является булево значение. Булево `true`, когда движение уменьшения включено (или когда "Transition Animation Scale" в "Developer options" имеет значение "Animation off") и `false` в противном случае.               |
| `reduceTransparencyChanged`<br/><div class="label two-lines ios">iOS</div>           | Срабатывает при изменении состояния переключателя прозрачности уменьшения. Аргументом обработчика события является булево значение. Булево равно `true`, если прозрачность уменьшения включена, и `false` в противном случае.                                                                                               |
| `screenReaderChanged`                                                                | Срабатывает при изменении состояния устройства чтения с экрана. Аргументом обработчика события является булево значение. Булево равно `true`, если устройство чтения с экрана включено, и `false` в противном случае.                                                                                                       |

### `announceForAccessibility()`.

```tsx
static announceForAccessibility(announcement: string);
```

Разместите строку, которая будет озвучена устройством чтения с экрана.

### `announceForAccessibilityWithOptions()`.

```tsx
static announceForAccessibilityWithOptions(
  announcement: string,
  options: options: {queue?: boolean},
);
```

Поместите строку для объявления считывателем экрана с параметрами модификации. По умолчанию объявления будут прерывать любую существующую речь, но на iOS их можно поставить в очередь за существующей речью, установив `queue` в `true` в объекте options.

**Параметры:**

| Name                                                          | Type   | Description                                                          |
| ------------------------------------------------------------- | ------ | -------------------------------------------------------------------- |
| announcement <div class="label basic required">Required</div> | string | The string to be announced                                           |
| options <div class="label basic required">Required</div>      | object | `queue` - queue the announcement behind existing speech :simple-ios: |

### `getRecommendedTimeoutMillis()` :simple-android:

```tsx
static getRecommendedTimeoutMillis(originalTimeout: number): Promise<number>;
```

Получает тайм-аут в миллисекундах, который необходим пользователю.
Это значение устанавливается в параметре "Время для принятия действия (таймаут доступности)" настроек "Доступность".

**Параметры:**

| Name                                                             | Type   | Description                                                                           |
| ---------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| originalTimeout <div class="label basic required">Required</div> | number | The timeout to return if "Accessibility timeout" is not set. Specify in milliseconds. |

### `isAccessibilityServiceEnabled()` :simple-android:

```tsx
static isAccessibilityServiceEnabled(): Promise<boolean>;
```

Проверьте, включена ли какая-либо служба доступности. Сюда входит TalkBack, а также любое стороннее приложение доступности, которое может быть установлено. Чтобы проверить, включен ли только TalkBack, используйте `isScreenReaderEnabled`. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если некоторые службы доступности включены, и `false` в противном случае.

!!!note "Примечание"

    Пожалуйста, используйте `isScreenReaderEnabled`, если вы хотите проверить только состояние TalkBack.

### `isBoldTextEnabled()` :simple-ios:

```tsx
static isBoldTextEnabled(): Promise<boolean>:
```

Запрос о том, включен ли в данный момент полужирный текст. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если полужирный текст включен, и `false` в противном случае.

### `isGrayscaleEnabled()` :simple-ios:

```tsx
static isGrayscaleEnabled(): Promise<boolean>;
```

Запрос о том, включена ли в данный момент шкала серого. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если шкала серого включена, и `false` в противном случае.

### `isInvertColorsEnabled()` :simple-ios:

```tsx
static isInvertColorsEnabled(): Promise<boolean>;
```

Запрос о том, включена ли в данный момент функция инвертирования цветов. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если инвертирование цветов включено, и `false` в противном случае.

### `isReduceMotionEnabled()`

```tsx
static isReduceMotionEnabled(): Promise<boolean>;
```

Запрос о том, включено ли в данный момент уменьшение движения. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если движение уменьшения включено, и `false` в противном случае.

### `isReduceTransparencyEnabled()` :simple-ios:

```tsx
static isReduceTransparencyEnabled(): Promise<boolean>;
```

Query whether reduce transparency is currently enabled. Returns a promise which resolves to a boolean. The result is `true` when a reduce transparency is enabled and `false` otherwise.

### `isScreenReaderEnabled()`.

```tsx
static isScreenReaderEnabled(): Promise<boolean>;
```

Запрос о том, включено ли в данный момент устройство чтения с экрана. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если устройство чтения с экрана включено, и `false` в противном случае.

### `prefersCrossFadeTransitions()` :simple-ios:

```tsx
static prefersCrossFadeTransitions(): Promise<boolean>;
```

Запрос о том, включены ли в данный момент настройки уменьшения движения и предпочтения переходов кроссфейда. Возвращает обещание, которое разрешается в булево число. Результат будет `true`, если предпочтение переходов кроссфейда включено, и `false` в противном случае.

### `setAccessibilityFocus()`.

```tsx
static setAccessibilityFocus(reactTag: number);
```

Установка фокуса доступности для компонента React.

На Android для этого вызывается метод `UIManager.sendAccessibilityEvent` с переданными аргументами `reactTag` и `UIManager.AccessibilityEventTypes.typeViewFocused`.

!!!note "Примечание"

    Убедитесь, что любой `View`, который вы хотите получить фокус доступности, имеет `accessible={true}`.
