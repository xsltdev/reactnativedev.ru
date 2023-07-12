# Layout Props

> Более подробные примеры этих свойств можно найти на странице [Layout with Flexbox](flexbox).

## Пример

Следующий пример показывает, как различные свойства могут влиять на макет React Native. Например, можно попробовать добавить или убрать квадраты из пользовательского интерфейса, изменяя значения свойства `flexWrap`.

=== "Typescript"

    <div data-snack-id="@bndby/layoutprops-example-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "Javascript"

    <div data-snack-id="@bndby/layoutprops-example-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#fbfcfd;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Реквизиты

### `alignContent`

`alignContent` управляет выравниванием строк в поперечном направлении, переопределяя `alignContent` родителя. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/align-content.

| Type                                                                                 | Required |
| ------------------------------------------------------------------------------------ | -------- |
| enum('flex-start', 'flex-end', 'center', 'stretch', 'space-between', 'space-around') | No       |

### `alignItems`

`alignItems` выравнивает дочерние элементы в поперечном направлении. Например, если дочерние элементы перетекают по вертикали, `alignItems` управляет тем, как они выравниваются по горизонтали. Работает подобно `align-items` в CSS (по умолчанию: stretch). Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/align-items.

| Type                                                            | Required |
| --------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'stretch', 'baseline') | Нет      |

### `alignSelf`

`alignSelf` управляет выравниванием дочернего элемента в поперечном направлении, переопределяя `alignItems` родительского элемента. Работает аналогично `align-self` в CSS (по умолчанию: auto). Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/align-self.

| Type                                                                    | Required |
| ----------------------------------------------------------------------- | -------- |
| enum('auto', 'flex-start', 'flex-end', 'center', 'stretch', 'baseline') | No       |

### `aspectRatio`

Соотношение сторон управляет размером неопределенного измерения узла. Более подробная информация приведена на сайте https://developer.mozilla.org/docs/Web/CSS/aspect-ratio.

-   Для узла с заданной шириной/высотой соотношение сторон управляет размером неопределенного измерения
-   В узле с заданным базисом flex соотношение сторон контролирует размер узла по поперечной оси, если он не задан.
-   В узле с функцией измерения соотношение сторон работает так, как будто функция измерения измеряет гибкую основу
-   В узле с функцией flex grow/shrink соотношение сторон управляет размером узла по поперечной оси, если оно не задано.
-   Соотношение сторон учитывает минимальные/максимальные размеры

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `borderBottomWidth`

`borderBottomWidth` работает так же, как `border-bottom-width` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/border-bottom-width.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `borderEndWidth`

Если направление равно `ltr`, то `borderEndWidth` эквивалентно `borderRightWidth`. Если направление `rtl`, то `borderEndWidth` эквивалентна `borderLeftWidth`.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `borderLeftWidth`

`borderLeftWidth` работает так же, как `border-left-width` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/border-left-width.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `borderRightWidth`

`borderRightWidth` работает так же, как `border-right-width` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/border-right-width.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `borderStartWidth`

Если направление равно `ltr`, то `borderStartWidth` эквивалентна `borderLeftWidth`. Если направление `rtl`, то `borderStartWidth` эквивалентна `borderRightWidth`.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `borderTopWidth`

`borderTopWidth` работает так же, как `border-top-width` в CSS. Подробнее см. https://developer.mozilla.org/docs/Web/CSS/border-top-width.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `borderWidth`

`borderWidth` работает так же, как `border-width` в CSS. Подробности см. на сайте https://developer.mozilla.org/en-US/docs/Web/CSS/border-width.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `bottom`

`bottom` - это количество логических пикселей для смещения нижнего края данного компонента.

Работает аналогично `bottom` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее о том, как `bottom` влияет на верстку, см. https://developer.mozilla.org/en-US/docs/Web/CSS/bottom.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `columnGap`

`columnGap` работает аналогично `column-gap` в CSS. В React Native поддерживаются только пиксельные единицы измерения. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/column-gap.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `direction`

