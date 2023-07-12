# DrawerLayoutAndroid

Компонент React, который оборачивает платформу `DrawerLayout` (только для Android). `Drawer` (обычно используемый для навигации) рендерится с помощью `renderNavigationView`, а прямыми дочерними элементами являются основные представления (куда попадает ваш контент). Навигационное представление изначально не видно на экране, но может быть притянуто со стороны окна, указанной параметром `drawerPosition`, а его ширина может быть задана параметром `drawerWidth`.

## Пример

=== "Typescript"

    <div data-snack-id="@bndby/ts-drawerlayoutandroid-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "Javascript"

    <div data-snack-id="@bndby/js-drawerlayoutandroid-component-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Реквизиты

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `drawerBackgroundColor`

Определяет цвет фона ящика. Значение по умолчанию — `white`. Если вы хотите задать непрозрачность ящика, используйте rgba. Пример:

```ts
return (
    <DrawerLayoutAndroid drawerBackgroundColor="rgba(0,0,0,0.5)" />
);
```

| Type                         | Required |
| ---------------------------- | -------- |
| [color](../guides/colors.md) | No       |

### `drawerLockMode`

Определяет режим блокировки выдвижного ящика. Ящик может быть заблокирован в 3 состояниях:

-   `unlocked` (по умолчанию), означает, что ящик будет реагировать (открываться/закрываться) на жесты касания.
-   `locked-closed`, то есть ящик будет оставаться закрытым и не реагировать на жесты.
-   `locked-open` — означает, что ящик будет оставаться открытым и не реагировать на жесты. Ящик по-прежнему можно открывать и закрывать программно (`openDrawer`/`closeDrawer`).

| Type                                             | Required |
| ------------------------------------------------ | -------- |
| enum('unlocked', 'locked-closed', 'locked-open') | No       |

### `drawerPosition`

Указывает сторону экрана, с которой будет выдвигаться ящик. По умолчанию установлено значение `left`.

| Type                  | Required |
| --------------------- | -------- |
| enum('left', 'right') | No       |

### `drawerWidth`

Определяет ширину ящика, точнее, ширину вида, который будет выдвигаться от края окна.

| Type   | Required |
| ------ | -------- |
| number | No       |

### `keyboardDismissMode`

Определяет, будет ли клавиатура удалена в ответ на перетаскивание.

-   `'none'` (по умолчанию), при перетаскивании клавиатура не отключается.
-   `'on-drag'`, клавиатура отключается, когда начинается перетаскивание.

| Type                    | Required |
| ----------------------- | -------- |
| enum('none', 'on-drag') | No       |

### `onDrawerClose`

Функция, вызываемая каждый раз, когда навигационное представление было закрыто.

| Type     | Required |
| -------- | -------- |
| function | No       |

### `onDrawerOpen`

Функция, вызываемая каждый раз, когда открыто представление навигации.

| Type     | Required |
| -------- | -------- |
| function | No       |

### `onDrawerSlide`

Функция вызывается всякий раз, когда происходит взаимодействие с навигационным представлением.

| Type     | Required |
| -------- | -------- |
| function | No       |

### `onDrawerStateChanged`

Функция вызывается при изменении состояния ящика. Ящик может находиться в 3 состояниях:

-   `idle`, означает, что в данный момент не происходит никакого взаимодействия с навигационным представлением
-   `dragging`, то есть в данный момент происходит взаимодействие с видом навигации
-   `settling`, означает, что было взаимодействие с навигационным представлением, и навигационное представление сейчас завершает анимацию закрытия или открытия

| Type     | Required |
| -------- | -------- |
| function | No       |

### `renderNavigationView`

Вид навигации, который будет отображаться сбоку экрана и может быть втянут внутрь.

| Type     | Required |
| -------- | -------- |
| function | Yes      |

### `statusBarBackgroundColor`

Сделайте так, чтобы ящик занимал весь экран и рисовал фон строки состояния, чтобы он открывался поверх строки состояния. Это будет иметь эффект только для API 21+.

| Type                         | Required |
| ---------------------------- | -------- |
| [color](../guides/colors.md) | No       |

## Методы

### `closeDrawer()`

```ts
closeDrawer();
```

Закрывает ящик.

### `openDrawer()`

```ts
openDrawer();
```

Открывает ящик.
