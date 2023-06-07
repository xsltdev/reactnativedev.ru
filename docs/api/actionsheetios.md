# ActionSheetIOS

Отображает родной для iOS компонент [Action Sheet](https://developer.apple.com/design/human-interface-guidelines/ios/views/action-sheets/).

## Пример

<div data-snack-id="@bndby/actionsheetios" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Methods

### `showActionSheetWithOptions()`

```tsx
static showActionSheetWithOptions: (
  options: ActionSheetIOSOptions,
  callback: (buttonIndex: number) => void,
);
```

Отображение листа действий iOS. Объект `options` должен содержать одно или несколько из:

-   `options` (массив строк) - список названий кнопок (обязательно)
-   `cancelButtonIndex` (int) - индекс кнопки отмены в `options`
-   `cancelButtonTintColor` (string) - [цвет](цвета), используемый для изменения цвета текста кнопки отмены
-   `destructiveButtonIndex` (int или массив ints) - индексы деструктивных кнопок в `options`
-   `title` (строка) - заголовок для отображения над листом действий
-   `message` (строка) - сообщение для отображения под заголовком
-   `anchor` (число) - узел, к которому должен быть привязан лист действий (используется для iPad)
-   `tintColor` (строка) - [цвет](цвета), используемый для неразрушающих заголовков кнопок
-   `disabledButtonIndices` (массив чисел) - список индексов кнопок, которые должны быть отключены
-   `userInterfaceStyle` (строка) - стиль интерфейса, используемый для листа действий, может быть установлен на `light` или `dark`, иначе будет использоваться системный стиль по умолчанию

Функция `'callback'` принимает один параметр - индекс выбранного элемента, основанный на нуле.

Минимальный пример:

```tsx
ActionSheetIOS.showActionSheetWithOptions(
    {
        options: ['Cancel', 'Remove'],
        destructiveButtonIndex: 1,
        cancelButtonIndex: 0,
    },
    (buttonIndex) => {
        if (buttonIndex === 1) {
            /* destructive action */
        }
    }
);
```

### `dismissActionSheet()`

```tsx
static dismissActionSheet();
```

Удаляет самый верхний представленный лист действий iOS, если лист действий не представлен, отображается предупреждение.

### `showShareActionSheetWithOptions()`

```tsx
static showShareActionSheetWithOptions: (
  options: ShareActionSheetIOSOptions,
  failureCallback: (error: Error) => void,
  successCallback: (success: boolean, method: string) => void,
);
```

Отображение листа акций iOS. Объект `options` должен содержать одно или оба из `message` и `url` и может дополнительно содержать `subject` или `excludedActivityTypes`:

-   `url` (строка) - URL-адрес, которым нужно поделиться
-   `message` (строка) - сообщение, которым нужно поделиться
-   `subject` (строка) - тема сообщения
-   `excludedActivityTypes` (массив) - действия, которые необходимо исключить из `ActionSheet`

!!!warning "Примечание"

    Если `url` указывает на локальный файл или является base64-кодированным uri, файл, на который он указывает, будет загружен и передан напрямую. Таким образом, вы можете обмениваться изображениями, видео, PDF-файлами и т.д. Если `url` указывает на удаленный файл или адрес, он должен соответствовать формату URL, описанному в [RFC 2396](https://www.ietf.org/rfc/rfc2396.txt). Например, веб-адрес URL без соответствующего протокола (HTTP/HTTPS) не будет передан.

Функция `'failureCallback'` принимает один параметр - объект ошибки. Единственным свойством, определенным для этого объекта, является необязательное свойство `stack` типа `string`.

Функция `'successCallback'` принимает два параметра:

-   булево значение, означающее успех или неудачу
-   строка, которая в случае успеха указывает на метод совместного использования
