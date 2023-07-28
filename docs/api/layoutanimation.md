---
description: Автоматически анимирует представления в их новые позиции, когда происходит следующая компоновка
---

# LayoutAnimation

Автоматически анимирует представления в их новые позиции, когда происходит следующая компоновка.

Обычный способ использования этого API — вызвать его перед обновлением хука состояния в функциональных компонентах и вызовом `setState` в компонентах класса.

Обратите внимание, что для того, чтобы это работало на **Android**, необходимо установить следующие флаги через `UIManager`:

```js
if (Platform.OS === 'android') {
    if (UIManager.setLayoutAnimationEnabledExperimental) {
        UIManager.setLayoutAnimationEnabledExperimental(
            true
        );
    }
}
```

## Пример {#example}

<div data-snack-id="@bndby/layoutanimation" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы {#methods}

### configureNext()

```ts
static configureNext(
  config: LayoutAnimationConfig,
  onAnimationDidEnd?: () => void,
  onAnimationDidFail?: () => void,
);
```

Планирует анимацию на следующий макет.

**Параметры:**

| Имя                    | Тип        | Описание                                |
| ---------------------- | ---------- | --------------------------------------- |
| `config` (обязательно) | `object`   | См. описание конфигурации ниже.         |
| `onAnimationDidEnd`    | `function` | Вызывается при завершении анимации.     |
| `onAnimationDidFail`   | `function` | Вызывается, когда анимация завершилась. |

Параметр `config` представляет собой объект с ключами, указанными ниже. [`create`](layoutanimation.md#create) возвращает правильный объект для `config`, и объекты [`Presets`](layoutanimation.md#presets) также могут быть переданы в качестве `config`.

-   `duration` в миллисекундах
-   `create`, необязательная конфигурация для анимации в новых представлениях
-   `update`, необязательный конфиг для анимации представлений, которые были обновлены
-   `delete`, необязательная конфигурация для анимации представлений при их удалении

Конфиг, передаваемый в `create`, `update` или `delete`, имеет следующие ключи:

-   `type`, используемый [тип анимации](layoutanimation.md#types)
-   `property`, свойство [layout property](layoutanimation.md#properties) для анимации (необязательно, но рекомендуется для `create` и `delete`)
-   `springDamping` (число, необязательное и только для использования с `type: Type.spring`)
-   `initialVelocity` (число, необязательное)
-   `delay` (число, необязательно)
-   `duration` (число, необязательно)

### create()

```ts
static create(duration, type, creationProp)
```

Помощник, создающий объект (с полями `create`, `update` и `delete`) для передачи в [`configureNext`](layoutanimation.md#configurenext). Параметр `type` — это [тип анимации](layoutanimation.md#types), а параметр `creationProp` — это [свойство макета](layoutanimation.md#properties).

**Пример:**

<div data-snack-id="@bndby/layoutanimation-2" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Свойства {#properties}

### Types

Перечисление типов анимации для использования в методе [`create`](layoutanimation.md#create) или в конфигурациях `create`/`update`/`delete` для [`configureNext`](layoutanimation.md#configurenext). (пример использования: `LayoutAnimation.Types.easeIn`)

| Types           |
| --------------- |
| `spring`        |
| `linear`        |
| `easeInEaseOut` |
| `easeIn`        |
| `easeOut`       |
| `keyboard`      |

### Properties

Перечисление свойств макета, которые должны быть анимированы для использования в методе [`create`](layoutanimation.md#create) или в конфигурациях `create`/`update`/`delete` для [`configureNext`](layoutanimation.md#configurenext). (пример использования: `LayoutAnimation.Properties.opacity`)

| Свойства  |
| --------- |
| `opacity` |
| `scaleX`  |
| `scaleY`  |
| `scaleXY` |

### Предустановки {#presets}

Набор предопределенных конфигураций анимации для передачи в [`configureNext`](layoutanimation.md#configurenext).

| Предустановки   | Значение                                                                                                                                                       |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `easeInEaseOut` | `create(300, 'easeInEaseOut', 'opacity')`                                                                                                                      |
| `linear`        | `create(500, 'linear', 'opacity')`                                                                                                                             |
| `spring`        | `{duration: 700, create: {type: 'linear', property: 'opacity'}, update: {type: 'spring', springDamping: 0.4}, delete: {type: 'linear', property: 'opacity'} }` |

### easeInEaseOut

Вызывает `configureNext()` с `Presets.easeInEaseOut`.

### linear

Вызывает `configureNext()` с `Presets.linear`.

### spring

Вызывает `configureNext()` с `Presets.spring`.

**Пример:**

<div data-snack-id="@bndby/layoutanimation-3" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>
