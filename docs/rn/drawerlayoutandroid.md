---
description: DrawerLayoutAndroid - компонент React, который оборачивает платформу DrawerLayout (только для Android)
---

# DrawerLayoutAndroid

**`DrawerLayoutAndroid`** - компонент React, который оборачивает платформу `DrawerLayout` (только для Android). `Drawer` (обычно используемый для навигации) рендерится с помощью `renderNavigationView`, а прямыми дочерними элементами являются основные представления (куда попадает ваш контент). Навигационное представление изначально не видно на экране, но может быть притянуто со стороны окна, указанной параметром `drawerPosition`, а его ширина может быть задана параметром `drawerWidth`.

## Пример {#example}

=== "Typescript"

    <div data-snack-id="@bndby/ts-drawerlayoutandroid-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "Javascript"

    <div data-snack-id="@bndby/js-drawerlayoutandroid-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы {#props}

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### drawerBackgroundColor

Определяет цвет фона drawerа. Значение по умолчанию — `white`. Если вы хотите задать непрозрачность drawerа, используйте rgba. Пример:

```ts
return (
    <DrawerLayoutAndroid drawerBackgroundColor="rgba(0,0,0,0.5)" />
);
```

| Тип               | Требуется |
| ----------------- | --------- |
| [color]colors.md) | Нет       |

### drawerLockMode

Определяет режим блокировки выдвижного drawerа. drawer может быть заблокирован в 3 состояниях:

-   `unlocked` (по умолчанию), означает, что drawer будет реагировать (открываться/закрываться) на жесты касания.
-   `locked-closed`, то есть drawer будет оставаться закрытым и не реагировать на жесты.
-   `locked-open` — означает, что drawer будет оставаться открытым и не реагировать на жесты. drawer по-прежнему можно открывать и закрывать программно (`openDrawer`/`closeDrawer`).

| Тип                                                | Требуется |
| -------------------------------------------------- | --------- |
| `enum('unlocked', 'locked-closed', 'locked-open')` | Нет       |

### drawerPosition

Указывает сторону экрана, с которой будет выдвигаться drawer. По умолчанию установлено значение `left`.

| Тип                     | Требуется |
| ----------------------- | --------- |
| `enum('left', 'right')` | Нет       |

### drawerWidth

Определяет ширину drawerа, точнее, ширину вида, который будет выдвигаться от края окна.

| Тип      | Требуется |
| -------- | --------- |
| `number` | Нет       |

### keyboardDismissMode

Определяет, будет ли клавиатура удалена в ответ на перетаскивание.

-   `'none'` (по умолчанию), при перетаскивании клавиатура не отключается.
-   `'on-drag'`, клавиатура отключается, когда начинается перетаскивание.

| Тип                       | Требуется |
| ------------------------- | --------- |
| `enum('none', 'on-drag')` | Нет       |

### onDrawerClose

Функция, вызываемая каждый раз, когда навигационное представление было закрыто.

| Тип        | Требуется |
| ---------- | --------- |
| `function` | Нет       |

### onDrawerOpen

Функция, вызываемая каждый раз, когда открыто представление навигации.

| Тип        | Требуется |
| ---------- | --------- |
| `function` | Нет       |

### onDrawerSlide

Функция вызывается всякий раз, когда происходит взаимодействие с навигационным представлением.

| Тип        | Требуется |
| ---------- | --------- |
| `function` | Нет       |

### onDrawerStateChanged

Функция вызывается при изменении состояния drawerа. drawer может находиться в 3 состояниях:

-   `idle`, означает, что в данный момент не происходит никакого взаимодействия с навигационным представлением
-   `dragging`, то есть в данный момент происходит взаимодействие с видом навигации
-   `settling`, означает, что было взаимодействие с навигационным представлением, и навигационное представление сейчас завершает анимацию закрытия или открытия

| Тип        | Требуется |
| ---------- | --------- |
| `function` | Нет       |

### renderNavigationView

Вид навигации, который будет отображаться сбоку экрана и может быть втянут внутрь.

| Тип        | Требуется |
| ---------- | --------- |
| `function` | Да        |

### statusBarBackgroundColor

Сделайте так, чтобы drawer занимал весь экран и рисовал фон строки состояния, чтобы он открывался поверх строки состояния. Это будет иметь эффект только для API 21+.

| Тип               | Требуется |
| ----------------- | --------- |
| [color]colors.md) | Нет       |

## Методы {#methods}

### closeDrawer()

```ts
closeDrawer();
```

Закрывает drawer.

### openDrawer()

```ts
openDrawer();
```

Открывает drawer.
