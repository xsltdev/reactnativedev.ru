---
description: Модуль DevSettings раскрывает методы для настройки параметров для разработчиков в процессе разработки
---

# DevSettings

Модуль **`DevSettings`** раскрывает методы для настройки параметров для разработчиков в процессе разработки.

## Методы {#methods}

### addMenuItem()

```ts
static addMenuItem(title: string, handler: () => any);
```

Добавьте пользовательский пункт меню в меню Dev.

**Параметры:**

| Имя                     | Тип        |
| ----------------------- | ---------- |
| `title` (обязательно)   | `string`   |
| `handler` (обязательно) | `function` |

**Пример:**

```ts
DevSettings.addMenuItem('Show Secret Dev Screen', () => {
    Alert.alert('Showing secret dev screen!');
});
```

### reload()

```ts
static reload(reason?: string): void;
```

Перезагрузить приложение. Может вызываться напрямую или при взаимодействии с пользователем.

**Пример:**

```ts
<Button
    title="Reload"
    onPress={() => DevSettings.reload()}
/>
```
