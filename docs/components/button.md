# Button

Базовый компонент кнопки, который должен хорошо отображаться на любой платформе. Поддерживает минимальный уровень кастомизации.

Если эта кнопка не подходит для вашего приложения, вы можете создать свою собственную кнопку, используя [Pressable](pressable.md). Для вдохновения посмотрите [исходный код компонента Button](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/Button.js).

```tsx
<Button
    onPress={onPressLearnMore}
    title="Learn More"
    color="#841584"
    accessibilityLabel="Learn more about this purple button"
/>
```

## Пример

<div data-snack-id="@bndby/button-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Реквизиты

### `onPress` (обязательный)

Обработчик, вызываемый при нажатии пользователем кнопки.

| Type                                         |
| -------------------------------------------- |
| ({nativeEvent: [PressEvent](pressevent.md)}) |

### `title` (обязательный)

Текст для отображения внутри кнопки. На Android заданный заголовок будет преобразован в заглавную форму.

| Type   |
| ------ |
| string |

### `accessibilityLabel`

Текст для отображения в целях обеспечения доступности для слепых.

| Type   |
| ------ |
| string |

### `accessibilityLanguage` :simple-ios:

Значение, указывающее, какой язык должен использоваться программой чтения с экрана при взаимодействии пользователя с элементом. Оно должно соответствовать [спецификации BCP 47](https://www.rfc-editor.org/info/bcp47).

Дополнительную информацию см. в [iOS `accessibilityLanguage` doc](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage).

| Type   |
| ------ |
| string |

### `accessibilityActions`

Действия доступности позволяют ассистивной технологии программно вызывать действия компонента. Свойство `accessibilityActions` должно содержать список объектов действий. Каждый объект действия должен содержать имя поля и метку.

Дополнительную информацию см. в [Руководстве по доступности](accessibility.md#accessibility-actions).

| Type  | Required |
| ----- | -------- |
| array | No       |

### `onAccessibilityAction`

Вызывается, когда пользователь выполняет действия по обеспечению доступности. Единственным аргументом этой функции является событие, содержащее имя выполняемого действия.

Дополнительную информацию см. в [Руководстве по доступности](accessibility.md#accessibility-actions).

| Type     | Required |
| -------- | -------- |
| function | No       |

### `color`

Цвет текста (iOS) или цвет фона кнопки (Android).

| Type               | Default                                                    |
| ------------------ | ---------------------------------------------------------- |
| [color](colors.md) | `'#2196F3'` :simple-android:<hr/> `'#007AFF'` :simple-ios: |

### `disabled`

Если `true`, отключите все взаимодействия для этого компонента.

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `hasTVPreferredFocus` :tv:

Предпочтение отдается телевидению.

| Type | Default |
| ---- | ------- |
| bool | `false` |

### `nextFocusDown` :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус, когда пользователь перемещается вниз. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusDown).

| Type   |
| ------ |
| number |

### `nextFocusForward` :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус, когда пользователь перемещается вперед. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusForward).

| Type   |
| ------ |
| number |

### `nextFocusLeft` :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус, когда пользователь перемещается влево. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusLeft).

| Type   |
| ------ |
| number |

### `nextFocusRight` :simple-android: :tv:

Назначает следующее представление для получения фокуса, когда пользователь перемещается вправо. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusRight).

| Type   |
| ------ |
| number |

### `nextFocusUp` :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус при навигации пользователя вверх. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusUp).

| Type   |
| ------ |
| number |

### `testID`

Используется для размещения этого представления в сквозных тестах.

| Type   |
| ------ |
| string |

### `touchSoundDisabled` :simple-android:

Если `true`, не воспроизводит системный звук при прикосновении.

| Type    | Default |
| ------- | ------- |
| boolean | `false` |
