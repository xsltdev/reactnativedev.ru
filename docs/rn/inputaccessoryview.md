---
description: InputAccessoryView - компонент, позволяющий настраивать вид аксессуара ввода с клавиатуры на iOS
---

# InputAccessoryView

**`InputAccessoryView`** - компонент, позволяющий настраивать вид аксессуара ввода с клавиатуры на iOS. Представление аксессуара ввода отображается над клавиатурой, когда `TextInput` имеет фокус. Этот компонент можно использовать для создания пользовательских панелей инструментов.

Чтобы использовать этот компонент, оберните свою панель инструментов компонентом InputAccessoryView и задайте `nativeID`. Затем передайте этот `nativeID` в качестве `inputAccessoryViewID` любого желаемого `TextInput`. Основной пример:

<div data-snack-id="@bndby/inputaccessoryview" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Этот компонент также может быть использован для создания "липких" текстовых вводов (текстовых вводов, привязанных к верхней части клавиатуры). Для этого оберните `TextInput` компонентом `InputAccessoryView` и не задавайте `nativeID`. В качестве примера можно посмотреть [InputAccessoryViewExample.js](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/InputAccessoryView/InputAccessoryViewExample.js).

## Пропсы {#props}

### backgroundColor

| Тип               |
| ----------------- |
| [color]colors.md) |

### nativeID

Идентификатор, который используется для привязки данного `InputAccessoryView` к указанному `TextInput`(ам).

| Тип      |
| -------- |
| `string` |

### style

| Тип                               |
| --------------------------------- |
| [View Style](view-style-props.md) |

## Известные проблемы

-   [react-native#18997](https://github.com/facebook/react-native/issues/18997): Не поддерживается многострочный `TextInput`
-   [react-native#20157](https://github.com/facebook/react-native/issues/20157): Невозможно использовать с нижней панелью вкладок
