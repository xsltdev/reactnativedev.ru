# TextInput

Основополагающий компонент для ввода текста в приложение с помощью клавиатуры. пропсы обеспечивают возможность настройки нескольких функций, таких как автокоррекция, автокапитализация, текст-заполнитель и различные типы клавиатуры, например цифровая клавиатура.

Самый простой вариант использования — поместить `TextInput` и подписаться на события `onChangeText` для чтения пользовательского ввода. Существуют и другие события, такие как `onSubmitEditing` и `onFocus`, на которые можно подписаться. Минимальный пример:

<div data-snack-id="@bndby/textinput-example-1" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Два метода, открытые через родной элемент, это .focus() и .blur(), которые программно фокусируют или размывают TextInput.

Обратите внимание, что некоторые пропсы доступны только при `multiline={true/false}`. Кроме того, стили границ, которые применяются только к одной стороне элемента (например, `borderBottomColor`, `borderLeftWidth` и т.д.) не будут применяться, если `multiline=true`. Чтобы достичь того же эффекта, вы можете обернуть ваш `TextInput` в `View`:

<div data-snack-id="@bndby/textinput-example-2" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

По умолчанию `TextInput` имеет границу в нижней части своего представления. Эта граница имеет отступ, заданный фоновым изображением, предоставляемым системой, и его нельзя изменить. Чтобы избежать этого, можно либо не задавать высоту явно, в этом случае система позаботится об отображении границы в правильном положении, либо не отображать границу, установив `underlineColorAndroid` в прозрачный.

