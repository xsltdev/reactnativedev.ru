# Modal

Компонент **`Modal`** - это базовый способ представления содержимого поверх объемного представления.

## Пример

<div data-snack-id="@bndby/modal" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Реквизиты

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `animated`

!!!danger "Устарело"

    Вместо этого используйте реквизит [`animationType`](modal.md#animationtype).

### `animationType`

Свойство `animationType` управляет анимацией модала.

Возможные значения:

-   `slide` скользит снизу,
-   `fade` исчезает в поле зрения,
-   `none` появляется без анимации.

| Type                                | Default |
| ----------------------------------- | ------- |
| enum(`'none'`, `'slide'`, `'fade'`) | `none`  |

### `hardwareAccelerated` :simple-android:

Реквизит `hardwareAccelerated` управляет тем, нужно ли принудительное аппаратное ускорение для базового окна.

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `onDismiss` :simple-ios:

Реквизит `onDismiss` позволяет передать функцию, которая будет вызвана после того, как модальное окно будет закрыто.

| Type     |
| -------- |
| function |

### `onOrientationChange` :simple-ios:

Обратный вызов `onOrientationChange` вызывается при изменении ориентации во время отображения модала. Предоставляемая ориентация - только "портретная" или "альбомная". Этот обратный вызов также вызывается при первоначальном рендеринге, независимо от текущей ориентации.

| Type     |
| -------- |
| function |

### `onRequestClose`

Обратный вызов `onRequestClose` вызывается, когда пользователь нажимает аппаратную кнопку "Назад" на Android или кнопку меню на Apple TV. Из-за этого необходимого реквизита имейте в виду, что события `BackHandler` не будут испускаться до тех пор, пока модал открыт.

На iOS этот обратный вызов вызывается, когда модальное окно закрывается с помощью жеста перетаскивания, когда `presentationStyle` имеет значение `pageSheet или formSheet`.

| Type                                                                  |
| --------------------------------------------------------------------- |
| function (Required) :simple-android: :tv: <hr />function :simple-ios: |

### `onShow`

Реквизит `onShow` позволяет передать функцию, которая будет вызвана после показа модала.

| Type     |
| -------- |
| function |

### `presentationStyle` :simple-ios:

Реквизит `presentationStyle` управляет тем, как отображается модальное окно (обычно на больших устройствах, таких как iPad или iPhone размера plus size). [Подробнее](https://developer.apple.com/reference/uikit/uimodalpresentationstyle).

Возможные значения:

-   `fullScreen` охватывает экран полностью
-   `pageSheet` охватывает вид по центру портретной ширины (только на больших устройствах)
-   `formSheet` охватывает узкий вид по центру (только на больших устройствах)
-   `overFullScreen` полностью закрывает экран, но допускает прозрачность

| Type                                                                   | Default                                                                             |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| enum(`'fullScreen'`, `'pageSheet'`, `'formSheet'`, `'overFullScreen'`) | `fullScreen` if `transparent={false}`<hr />`overFullScreen` if `transparent={true}` |

### `statusBarTranslucent` :simple-android:

Свойство `statusBarTranslucent` определяет, должен ли ваш модал отображаться под панелью состояния системы.

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `supportedOrientations` :simple-ios:

Свойство `supportedOrientations` позволяет поворачивать модальное окно в любую из указанных ориентаций. На iOS модальное окно по-прежнему ограничено тем, что указано в поле UISupportedInterfaceOrientations в Info.plist вашего приложения.

!!!warning ""

    При использовании `presentationStyle` в `pageSheet` или `formSheet` это свойство будет игнорироваться iOS.

| Type                                                                                                           | Default        |
| -------------------------------------------------------------------------------------------------------------- | -------------- |
| array of enums(`'portrait'`, `'portrait-upside-down'`, `'landscape'`, `'landscape-left'`, `'landscape-right'`) | `['portrait']` |

### `transparent`

Свойство `transparent` определяет, будет ли ваш модал заполнять весь вид. Если установить значение `true`, модальное окно будет отображаться на прозрачном фоне.

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `visible`

Свойство `visible` определяет, является ли ваш модал видимым.

| Type | Default |
| ---- | ------- |
| bool | `true`  |