`direction` задает направленность потока пользовательского интерфейса. По умолчанию используется `inherit`, за исключением корневого узла, который будет иметь значение, основанное на текущей локали. Более подробная информация приведена на сайте https://yogalayout.com/docs/layout-direction.

| Type                          | Required | Platform |
| ----------------------------- | -------- | -------- |
| enum('inherit', 'ltr', 'rtl') | No       | iOS      |

### `display`

`display` задает тип отображения данного компонента.

Он работает аналогично `display` в CSS, но поддерживает только 'flex' и 'none'. По умолчанию используется 'flex'.

| Type                 | Required |
| -------------------- | -------- |
| enum('none', 'flex') | No       |

### `end`

Когда направление равно `ltr`, `end` эквивалентно `right`. Когда направление равно `rtl`, `end` эквивалентен `left`.

Этот стиль имеет приоритет над стилями `left` и `right`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `flex`

В React Native `flex` работает не так, как в CSS. `flex` - это число, а не строка, и оно работает в соответствии с механизмом верстки [Yoga](https://github.com/facebook/yoga).

Если `flex` - положительное число, то это делает компонент гибким, и его размер будет пропорционален значению flex. Так, компонент с `flex`, равным 2, займет в два раза больше места, чем компонент с `flex`, равным 1. `flex: <положительное число>` эквивалентно `flexGrow: <положительное число>, flexShrink: 1, flexBasis: 0`.

Когда `flex` равен 0, компонент имеет размеры `ширина` и `высота` и является негибким.

Когда `flex` равен -1, компонент нормально размещается в соответствии с `width` и `height`. Однако если места недостаточно, компонент уменьшится до своих `minWidth` и `minHeight`.

Функции `flexGrow`, `flexShrink` и `flexBasis` работают так же, как и в CSS.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `flexBasis`

`flexBasis` - это независимый от оси способ задать размер элемента по умолчанию вдоль главной оси. Установка `flexBasis` дочернего элемента аналогична установке `ширины` этого элемента, если его родитель является контейнером с `flexDirection: row`, или установке `высоты` дочернего элемента, если его родитель является контейнером с `flexDirection: column`. Значение `flexBasis` элемента - это размер элемента по умолчанию, размер элемента до выполнения любых вычислений `flexGrow` и `flexShrink`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `flexDirection`

`flexDirection` управляет тем, в каких направлениях движутся дочерние элементы контейнера. `row` идет слева направо, `column` - сверху вниз, и вы, возможно, догадываетесь, что делают два других. Это работает так же, как `flex-direction` в CSS, только по умолчанию используется `column`. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/flex-direction.

| Type                                                   | Required |
| ------------------------------------------------------ | -------- |
| enum('row', 'row-reverse', 'column', 'column-reverse') | No       |

### `flexGrow`

Функция `flexGrow` описывает, как пространство внутри контейнера должно быть распределено между его дочерними элементами вдоль главной оси. После размещения своих дочерних элементов контейнер распределяет оставшееся пространство в соответствии со значениями flex grow, указанными его дочерними элементами.

`flexGrow` принимает любое значение с плавающей точкой >= 0, по умолчанию используется значение 0. Контейнер будет распределять оставшееся пространство между своими дочерними элементами с учетом значений `flexGrow` дочерних элементов.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `flexShrink`

`flexShrink` описывает, как уменьшать дочерние элементы вдоль главной оси в случае, когда суммарный размер дочерних элементов превышает размер контейнера на главной оси. Свойство `flexShrink` очень похоже на `flexGrow` и может рассматриваться аналогичным образом, если любой переполненный размер рассматривается как отрицательное оставшееся пространство. Эти два свойства также хорошо работают вместе, позволяя дочерним элементам увеличиваться и уменьшаться по мере необходимости.

`flexShrink` принимает любое значение с плавающей точкой >= 0, причем по умолчанию используется значение 0. Контейнер будет сжимать своих дочерних элементов, взвешивая значения `flexShrink` дочерних элементов.

| Тип   | Требуется |
| ----- | --------- |
| Число | Нет       |

### `flexWrap`

`flexWrap` управляет тем, могут ли дочерние элементы обернуться после того, как они попадут в конец гибкого контейнера. Работает аналогично `flex-wrap` в CSS (по умолчанию: nowrap). Более подробную информацию см. на сайте https://developer.mozilla.org/en-US/docs/Web/CSS/flex-wrap. Обратите внимание, что он больше не работает с `alignItems: stretch` (по умолчанию), поэтому вы можете использовать, например, `alignItems: flex-start` (подробности изменения: https://github.com/facebook/react-native/releases/tag/v0.28.0).

| Type                                   | Required |
| -------------------------------------- | -------- |
| enum('wrap', 'nowrap', 'wrap-reverse') | Нет      |

### `gap`

`gap` работает аналогично `gap` в CSS. В React Native поддерживаются только пиксельные единицы измерения. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/gap.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `height`

`height` задает высоту данного компонента.

Работает аналогично `height` в CSS, но в React Native необходимо использовать пункты или проценты. Эмы и другие единицы измерения не поддерживаются. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/height.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `justifyContent`

`justifyContent` выравнивает дочерние элементы по основному направлению. Например, если дочерние элементы перетекают по вертикали, `justifyContent` управляет тем, как они выравниваются по вертикали. Работает подобно `justify-content` в CSS (по умолчанию: flex-start). Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/justify-content.

| Type                                                                                      | Required |
| ----------------------------------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'space-between', 'space-around', 'space-evenly') | No       |