Обратите внимание, что на Android выделение текста во вводе может изменить параметр активности приложения `windowSoftInputMode` на `adjustResize`. Это может вызвать проблемы с компонентами, имеющими position: 'absolute' при активной клавиатуре. Чтобы избежать такого поведения, либо укажите `windowSoftInputMode` в [`AndroidManifest.xml`](https://developer.android.com/guide/topics/manifest/activity-element.html), либо управляйте этим параметром программно с помощью нативного кода.

## пропсы

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### `allowFontScaling`

Определяет, должны ли шрифты масштабироваться с учетом настроек доступности Text Size. По умолчанию используется значение `true`.

| Type |
| ---- |
| bool |

### `autoCapitalize`

Указывает `TextInput` автоматически набирать определенные символы заглавными буквами. Это свойство не поддерживается некоторыми типами клавиатур, такими как `name-phone-pad`.

-   `characters`: все символы.
-   `words`: первая буква каждого слова.
-   `sentences`: первая буква каждого предложения (_по умолчанию_).
-   `none`: ничего автоматически не капитализировать.

| Type                                             |
| ------------------------------------------------ |
| enum('none', 'sentences', 'words', 'characters') |

### `autoComplete`

Определяет подсказки автозаполнения для системы, чтобы она могла обеспечить автозаполнение. На Android система всегда будет пытаться предложить автозаполнение, используя эвристику для определения типа содержимого. Чтобы отключить автозаполнение, установите `autoComplete` в `off`.

Следующие значения работают на разных платформах:

-   `additional-name`
-   `address-line1`
-   `address-line2`
-   `cc-number`
-   `country`
-   `current-password`
-   `email`
-   `family-name`
-   `given-name`
-   `honorific-prefix`
-   `honorific-suffix`
-   `name`
-   `new-password`
-   `off`
-   `one-time-code`
-   `postal-code`
-   `street-address`
-   `tel`
-   `username`

:simple-ios:

Следующие значения работают только на iOS:

-   `nickname`
-   `organization`
-   `organization-title`
-   `url`

:simple-android:

Следующие значения работают только на Android:

-   `birthdate-day`
-   `birthdate-full`
-   `birthdate-month`
-   `birthdate-year`
-   `cc-csc`
-   `cc-exp`
-   `cc-exp-day`
-   `cc-exp-month`
-   `cc-exp-year`
-   `gender`
-   `name-family`
-   `name-given`
-   `name-middle`
-   `name-middle-initial`
-   `name-prefix`
-   `name-suffix`
-   `password`
-   `password-new`
-   `postal-address`
-   `postal-address-country`
-   `postal-address-extended`
-   `postal-address-extended-postal-code`
-   `postal-address-locality`
-   `postal-address-region`
-   `sms-otp`
-   `tel-country-code`
-   `tel-national`
-   `tel-device`
-   `username-new`

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('additional-name', 'address-line1', 'address-line2', 'birthdate-day', 'birthdate-full', 'birthdate-month', 'birthdate-year', 'cc-csc', 'cc-exp', 'cc-exp-day', 'cc-exp-month', 'cc-exp-year', 'cc-number', 'country', 'current-password', 'email', 'family-name', 'gender', 'given-name', 'honorific-prefix', 'honorific-suffix', 'name', 'name-family', 'name-given', 'name-middle', 'name-middle-initial', 'name-prefix', 'name-suffix', 'new-password', 'nickname', 'one-time-code', 'organization', 'organization-title', 'password', 'password-new', 'postal-address', 'postal-address-country', 'postal-address-extended', 'postal-address-extended-postal-code', 'postal-address-locality', 'postal-address-region', 'postal-code', 'street-address', 'sms-otp', 'tel', 'tel-country-code', 'tel-national', 'tel-device', 'url', 'username', 'username-new', 'off') |

### `autoCorrect`

Если `false`, отключает автокоррекцию. По умолчанию используется значение `true`.

| Type |
| ---- |
| bool |

### `autoFocus`

Если `true`, фокусирует ввод на `componentDidMount` или `useEffect`. Значение по умолчанию — `false`.

| Type |
| ---- |
| bool |

### `blurOnSubmit`

Если `true`, текстовое поле будет размываться при отправке. Значение по умолчанию равно true для однострочных полей и false для многострочных. Обратите внимание, что для многострочных полей установка `blurOnSubmit` в `true` означает, что нажатие клавиши return размывает поле и вызывает событие `onSubmitEditing` вместо вставки новой строки в поле.

| Type |
| ---- |
| bool |

### `caretHidden`

Если `true`, каретка будет скрыта. Значение по умолчанию — `false`.

| Type |
| ---- |
| bool |

### `clearButtonMode` :simple-ios:

Когда кнопка очистки должна появиться в правой части текстового представления. Это свойство поддерживается только для однострочного компонента TextInput. Значение по умолчанию — `никогда`.

| Type                                                       |
| ---------------------------------------------------------- |
| enum('never', 'while-editing', 'unless-editing', 'always') |

### `clearTextOnFocus` :simple-ios:

Если `true`, то текстовое поле автоматически очищается, когда начинается редактирование.

| Type |
| ---- |
| bool |

### `contextMenuHidden`

Если `true`, контекстное меню будет скрыто. Значение по умолчанию — `false`.

| Type |
| ---- |
| bool |

### `dataDetectorTypes` :simple-ios:

Определяет типы данных, преобразуемых в кликабельные URL в текстовом вводе. Действует, только если `multiline={true}` и `editable={false}`. По умолчанию никакие типы данных не определяются.

Вы можете предоставить один тип или массив из множества типов.

Возможными значениями для `dataDetectorTypes` являются:

-   `'phoneNumber'`
-   `'link'`
-   `'address'`
-   `'calendarEvent'`
-   `'none'`
-   `'all'`

| Type                                                                                                                                                     |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('phoneNumber', 'link', 'address', 'calendarEvent', 'none', 'all'), ,array of enum('phoneNumber', 'link', 'address', 'calendarEvent', 'none', 'all') |

### `defaultValue`

Предоставляет начальное значение, которое будет меняться, когда пользователь начнет вводить текст. Полезно для тех случаев, когда вы не хотите заниматься прослушиванием событий и обновлением значения prop для синхронизации управляемого состояния.

| Type   |
| ------ |
| string |

### `cursorColor` :simple-android:

При указании этого параметра устанавливается цвет курсора (или "каретки") в компоненте. В отличие от поведения `selectionColor`, цвет курсора будет установлен независимо от цвета поля выбора текста.

| Type              |
| ----------------- |
| [color]colors.md) |

### `disableFullscreenUI` :simple-android:

При `false`, если вокруг текстового ввода имеется небольшое пространство (например, при альбомной ориентации телефона), ОС может выбрать режим редактирования текста внутри полноэкранного текстового ввода. Когда `true`, эта функция отключена, и пользователи всегда будут редактировать текст непосредственно внутри текстового ввода. По умолчанию установлено значение `false`.

| Type |
| ---- |
| bool |

### `editable`

Если `false`, текст не редактируется. По умолчанию используется значение `true`.

| Type |
| ---- |
| bool |

### `enablesReturnKeyAutomatically` :simple-ios:

Если `true`, клавиатура отключает клавишу возврата при отсутствии текста и автоматически включает ее при появлении текста. Значение по умолчанию — `false`.

