---
description: Share открывает диалог для обмена текстовым содержимым
---

# Share

## Пример {#example}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-share" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-share" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы {#methods}

### share()

```ts
static share(content: ShareContent, options?: ShareOptions);
```

Открывает диалог для обмена текстовым содержимым.

В iOS возвращает `Promise`, который будет вызван с объектом, содержащим `action` и `activityType`. Если пользователь отклонил диалог, `Promise` все равно будет разрешен с действием `Share.dismissedAction` и всеми остальными ключами неопределенными. Обратите внимание, что некоторые опции акций не будут отображаться или работать на симуляторе iOS.

В Android возвращает `Promise`, которое всегда будет разрешено с действием `Share.sharedAction`.

**Свойства:**

| Имя                     | Тип      | Описание                                                                                                                                                                                                                                                                       |
| ----------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `content` (обязательно) | `object` | `message` — сообщение для обмена<br/>`url` — URL-адрес для обмена :simple-ios:<br/>`title` — заголовок сообщения :simple-android:<hr/>Требуется хотя бы одно из значений `url` и `message`.                                                                                    |
| `options`               | `object` | `dialogTitle` :simple-android:<br/>`excludedActivityTypes` :simple-ios:<br/>`subject` — тема для обмена по электронной почте :simple-ios:<br/>`tintColor` :simple-ios:<br/>`anchor` — узел, к которому должен быть привязан лист действия (используется для iPad) :simple-ios: |

## Свойства {#properties}

### sharedAction

```ts
static sharedAction: 'sharedAction';
```

Контент был успешно передан.

### dismissedAction :simple-ios:

```ts
static dismissedAction: 'dismissedAction';
```

Диалог был прекращен.
