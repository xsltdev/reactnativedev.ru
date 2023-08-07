---
description: InteractionManager позволяет планировать выполнение длительных задач после завершения всех взаимодействий и анимаций
---

# InteractionManager

**`InteractionManager`** позволяет планировать выполнение длительных задач после завершения всех взаимодействий/анимаций. В частности, это позволяет JavaScript-анимации работать плавно.

Приложения могут планировать выполнение задач после завершения взаимодействий с помощью следующего кода:

```ts
InteractionManager.runAfterInteractions(() => {
    // ...long-running synchronous task...
});
```

Сравните это с другими альтернативами планирования:

-   `requestAnimationFrame()` для кода, который анимирует представление во времени.
-   `setImmediate/setTimeout()` запуск кода позже, обратите внимание, что это может задержать анимацию.
-   `runAfterInteractions()` запускает код позже, не задерживая активные анимации.

Система обработки касаний считает одно или несколько активных касаний "взаимодействием" и откладывает вызов `runAfterInteractions()` до тех пор, пока все касания не закончатся или не будут отменены.

`InteractionManager` также позволяет приложениям регистрировать анимации, создавая "хэндл" взаимодействия при запуске анимации и очищая его по завершении:

```ts
const handle = InteractionManager.createInteractionHandle();
// run animation... (`runAfterInteractions` tasks are queued)
// later, on animation completion:
InteractionManager.clearInteractionHandle(handle);
// queued tasks run if all handles were cleared
```

`runAfterInteractions` принимает либо обычную функцию обратного вызова, либо объект `PromiseTask` с методом `gen`, который возвращает `Promise`. Если передана `PromiseTask`, то она полностью решается (включая асинхронные зависимости, которые также планируют дополнительные задачи через `runAfterInteractions`) перед началом выполнения следующей задачи, которая могла быть поставлена в очередь синхронно ранее.

По умолчанию, поставленные в очередь задачи выполняются вместе в цикле в одном пакете `setImmediate`. Если `setDeadline` вызывается с положительным числом, то задачи будут выполняться только до тех пор, пока не наступит крайний срок (в терминах времени выполнения цикла событий js), после чего выполнение завершится через setTimeout, позволяя таким событиям, как касания, начать взаимодействие и блокировать выполнение поставленных в очередь задач, делая приложения более отзывчивыми.

## Пример {#example}

### Основной {#basic}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-interactionmanager-function-component-basic-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-interactionmanager-function-component-basic-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Продвинутый пример {#advanced}

=== "TypeScript"

    <div data-snack-id="@bndby/ts-interactionmanager-function-component-advanced-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-interactionmanager-function-component-advanced-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

!!!note "Примечание"

    `InteractionManager.runAfterInteractions()` не работает должным образом в веб. Он срабатывает немедленно, не дожидаясь завершения взаимодействия.

## Методы {#methods}

### runAfterInteractions()

```ts
static runAfterInteractions(task?: (() => any) | SimpleTask | PromiseTask);
```

Запланируйте выполнение функции после завершения всех взаимодействий. Возвращает отменяемое "обещание".

### createInteractionHandle()

```ts
static createInteractionHandle(): Handle;
```

Уведомление менеджера о начале взаимодействия.

### clearInteractionHandle()

```ts
static clearInteractionHandle(handle: Handle);
```

Уведомление менеджера о завершении взаимодействия.

### setDeadline()

```ts
static setDeadline(deadline: number);
```

Положительное число будет использовать `setTimeout` для планирования любых задач после того, как `eventLoopRunningTime` достигнет конечного значения, иначе все задачи будут выполнены в одном пакете `setImmediate` (по умолчанию).