| Type |
| ---- |
| bool |

### `enterKeyHint`

Определяет, какой текст должен быть показан для возвращаемого ключа. Имеет приоритет перед пропсом `returnKeyType`.

Следующие значения работают на разных платформах:

-   `enter`
-   `done`
-   `next`
-   `search`
-   `send`

Только для Android

Следующие значения работают только на Android:

-   `previous`

| Type                                                        |
| ----------------------------------------------------------- |
| enum('enter', 'done', 'next', 'previous', 'search', 'send') |

### `importantForAutofill` :simple-android:

Указывает операционной системе, следует ли включать отдельные поля вашего приложения в структуру представления для целей автозаполнения на Android API Level 26+. Возможные значения: `auto`, `no`, `noExcludeDescendants`, `yes` и `yesExcludeDescendants`. По умолчанию используется значение `auto`.

-   `auto`: Позволяет системе Android использовать свою эвристику, чтобы определить, важно ли представление для автозаполнения.
-   `no`: Это представление не важно для автозаполнения.
-   `noExcludeDescendants`: Это представление и его дочерние элементы не важны для автозаполнения.
-   `yes`: Это представление важно для автозаполнения.
-   `yesExcludeDescendants`: Это представление важно для автозаполнения, но его дочерние элементы не важны для автозаполнения.

| Type                                                                       |
| -------------------------------------------------------------------------- |
| enum('auto', 'no', 'noExcludeDescendants', 'yes', 'yesExcludeDescendants') |

### `inlineImageLeft` :simple-android:

Если определено, то предоставленный ресурс изображения будет отображаться слева. Ресурс изображения должен находиться внутри `/android/app/src/main/res/drawable` и ссылаться как

```js
<TextInput inlineImageLeft="search_icon" />
```

| Type   |
| ------ |
| string |

### `inlineImagePadding` :simple-android:

Расстояние между встроенным изображением, если оно есть, и самим вводимым текстом.

| Type   |
| ------ |
| number |

### `inputAccessoryViewID` :simple-ios:

Необязательный идентификатор, который связывает пользовательский [InputAccessoryView](inputaccessoryview.md) с этим текстовым вводом. `InputAccessoryView` отображается над клавиатурой, когда этот текстовый ввод сфокусирован.

| Type   |
| ------ |
| string |

### `inputMode`

Работает как атрибут `inputmode` в HTML, определяет, какую клавиатуру открыть, например `numeric`, и имеет приоритет перед `keyboardType`.

Поддерживает следующие значения:

-   `none`
-   `text`
-   `decimal`
-   `numeric`
-   `tel`
-   `search`
-   `email`
-   `url`

| Type                                                                        |
| --------------------------------------------------------------------------- |
| enum('decimal', 'email', 'none', 'numeric', 'search', 'tel', 'text', 'url') |

### `keyboardAppearance` :simple-ios:

Определяет цвет клавиатуры.

| Type                             |
| -------------------------------- |
| enum('default', 'light', 'dark') |

### `keyboardType`

Определяет, какую клавиатуру открывать, например, `numeric`.