### `left`

`left` - это количество логических пикселей для смещения левого края данного компонента.

Работает аналогично `left` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее о том, как `left` влияет на верстку, см. https://developer.mozilla.org/en-US/docs/Web/CSS/left.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `margin`

Установка `margin` имеет тот же эффект, что и установка каждого из `marginTop`, `marginLeft`, `marginBottom` и `marginRight`. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/margin.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginBottom`

`marginBottom` работает подобно `margin-bottom` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/margin-bottom.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginEnd`

Если направление равно `ltr`, то `marginEnd` эквивалентно `marginRight`. Если направление `rtl`, то `marginEnd` эквивалентно `marginLeft`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginHorizontal`

Установка `marginHorizontal` имеет тот же эффект, что и установка `marginLeft` и `marginRight`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginLeft`

`marginLeft` работает подобно `margin-left` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/margin-left.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginRight`

`marginRight` работает так же, как `margin-right` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/margin-right.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginStart`

Если направление равно `ltr`, то `marginStart` эквивалентно `marginLeft`. Если направление `rtl`, то `marginStart` эквивалентно `marginRight`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginTop`

`marginTop` работает так же, как `margin-top` в CSS. Подробности см. на сайте https://developer.mozilla.org/en-US/docs/Web/CSS/margin-top.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `marginVertical`

Установка `marginVertical` имеет тот же эффект, что и установка `marginTop` и `marginBottom`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `maxHeight`

`maxHeight` - это максимальная высота для данного компонента в логических пикселях.

Работает аналогично `max-height` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/max-height.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `maxWidth`

`maxWidth` - это максимальная ширина данного компонента в логических пикселях.

Работает аналогично `max-width` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/max-width.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `minHeight`

`minHeight` - это минимальная высота для данного компонента в логических пикселях.

Работает аналогично `min-height` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Более подробную информацию см. на сайте https://developer.mozilla.org/en-US/docs/Web/CSS/min-height.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `minWidth`

`minWidth` - это минимальная ширина данного компонента в логических пикселях.

Работает аналогично `min-width` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/min-width.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

---

### `overflow`

`overflow` управляет тем, как измеряются и отображаются дочерние элементы. `overflow: hidden` заставляет виды обрезаться, а `overflow: scroll` заставляет виды измеряться независимо от главной оси их родителей. Работает аналогично `overflow` в CSS (по умолчанию: visible). Подробнее см. https://developer.mozilla.org/en/docs/Web/CSS/overflow.

| Type                                | Required |
| ----------------------------------- | -------- |
| enum('visible', 'hidden', 'scroll') | No       |

### `padding`

Установка `padding` имеет тот же эффект, что и установка каждого из `paddingTop`, `paddingBottom`, `paddingLeft` и `paddingRight`. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/padding.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingBottom`

