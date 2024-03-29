---
description: Settings служит оберткой для NSUserDefaults, постоянного хранилища ключевых значений, доступного только на iOS
---

# Settings

**`Settings`** служит оберткой для [`NSUserDefaults`](https://developer.apple.com/documentation/foundation/nsuserdefaults), постоянного хранилища ключевых значений, доступного только на iOS.

## Пример {#example}

<div data-snack-id="@bndby/settings-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы {#methods}

### clearWatch()

```ts
static clearWatch(watchId: number);
```

`watchId` — это номер, возвращенный функцией `watchKeys()` при первоначальной настройке подписки.

### get()

```ts
static get(key: string): any;
```

Получение текущего значения для заданного `key` в `NSUserDefaults`.

### set()

```ts
static set(settings: Record<string, any>);
```

Установите одно или несколько значений в `NSUserDefaults`.

### watchKeys()

```ts
static watchKeys(keys: string | array<string>, callback: () => void): number;
```

Подписка на уведомление об изменении значения любого из ключей, указанных параметром `keys`, в `NSUserDefaults`. Возвращает номер `watchId`, который может быть использован с `clearWatch()` для отмены подписки.

!!!note "Примечание"

    `watchKeys()` по своей конструкции игнорирует внутренние вызовы `set()` и запускает обратный вызов только на изменения, выполненные вне кода React Native.