Смотрите скриншоты всех типов [здесь](http://lefkowitz.me/2018/04/30/visual-guide-to-react-native-textinput-keyboardtype-options/).

Следующие значения работают на разных платформах:

-   `default`
-   `number-pad`
-   `decimal-pad`
-   `numeric`
-   `email-address`
-   `phone-pad`
-   `url`

Только для iOS

Следующие значения работают только на iOS:

-   `ascii-capable`
-   `numbers-and-punctuation`
-   `name-phone-pad`
-   `twitter`
-   `web-search`

Только для Android

Следующие значения работают только на Android:

-   `visible-password`

| Type                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('default', 'email-address', 'numeric', 'phone-pad', 'ascii-capable', 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter', 'web-search', 'visible-password') |

### `maxFontSizeMultiplier`

Указывает наибольший возможный масштаб, которого может достичь шрифт, если включено `allowFontScaling`. Возможные значения:

| Type   |
| ------ |
| number |

### `maxLength`

Ограничивает максимальное количество символов, которые можно ввести. Используйте это вместо реализации логики в JS, чтобы избежать мерцания.

| Type   |
| ------ |
| number |

### `multiline`

Если `true`, текстовый ввод может состоять из нескольких строк. Значение по умолчанию — `false`.

!!!note ""

    Важно отметить, что это выравнивает текст по верху на iOS, и центрирует его на Android. Используйте с `textAlignVertical`, установленным на `top`, для одинакового поведения на обеих платформах.

| Type |
| ---- |
| bool |

### `numberOfLines` :simple-android:

Устанавливает количество строк для `TextInput`. Используйте его с параметром `multiline`, установленным в `true`, чтобы иметь возможность заполнять строки.

| Type   |
| ------ |
| number |

### `onBlur`

Обратный вызов, который вызывается при размытии вводимого текста.

!!!warning ""

    Примечание: Если вы пытаетесь получить доступ к значению `text` из `nativeEvent`, имейте в виду, что полученное значение может быть `undefined`, что может привести к непреднамеренным ошибкам. Если вы пытаетесь найти последнее значение `TextInput`, вы можете использовать событие [`onEndEditing`](textinput.md#onendediting), которое срабатывает по завершении редактирования.

| Type     |
| -------- |
| function |

### `onChange`

Обратный вызов, который вызывается при изменении текста текстового ввода.

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {eventCount, target, text}}`) => void |

### `onChangeText`

Обратный вызов, который вызывается при изменении текста текстового ввода. Измененный текст передается как единственный строковый аргумент обработчику обратного вызова.

| Type     |
| -------- |
| function |

### `onContentSizeChange`

Обратный вызов, который вызывается при изменении размера содержимого текстового ввода.

Вызывается только для многострочных текстовых вводов.

| Type                                                       |
| ---------------------------------------------------------- |
| (`{nativeEvent: {contentSize: {width, height} }}`) => void |

### `onEndEditing`

Обратный вызов, который вызывается по окончании ввода текста.

| Type     |
| -------- |
| function |

### `onPressIn`

Обратный вызов, который вызывается при задействовании касания.

| Type                                                 |
| ---------------------------------------------------- |
| ({nativeEvent: [PressEvent](pressevent.md)}) => void |

### `onPressOut`

Обратный вызов, который вызывается, когда прикосновение отпускается.

| Type                                                 |
| ---------------------------------------------------- |
| ({nativeEvent: [PressEvent](pressevent.md)}) => void |

### `onFocus`

Обратный вызов, который вызывается, когда текстовый ввод сфокусирован.

| Type                                                   |
| ------------------------------------------------------ |
| ({nativeEvent: [LayoutEvent](layoutevent.md)}) => void |

### `onKeyPress`

Обратный вызов, который вызывается при нажатии клавиши. Он будет вызван объектом, где `keyValue` равно `'Enter'` или `'Backspace'` для соответствующих клавиш и вводимому символу в противном случае, включая `''` для пробела. Вызывается перед обратным вызовом `onChange`. Примечание: на Android обрабатываются только вводы с мягкой клавиатуры, а не с аппаратной.

| Type                                        |
| ------------------------------------------- |
| (`{nativeEvent: {key: keyValue} }`) => void |

### `onLayout`

Вызывается при монтировании и при смене раскладки.

| Type                                                   |
| ------------------------------------------------------ |
| ({nativeEvent: [LayoutEvent](layoutevent.md)}) => void |

### `onScroll`

Вызывается при прокрутке содержимого. Может также содержать другие свойства из `ScrollEvent`, но в Android `contentSize` не предоставляется по причинам производительности.

| Type                                                |
| --------------------------------------------------- |
| (`{nativeEvent: {contentOffset: {x, y} }}`) => void |

### `onSelectionChange`

Обратный вызов, который вызывается при изменении выбора ввода текста.

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {selection: {start, end} }}`) => void |

### `onSubmitEditing`

Обратный вызов, который вызывается при нажатии кнопки отправки текстового ввода.

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {text, eventCount, target}}`) => void |

Обратите внимание, что на iOS этот метод не вызывается при использовании `keyboardType="phone-pad"`.

### `placeholder`

Строка, которая будет выведена перед вводом текста.

| Type   |
| ------ |
| string |

### `placeholderTextColor`

Цвет текста строки-заполнителя.

| Type              |
| ----------------- |
| [color]colors.md) |

### `readOnly`

Если `true`, текст не редактируется. Значение по умолчанию — `false`.

| Type |
| ---- |
| bool |

### `returnKeyLabel` :simple-android:

Устанавливает ключ возврата для метки. Используйте его вместо `returnKeyType`.

| Type   |
| ------ |
| string |

### `returnKeyType`

Определяет, как должен выглядеть ключ возврата. В Android вы также можете использовать `returnKeyLabel`.

_Кросс-платформа_

Следующие значения работают на разных платформах:

-   `done`
-   `go`
-   `next`
-   `search`
-   `send`

Только для Android

Следующие значения работают только на Android:

-   `none`
-   `previous`

Только для iOS

Следующие значения работают только на iOS:

-   `default`
-   `emergency-call`
-   `google`
-   `join`
-   `route`
-   `yahoo`

| Type                                                                                                                              |
| --------------------------------------------------------------------------------------------------------------------------------- |
| enum('done', 'go', 'next', 'search', 'send', 'none', 'previous', 'default', 'emergency-call', 'google', 'join', 'route', 'yahoo') |

### `rejectResponderTermination` :simple-ios:

Если `true`, позволяет `TextInput` передавать события касания родительскому компоненту. Это позволяет таким компонентам, как `SwipeableListView`, быть пролистываемыми из `TextInput` на iOS, как это происходит по умолчанию на Android. Если `false`, `TextInput` всегда просит обработать ввод (кроме случаев, когда он отключен). Значение по умолчанию — `true`.

| Type |
| ---- |
| bool |

### `rows` :simple-android:

Устанавливает количество строк для `TextInput`. Используйте его с параметром `multiline`, установленным в `true`, чтобы иметь возможность заполнять строки.

| Type   |
| ------ |
| number |

### `scrollEnabled` :simple-ios:

| Type |
| ---- |
| bool |

### `secureTextEntry`

Если `true`, текстовый ввод скрывает вводимый текст, что позволяет сохранить конфиденциальный текст, например, пароли. Значение по умолчанию — `false`. Не работает с `multiline={true}`.

| Type |
| ---- |
| bool |

### `selection`

Начало и конец выделения вводимого текста. Установите начало и конец в одно и то же значение для позиционирования курсора.

| Type                                |
| ----------------------------------- |
| object: {start: number,end: number} |

### `selectionColor`

Цвет выделения и курсора при вводе текста.

| Type              |
| ----------------- |
| [color]colors.md) |

### `selectTextOnFocus`

Если `true`, весь текст будет автоматически выделяться при фокусировке.

| Type |
| ---- |
| bool |

### `showSoftInputOnFocus`

Если значение `false`, оно предотвращает отображение мягкой клавиатуры, когда поле сфокусировано. Значение по умолчанию — `true`.

| Type |
| ---- |
| bool |

### `spellCheck` :simple-ios:

Если `false`, отключает стиль проверки орфографии (т.е. красные подчеркивания). Значение по умолчанию наследуется от `autoCorrect`.

| Type |
| ---- |
| bool |

### `textAlign`

Выравнивание вводимого текста по левой, центральной или правой стороне поля ввода.

Возможными значениями для `textAlign` являются:

-   `left`
-   `center`
-   `right`

| Type                            |
| ------------------------------- |
| enum('left', 'center', 'right') |

### `textContentType` :simple-ios:

Предоставить клавиатуре и системе информацию об ожидаемом семантическом значении контента, который вводят пользователи.

!!!note ""

    `autoComplete`, обеспечивает ту же функциональность и доступен для всех платформ. Вы можете использовать [`Platform.select`]platform.md#select) для различных вариантов поведения платформы.

    Избегайте использования одновременно `textContentType` и `autoComplete`. Для обратной совместимости, `textContentType` имеет приоритет, когда оба свойства установлены.

Для iOS 11+ вы можете установить `textContentType` в `username` или `password`, чтобы включить автозаполнение регистрационных данных из связки ключей устройства.

Для iOS 12+ `newPassword` может использоваться для указания ввода нового пароля, который пользователь может захотеть сохранить в связке ключей, а `oneTimeCode` может использоваться для указания того, что поле может быть автоматически заполнено кодом, пришедшим в SMS.

Чтобы отключить автозаполнение, установите `textContentType` в `none`.

Возможными значениями для `textContentType` являются:

-   `none`
-   `URL`
-   `addressCity`
-   `addressCityAndState`
-   `addressState`
-   `countryName`
-   `creditCardNumber`
-   `emailAddress`
-   `familyName`
-   `fullStreetAddress`
-   `givenName`
-   `jobTitle`
-   `location`
-   `middleName`
-   `name`
-   `namePrefix`
-   `nameSuffix`
-   `nickname`
-   `organizationName`
-   `postalCode`
-   `streetAddressLine1`
-   `streetAddressLine2`
-   `sublocality`
-   `telephoneNumber`
-   `username`
-   `password`
-   `newPassword`
-   `oneTimeCode`

| Type                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('none', 'URL', 'addressCity', 'addressCityAndState', 'addressState', 'countryName', 'creditCardNumber', 'emailAddress', 'familyName', 'fullStreetAddress', 'givenName', 'jobTitle', 'location', 'middleName', 'name', 'namePrefix', 'nameSuffix', 'nickname', 'organizationName', 'postalCode', 'streetAddressLine1', 'streetAddressLine2', 'sublocality', 'telephoneNumber', 'username', 'password') |

### `passwordRules` :simple-ios:

При использовании `textContentType` в качестве `newPassword` на iOS мы можем сообщить ОС минимальные требования к паролю, чтобы она могла сгенерировать тот, который будет им соответствовать. Чтобы создать допустимую строку для `PasswordRules`, загляните в [Apple Docs](https://developer.apple.com/password-rules/).

!!!warning ""

    Если диалог генерации паролей не появляется, убедитесь, что:

    -   Автозаполнение включено: **Настройки** → **Пароли и учетные записи** → установите флажок "Вкл" на **Автозаполнение паролей**,
    -   Используется связка ключей iCloud: **Настройки** → **Apple ID** → **iCloud** → **Keychain** → переключить "Вкл" **iCloud Keychain**.

| Type   |
| ------ |
| string |

### `style`

Обратите внимание, что поддерживаются не все стили текста, неполный список того, что не поддерживается, включает:

-   `borderLeftWidth`
-   `borderTopWidth`
-   `borderRightWidth`
-   `borderBottomWidth`
-   `borderTopLeftRadius`
-   `borderTopRightRadius`
-   `borderBottomRightRadius`
-   `borderBottomLeftRadius`

более подробно см. в [Issue#7070](https://github.com/facebook/react-native/issues/7070).

| Type                  |
| --------------------- |
| [Text](text.md#style) |

### `textBreakStrategy` :simple-android:

Установка стратегии разбиения текста на Android API Level 23+, возможные значения `simple`, `highQuality`, `balanced` Значение по умолчанию `simple`.

| Type                                      |
| ----------------------------------------- |
| enum('simple', 'highQuality', 'balanced') |

### `underlineColorAndroid` :simple-android:

Цвет подчеркивания `TextInput`.

| Type              |
| ----------------- |
| [color]colors.md) |

### `value`

Значение, которое будет отображаться для ввода текста. `TextInput` является контролируемым компонентом, что означает, что собственное значение будет принудительно соответствовать этому значению prop, если оно предоставлено. Для большинства случаев это работает отлично, но в некоторых случаях это может вызвать мерцание — одной из распространенных причин является предотвращение редактирования путем сохранения одинакового значения. В дополнение к установке одинакового значения, либо установите `editable={false}`, либо установите/обновите `maxLength`, чтобы предотвратить нежелательное редактирование без мерцания.

| Type   |
| ------ |
| string |

### `lineBreakStrategyIOS` :simple-ios:

Установка стратегии перевода строки на iOS 14+. Возможные значения: `none`, `standard`, `hangul-word` и `push-out`.

| Type                                                        | Default  |
| ----------------------------------------------------------- | -------- |
| enum(`'none'`, `'standard'`, `'hangul-word'`, `'push-out'`) | `'none'` |

## Методы

### `.focus()`

```ts
focus();
```

Делает запрос на ввод исходного текста фокусным.

### `.blur()`

```ts
blur();
```

Заставляет пользовательский ввод потерять фокус.

### `clear()`

```ts
clear();
```

Удаляет весь текст из `TextInput`.

### `isFocused()`

```ts
isFocused(): boolean;
```

Возвращает `true`, если вход в данный момент сфокусирован; `false` в противном случае.

## Известные проблемы

-   [react-native#19096](https://github.com/facebook/react-native/issues/19096): Не поддерживает `onKeyPreIme` в Android.
-   [react-native#19366](https://github.com/facebook/react-native/issues/19366): Вызов `.focus()` после закрытия клавиатуры Android с помощью кнопки "Назад" не вызывает клавиатуру снова.
-   [react-native#26799](https://github.com/facebook/react-native/issues/26799): Не поддерживается `secureTextEntry` в Android, когда `keyboardType="email-address` или `keyboardType="phone-pad"`.
