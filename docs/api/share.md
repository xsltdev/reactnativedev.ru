# Share

## Пример

=== "TypeScript"

    <div data-snack-id="@bndby/ts-share" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-share" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `share()`

```ts
static share(content: ShareContent, options?: ShareOptions);
```

Открывает диалог для обмена текстовым содержимым.

В iOS возвращает Promise, который будет вызван с объектом, содержащим `action` и `activityType`. Если пользователь отклонил диалог, Promise все равно будет разрешен с действием `Share.dismissedAction` и всеми остальными ключами неопределенными. Обратите внимание, что некоторые опции акций не будут отображаться или работать на симуляторе iOS.

В Android возвращает обещание, которое всегда будет разрешено с действием `Share.sharedAction`.

**Свойства:**

| Name                  | Type   | Description                                                                                                                                                                                                                                                      |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| content (обязательно) | object | `message` — a message to share<br/>`url` — a URL to share :simple-ios:<br/>`title` — title of the message :simple-android:<hr/>At least one of `url` and `message` is required.                                                                                  |
| options               | object | `dialogTitle` :simple-android:<br/>`excludedActivityTypes` :simple-ios:<br/>`subject` — a subject to share via email :simple-ios:<br/>`tintColor` :simple-ios:<br/>`anchor` — the node to which the action sheet should be anchored (used for iPad) :simple-ios: |

## Свойства

### `sharedAction`

```ts
static sharedAction: 'sharedAction';
```

Контент был успешно передан.

### `dismissedAction` :simple-ios:

```ts
static dismissedAction: 'dismissedAction';
```

Диалог был прекращен.
