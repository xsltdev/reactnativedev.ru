# Systrace

**`Systrace`** — это стандартный инструмент профилирования на основе маркеров для Android (и устанавливается при установке пакета Android platform-tools). Профилированные блоки кода окружены маркерами начала/конца, которые затем визуализируются в виде красочной диаграммы. И Android SDK, и фреймворк React Native предоставляют стандартные маркеры, которые можно визуализировать.

## Пример

`Systrace` позволяет отмечать события JavaScript (JS) с помощью тега и целочисленного значения. Захватывайте события JS без тайминга в EasyProfiler.

<div data-snack-id="@bndby/systrace-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы

### `isEnabled()`

```ts
static isEnabled(): boolean;
```

### `beginEvent()`

```ts
static beginEvent(eventName: string | (() => string), args?: EventArgs);
```

`beginEvent`/`endEvent` для запуска и последующего завершения профиля в одном и том же кадре стека вызовов.

### `endEvent()`

```ts
static endEvent(args?: EventArgs);
```

### `beginAsyncEvent()`

```ts
static beginAsyncEvent(
  eventName: string | (() => string),
  args?: EventArgs,
): number;
```

`beginAsyncEvent`/`endAsyncEvent` для запуска и последующего завершения профиля, когда завершение может произойти либо в другом потоке, либо вне текущего стекового кадра, например, ожидание возвращенной переменной `cookie` должно быть использовано как вход в вызов `endAsyncEvent` для завершения профиля.

### `endAsyncEvent()`

```ts
static endAsyncEvent(
  eventName: EventName,
  cookie: number,
  args?: EventArgs,
);
```

### `counterEvent()`

```ts
static counterEvent(eventName: string | (() => string), value: number);
```

Зарегистрируйте значение в `profileName` на временной шкале `systrace`.
