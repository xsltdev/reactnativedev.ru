# Text Style Props

## Пример

=== "Typescript"

    <div data-snack-id="@bndby/textstyleprops-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "Javascript"

    <div data-snack-id="@bndby/textstyleprops-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## пропсы

### `color`

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |

### `fontFamily`

| Type   |
| ------ |
| строка |

### `fontSize`

| Тип   |
| ----- |
| число |

### `fontStyle`

| Type                         |
| ---------------------------- |
| enum(`'normal'`, `'italic'`) |

### `fontWeight`

Определяет вес шрифта. Для большинства шрифтов поддерживаются значения `'normal'` и `'bold'`. Не все шрифты имеют варианты для каждого из числовых значений, в этом случае выбирается наиболее близкий.

| Тип                                                                                                                    | По умолчанию |
| ---------------------------------------------------------------------------------------------------------------------- | ------------ |
| enum(`'normal'`, `'bold'`, `'100'`, `'200'`, `'300'`, `'400'`, `'500'`, `'600'`, `'700'`, `'800'`, `'900'`) или number | `'normal'`   |

### `includeFontPadding` :simple-android:

Установите значение `false`, чтобы удалить дополнительную подложку шрифта, предназначенную для освобождения места для некоторых восходящих/нисходящих элементов. В некоторых шрифтах такая подгонка может привести к тому, что текст будет выглядеть немного смещенным при вертикальном центрировании. Для достижения наилучших результатов также установите `textAlignVertical` в значение `center`.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

### `fontVariant`

Позволяет задать все варианты шрифта для данного шрифта. Может быть задан с помощью массива перечислений или строки, разделенной пробелами, например, `'small-caps common-ligatures'`.

| Type                                                                                                                | Default |
| ------------------------------------------------------------------------------------------------------------------- | ------- |
| Массив enum(`'small-caps'`, `'oldstyle-nums'`, `'lining-nums'`, `'tabular-nums'`, `'proportional-nums'`) или строка | `[]`    |

### `letterSpacing`

Увеличивает или уменьшает интервал между символами. По умолчанию дополнительные интервалы между буквами отсутствуют.

| Type  |
| ----- |
| номер |

### `lineHeight`

| Type  |
| ----- |
| число |

### `textAlign`

Задает выравнивание текста. В Android значение 'justify' поддерживается только в версии Oreo (8.0) и выше (уровень API >= 26). На более низких версиях Android значение будет возвращаться к `left`.

| Тип                                                          | По умолчанию |
| ------------------------------------------------------------ | ------------ |
| enum(`'auto'`, `'left'`, `'right'`, `'center'`, `'justify'`) | `'auto'`     |

### `textAlignVertical` :simple-android:

| Type                                            | Default  |
| ----------------------------------------------- | -------- |
| enum(`'auto'`, `'top'`, `'bottom'`, `'center'`) | `'auto'` |

### `textDecorationColor` :simple-ios:

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |

### `textDecorationLine`

| Type                                                                        | Default  |
| --------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'underline'`, `'line-through'`, `'underline line-through'`) | `'none'` |

### `textDecorationStyle` :simple-ios:

| Type                                                | Default   |
| --------------------------------------------------- | --------- |
| enum(`'solid'`, `'double'`, `'dotted'`, `'dashed'`) | `'solid'` |

### `textShadowColor`

| Type                         |
| ---------------------------- |
| [color](../guides/colors.md) |

### `textShadowOffset`

| Type                                        |
| ------------------------------------------- |
| `object: {width?: number, height?: number}` |

### `textShadowRadius`

| Type  |
| ----- |
| число |

### `textTransform`

| Type                                                         | Default  |
| ------------------------------------------------------------ | -------- |
| enum(`'none'`, `'uppercase'`, `'lowercase'`, `'capitalize'`) | `'none'` |

### `verticalAlign` :simple-android:

| Type                                            | Default  |
| ----------------------------------------------- | -------- |
| enum(`'auto'`, `'top'`, `'bottom'`, `'middle'`) | `'auto'` |

### `writingDirection` :simple-ios:

| Type                             | Default  |
| -------------------------------- | -------- |
| enum(`'auto'`, `'ltr'`, `'rtl'`) | `'auto'` |
