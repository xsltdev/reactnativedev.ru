---
description: Базовый компонент кнопки Button, который должен хорошо отображаться на любой платформе
---

# Button

Базовый компонент кнопки **`Button`**, который должен хорошо отображаться на любой платформе. Поддерживает минимальный уровень кастомизации.

Если эта кнопка не подходит для вашего приложения, вы можете создать свою собственную кнопку, используя [Pressable](pressable.md). Для вдохновения посмотрите [исходный код компонента Button](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/Button.js).

```ts
<Button
    onPress={onPressLearnMore}
    title="Learn More"
    color="#841584"
    accessibilityLabel="Learn more about this purple button"
/>
```

## Пример {#example}

<div data-snack-id="@bndby/button-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы {#props}

### onPress (обязательный) {#requiredonpress}

Обработчик, вызываемый при нажатии пользователем кнопки.

| Тип                                          |
| -------------------------------------------- |
| ({nativeEvent: [PressEvent](pressevent.md)}) |

### title (обязательный) {#requiredtitle}

Текст для отображения внутри кнопки. На Android заданный заголовок будет преобразован в заглавную форму.

| Тип      |
| -------- |
| `string` |

### accessibilityLabel

Текст для отображения в целях обеспечения доступности для слепых.

| Тип      |
| -------- |
| `string` |

### accessibilityLanguage :simple-ios: {#accessibilitylanguage-ios}

Значение, указывающее, какой язык должен использоваться программой чтения с экрана при взаимодействии пользователя с элементом. Оно должно соответствовать [спецификации BCP 47](https://www.rfc-editor.org/info/bcp47).

Дополнительную информацию см. в [iOS `accessibilityLanguage` doc](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage).

| Тип      |
| -------- |
| `string` |

### accessibilityActions

Действия доступности позволяют ассистивной технологии программно вызывать действия компонента. Свойство `accessibilityActions` должно содержать список объектов действий. Каждый объект действия должен содержать имя поля и метку.

Дополнительную информацию см. в [Руководстве по доступности](accessibility.md#accessibility-actions).

| Тип     | Требуется |
| ------- | --------- |
| `array` | Нет       |

### onAccessibilityAction

Вызывается, когда пользователь выполняет действия по обеспечению доступности. Единственным аргументом этой функции является событие, содержащее имя выполняемого действия.

Дополнительную информацию см. в [Руководстве по доступности](accessibility.md#accessibility-actions).

| Тип        | Требуется |
| ---------- | --------- |
| `function` | Нет       |

### color

Цвет текста (iOS) или цвет фона кнопки (Android).

| Тип                | По-умолчанию                                               |
| ------------------ | ---------------------------------------------------------- |
| [color](colors.md) | `'#2196F3'` :simple-android:<hr/> `'#007AFF'` :simple-ios: |

### disabled

Если `true`, отключите все взаимодействия для этого компонента.

| Тип    | По-умолчанию |
| ------ | ------------ |
| `bool` | `false`      |

### hasTVPreferredFocus :tv:

Предпочтение отдается телевидению.

| Тип    | По-умолчанию |
| ------ | ------------ |
| `bool` | `false`      |

### nextFocusDown :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус, когда пользователь перемещается вниз. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusDown).

| Тип      |
| -------- |
| `number` |

### nextFocusForward :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус, когда пользователь перемещается вперед. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusForward).

| Тип      |
| -------- |
| `number` |

### nextFocusLeft :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус, когда пользователь перемещается влево. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusLeft).

| Тип      |
| -------- |
| `number` |

### nextFocusRight :simple-android: :tv:

Назначает следующее представление для получения фокуса, когда пользователь перемещается вправо. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusRight).

| Тип      |
| -------- |
| `number` |

### nextFocusUp :simple-android: :tv:

Назначает следующее представление, которое будет получать фокус при навигации пользователя вверх. См. [документацию по Android](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusUp).

| Тип      |
| -------- |
| `number` |

### testID

Используется для размещения этого представления в сквозных тестах.

| Тип      |
| -------- |
| `string` |

### touchSoundDisabled :simple-android:

Если `true`, не воспроизводит системный звук при прикосновении.

| Тип       | По-умолчанию |
| --------- | ------------ |
| `boolean` | `false`      |
