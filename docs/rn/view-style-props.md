# View Style Props

## Пример

<div data-snack-id="@bndby/viewstyleprops" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## пропсы

### `backfaceVisibility`

| Type                          |
| ----------------------------- |
| enum(`'visible'`, `'hidden'`) |

### `backgroundColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderBottomColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderBottomEndRadius`

| Type  |
| ----- |
| Число |

### `borderBottomLeftRadius`

| Type  |
| ----- |
| Число |

### `borderBottomRightRadius`

| Type  |
| ----- |
| Число |

### `borderBottomStartRadius`

| Type  |
| ----- |
| Число |

### `borderBottomWidth`

| Type  |
| ----- |
| число |

### `borderColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderCurve` :simple-ios:

В iOS 13+ можно изменить угловую кривую границ.

| Type                             |
| -------------------------------- |
| `enum('circular', 'continuous')` |

### `borderEndColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderLeftColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderLeftWidth`

| Type  |
| ----- |
| число |

### `borderRadius`

Если закругленная граница не видна, попробуйте также применить `overflow: 'hidden'`.

| Type  |
| ----- |
| Число |

### `borderRightColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderRightWidth`

| Type  |
| ----- |
| число |

### `borderStartColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderStyle`

| Type                                |
| ----------------------------------- |
| `enum('solid', 'dotted', 'dashed')` |

### `borderTopColor`

| Type               |
| ------------------ |
| [color](colors.md) |

### `borderTopEndRadius`

| Тип   |
| ----- |
| число |

### `borderTopLeftRadius`

| Тип   |
| ----- |
| Число |

### `borderTopRightRadius`

| Type  |
| ----- |
| Число |

### `borderTopStartRadius`

| Тип   |
| ----- |
| число |

### `borderTopWidth`

| Тип   |
| ----- |
| число |

### `borderWidth`

| Тип   |
| ----- |
| число |

### `elevation` :simple-android:

Устанавливает высоту вида, используя базовый [elevation API](https://developer.android.com/training/material/shadows-clipping.html#Elevation). Это добавляет падающую тень к элементу и влияет на z-ордер для перекрывающихся видов. Поддерживается только в Android 5.0+, на более ранних версиях не действует.

| Тип   |
| ----- |
| номер |

### `opacity`

| Тип   |
| ----- |
| Число |

### `pointerEvents`

Определяет, может ли `View` быть целью событий касания.

-   `авто`: Вид может быть целью событий касания.

-   `'none'`: Представление никогда не является целью событий касания.

-   `'box-none'`: Представление никогда не является целью событий касания, но его вложенные представления могут быть таковыми.

-   `'box-only'`: Представление может быть целью событий касания, но его вложенные представления не могут быть.

| Type                                            |
| ----------------------------------------------- |
| `enum('auto', 'box-none', 'box-only', 'none' )` |
