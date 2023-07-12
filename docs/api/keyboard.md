# Keyboard

Модуль **`Keyboard`** для управления событиями клавиатуры.

## Использование

Модуль Keyboard позволяет прослушивать события клавиатуры и реагировать на них, а также вносить изменения в клавиатуру, например, отключать ее.

<div data-snack-id="@bndby/keyboard-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `addListener()`

```ts
static addListener: (
  eventType: KeyboardEventName,
  listener: KeyboardEventListener,
) => EmitterSubscription;
```

Функция `addListener` подключает функцию JavaScript к идентифицированному событию уведомления клавиатуры.

Затем эта функция возвращает ссылку на слушателя.

**Параметры:**

| Name                    | Type     | Description                                                                    |
| ----------------------- | -------- | ------------------------------------------------------------------------------ |
| eventName (обязательно) | string   | The string that identifies the event you're listening for. See the list below. |
| callback (обязательно)  | function | The function to be called when the event fires                                 |

**`eventName`**

Это может быть любой из следующих вариантов:

-   `keyboardWillShow`
-   `keyboardDidShow`
-   `keyboardWillHide`
-   `keyboardDidHide`
-   `keyboardWillChangeFrame`
-   `keyboardDidChangeFrame`

!!!note ""

    Обратите внимание, что на Android доступны только события `keyboardDidShow` и `keyboardDidHide`. Эти события не будут срабатывать при использовании Android 10 и ниже, если для вашей активности `android:windowSoftInputMode` установлено значение `adjustNothing`.

### `dismiss()`

```ts
static dismiss();
```

Отключает активную клавиатуру и снимает фокус.

### `scheduleLayoutAnimation`

```ts
static scheduleLayoutAnimation(event: KeyboardEvent);
```

Используется для синхронизации изменений размера позиции `TextInput` (или другого представления клавиатурного аксессуара) с движениями клавиатуры.

### `isVisible()`

```ts
static isVisible(): boolean;
```

Когда клавиатура была видна в последний раз.

### `metrics()`

```ts
static metrics(): KeyboardMetrics | undefined;
```

Возвращает метрику софт-клавиатуры, если она видна.