`paddingBottom` работает подобно `padding-bottom` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/padding-bottom.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingEnd`

Если направление равно `ltr`, то `paddingEnd` эквивалентно `paddingRight`. Если направление `rtl`, то `paddingEnd` эквивалентно `paddingLeft`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingHorizontal`

Установка `paddingHorizontal` аналогична установке обоих `paddingLeft` и `paddingRight`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingLeft`

`paddingLeft` работает подобно `padding-left` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/padding-left.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingRight`

`paddingRight` работает так же, как `padding-right` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/padding-right.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingStart`

Если направление равно `ltr`, то `paddingStart` эквивалентно `paddingLeft`. Если направление `rtl`, то `paddingStart` эквивалентно `paddingRight`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `paddingTop`

`paddingTop` работает так же, как `padding-top` в CSS. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/padding-top.

| Type           | Required |
| -------------- | -------- |
| Число, ,строка | Нет      |

### `paddingVertical`

Установка `paddingVertical` аналогична установке `paddingTop` и `paddingBottom`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `position`

`position` в React Native аналогичен обычному CSS, но по умолчанию все имеет значение `relative`, поэтому позиционирование `absolute` всегда относительно родителя.

Если вы хотите позиционировать дочерний элемент с помощью определенного количества логических пикселей относительно родителя, задайте для него `absolute` position.

Если требуется позиционировать дочерний компонент относительно чего-то, что не является его родителем, не используйте для этого стили. Используйте дерево компонентов.

Подробнее о том, чем отличается `позиция` в React Native от CSS, см. https://github.com/facebook/yoga.

| Type                         | Required |
| ---------------------------- | -------- |
| enum('absolute', 'relative') | No       |

### `right`

`right` - это количество логических пикселей для смещения правого края данного компонента.

Работает аналогично `right` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее о том, как `right` влияет на верстку, см. https://developer.mozilla.org/en-US/docs/Web/CSS/right.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `rowGap`

`rowGap` работает аналогично `row-gap` в CSS. В React Native поддерживаются только пиксельные единицы измерения. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/row-gap.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |

### `start`

Когда направление равно `ltr`, `start` эквивалентно `left`. Если направление `rtl`, то `start` эквивалентен `right`.

Этот стиль имеет приоритет над стилями `left`, `right` и `end`.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `top`

`top` - это количество логических пикселей для смещения верхнего края данного компонента.

Работает аналогично `top` в CSS, но в React Native необходимо использовать точки или проценты. Эмы и другие единицы измерения не поддерживаются.

Подробнее о том, как `top` влияет на верстку, см. https://developer.mozilla.org/en-US/docs/Web/CSS/top.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `width`

`width` задает ширину данного компонента.

Он работает аналогично `width` в CSS, но в React Native необходимо использовать пункты или проценты. Эмы и другие единицы измерения не поддерживаются. Подробнее см. https://developer.mozilla.org/en-US/docs/Web/CSS/width.

| Type          | Required |
| ------------- | -------- |
| Число, строка | Нет      |

### `zIndex`

`zIndex` управляет тем, какие компоненты отображаются поверх других. Обычно `zIndex` не используется. Компоненты отображаются в соответствии с их порядком в дереве документа, поэтому более поздние компоненты рисуются поверх более ранних. Использование `zIndex` может быть полезно, если у вас есть анимация или пользовательские модальные интерфейсы, где такое поведение нежелательно.

Оно работает подобно свойству CSS `z-index` - компоненты с большим `zIndex` будут отрисовываться сверху. Считайте, что направление z направлено от телефона к вашему глазному яблоку. Более подробную информацию см. на сайте https://developer.mozilla.org/en-US/docs/Web/CSS/z-index.

В iOS для корректной работы `zIndex` может потребоваться, чтобы `View` были братьями и сестрами друг друга.

| Type  | Required |
| ----- | -------- |
| Число | Нет      |
