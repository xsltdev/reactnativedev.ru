# DevSettings

Модуль `DevSettings` раскрывает методы для настройки параметров для разработчиков в процессе разработки.

## Методы

### `addMenuItem()`

```tsx
static addMenuItem(title: string, handler: () => any);
```

Добавьте пользовательский пункт меню в меню Dev.

**Параметры:**

| Name                  | Type     |
| --------------------- | -------- |
| title (обязательно)   | string   |
| handler (обязательно) | function |

**Пример:**

```tsx
DevSettings.addMenuItem('Show Secret Dev Screen', () => {
    Alert.alert('Showing secret dev screen!');
});
```

### `reload()`

```tsx
static reload(reason?: string): void;
```

Перезагрузить приложение. Может вызываться напрямую или при взаимодействии с пользователем.

**Пример:**

```tsx
<Button
    title="Reload"
    onPress={() => DevSettings.reload()}
/>
```
