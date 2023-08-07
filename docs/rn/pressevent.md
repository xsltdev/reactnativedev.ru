# PressEvent Object Type

Объект **`PressEvent`** возвращается в обратном вызове в результате взаимодействия с пользователем по нажатию кнопки, например, `onPress` в компоненте [Button](button.md).

## Пример

```js
{
    changedTouches: [PressEvent],
    identifier: 1,
    locationX: 8,
    locationY: 4.5,
    pageX: 24,
    pageY: 49.5,
    target: 1127,
    timestamp: 85131876.58868201,
    touches: []
}
```

## Ключи и значения

### `changedTouches`

Массив всех событий `PressEvents`, которые изменились с момента последнего события.

| Type                 | Optional |
| -------------------- | -------- |
| array of PressEvents | No       |

### `force` :simple-ios:

Величина силы, используемой при нажатии 3D Touch. Возвращает плавающее значение в диапазоне от `0.0` до `1.0`.

| Type   | Optional |
| ------ | -------- |
| number | Yes      |

### `identifier`

Уникальный числовой идентификатор, присвоенный событию.

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `locationX`

Координата X начала касания внутри области касания (относительно элемента).

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `locationY`

Координата Y начала касания внутри области касания (относительно элемента).

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `pageX`

Координата X точки касания на экране (относительно корневого вида).

| Type  | Optional |
| ----- | -------- |
| Число | Нет      |

### `pageY`

Координата начала координат Y на экране (относительно корневого вида).

| Тип   | Необязательно |
| ----- | ------------- |
| Число | Нет           |

### `target`

Идентификатор узла элемента, получившего событие нажатия.

| Тип                        | Необязательный |
| -------------------------- | -------------- |
| число, `null`, `undefined` | Нет            |

### `timestamp`

Значение временной метки, когда произошло событие `PressEvent`. Значение представлено в миллисекундах.

| Тип   | Необязательно |
| ----- | ------------- |
| Число | Нет           |

### `touches`

Массив всех текущих событий нажатия на экране.

| Тип                    | Необязательный |
| ---------------------- | -------------- |
| массив событий нажатия | Нет            |

## Используется для

-   [`Button`](button.md)
-   [`PanResponder`](panresponder.md)
-   [`Pressable`](pressable.md)
-   [`ScrollView`](scrollview.md)
-   [`Text`](text.md)
-   [`TextInput`](textinput.md)
-   [`TouchableHighlight`](touchablenativefeedback.md)
-   [`TouchableOpacity`](touchablewithoutfeedback.md)
-   [`TouchableNativeFeedback`](touchablenativefeedback.md)
-   [`TouchableWithoutFeedback`](touchablewithoutfeedback.md)
-   [`View`](view.md)
