# Макет с Flexbox

Компонент может задавать расположение своих дочерних элементов с помощью алгоритма Flexbox. Flexbox предназначен для обеспечения согласованного расположения на экранах разных размеров.

Обычно вы используете комбинацию `flexDirection`, `alignItems` и `justifyContent` для достижения правильного расположения.

!!!warning ""

    Flexbox работает в React Native так же, как и в CSS в Интернете, за некоторыми исключениями.

    Значения по умолчанию отличаются: `flexDirection` по умолчанию `column` вместо `row`, `alignContent` по умолчанию `flex-start` вместо `stretch`, `flexShrink` по умолчанию `0` вместо `1`, параметр `flex` поддерживает только одно число.

## Flex

[`flex`]layout-props.md#flex) определяет, как ваши элементы будут **"заполнять "** доступное пространство вдоль главной оси. Пространство будет распределено в соответствии со свойством `flex` каждого элемента.

В следующем примере красное, оранжевое и зеленое представления являются дочерними в представлении контейнера, для которого установлен параметр `flex: 1`. Красный вид использует `flex: 1`, оранжевый вид использует `flex: 2`, а зеленый вид использует `flex: 3`. **1+2+3 = 6**, что означает, что красный вид получит `1/6` пространства, оранжевый `2/6` пространства, а зеленый `3/6` пространства.

<div data-snack-id="@bndby/flex-example-1" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Flex Direction

[`flexDirection`]layout-props.md#flexdirection) управляет направлением, в котором располагаются дочерние узлы узла. Это также называется главной осью. Поперечная ось — это ось, перпендикулярная главной оси, или ось, по которой располагаются линии обводки.

-   `column` (**значение по умолчанию**) Выравнивание дочерних элементов сверху вниз. Если включено обертывание, то следующая строка будет начинаться справа от первого элемента в верхней части контейнера.
-   `row` Выравнивание детей слева направо. Если включено обертывание, то следующая строка будет начинаться под первым элементом слева от контейнера.
-   `column-reverse` Выравнивание детей снизу вверх. Если обертывание включено, то следующая строка будет начинаться справа от первого элемента внизу контейнера.
-   `row-reverse` Выравнивание детей справа налево. Если обертывание включено, то следующая строка будет начинаться под первым элементом справа от контейнера.

Вы можете узнать больше [здесь](https://yogalayout.com/docs/flex-direction).

=== "TypeScript"

    <div data-snack-id="@bndby/ts-flex-direction" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-flex-direction" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Layout Direction

Layout [`direction`]layout-props.md#direction) определяет направление, в котором должны располагаться дочерние элементы и текст в иерархии. Направление компоновки также влияет на то, к какому краю относятся `start` и `end`. По умолчанию React Native выстраивает макет в направлении LTR. В этом режиме `start` относится к левому краю, а `end` — к правому.

-   `LTR` (**значение по умолчанию**) Текст и дочерние элементы располагаются слева направо. Маржа и отступы, примененные к началу элемента, применяются с левой стороны.
-   `RTL` Текст и дочерние элементы располагаются справа налево. Маржа и отступы, применяемые к началу элемента, применяются с правой стороны.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-flex-direction" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-flex-direction" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Justify Content

[`justifyContent`]layout-props.md#justifycontent) описывает, как выровнять дочерние элементы по главной оси их контейнера. Например, вы можете использовать это свойство для центрирования дочернего элемента по горизонтали в контейнере с `flexDirection`, установленным на `row`, или по вертикали в контейнере с `flexDirection`, установленным на `column`.

-   `flex-start` (**значение по умолчанию**) Выравнивание дочерних элементов контейнера по началу главной оси контейнера.
-   `flex-end` Выравнивание дочерних элементов контейнера по концу главной оси контейнера.
-   `center` Выравнивание дочерних элементов контейнера по центру главной оси контейнера.
-   `space-between` Равномерно распределяет дочерние элементы по главной оси контейнера, распределяя оставшееся пространство между ними.
-   `space-around` Равномерно распределяет пространство между детьми по главной оси контейнера, распределяя оставшееся пространство вокруг детей. По сравнению с `space-between`, использование `space-around` приведет к распределению пространства между началом первого и концом последнего ребенка.
-   `space-evenly` Равномерно распределить дочерние элементы в контейнере выравнивания вдоль главной оси. Расстояние между каждой парой соседних элементов, начальным краем главной оси и первым элементом и конечным краем главной оси и последним элементом, абсолютно одинаково.

Вы можете узнать больше [здесь](https://yogalayout.com/docs/justify-content).

=== "TypeScript"

    <div data-snack-id="@bndby/ts-justify-content" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-justify-content" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Align Items

[`alignItems`]layout-props.md#alignitems) описывает, как выровнять дочерние элементы по поперечной оси их контейнера. Это очень похоже на `justifyContent`, но вместо применения к главной оси, `alignItems` применяется к поперечной оси.

-   `stretch` (**значение по умолчанию**) Растягивает дочерние элементы контейнера в соответствии с `высотой` поперечной оси контейнера.
-   `flex-start` Выравнивает дочерние элементы контейнера по началу поперечной оси контейнера.
-   `flex-end` Выравнивание дочерних элементов контейнера по концу поперечной оси контейнера.
-   `center` Выравнивание дочерних элементов контейнера по центру поперечной оси контейнера.
-   `baseline` Выравнивание дочерних элементов контейнера по общей базовой линии. Отдельные дочерние элементы могут быть установлены в качестве базовой линии для своих родителей.

!!!info ""

    Чтобы `stretch` имело эффект, дочерние элементы не должны иметь фиксированный размер вдоль вторичной оси. В следующем примере установка `alignItems: stretch` ничего не дает, пока `width: 50` не будет удалено из дочерних элементов.

Вы можете узнать больше [здесь](https://yogalayout.com/docs/align-items).

=== "TypeScript"

    <div data-snack-id="@bndby/ts-align-items" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-align-items" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Align Self

[`alignSelf`]layout-props.md#alignself) имеет те же опции и эффект, что и `alignItems`, но вместо того, чтобы влиять на дочерние элементы внутри контейнера, вы можете применить это свойство к одному дочернему элементу, чтобы изменить его выравнивание внутри родителя. `alignSelf` переопределяет любую опцию, установленную родителем с помощью `alignItems`.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-align-self" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-align-self" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Align Content

[alignContent]layout-props.md#aligncontent) определяет распределение линий вдоль поперечной оси. Это имеет значение только в том случае, если элементы обернуты в несколько линий с помощью `flexWrap`.

-   `flex-start` (**значение по умолчанию**) Выравнивание обернутых линий по началу поперечной оси контейнера.
-   `flex-end` Выравнивает обернутые линии по концу поперечной оси контейнера.
-   `stretch` (_значение по умолчанию при использовании Yoga в Интернете_) Растягивает обернутые линии в соответствии с высотой поперечной оси контейнера.
-   `center` Выравнивание обернутых линий по центру поперечной оси контейнера.
-   `space-between` Равномерно распределять обернутые линии по поперечной оси контейнера, распределяя оставшееся пространство между линиями.
-   `space-around` Равномерно распределять обернутые линии по поперечной оси контейнера, распределяя оставшееся пространство вокруг линий. По сравнению с `space-between`, использование `space-around` приводит к распределению пространства между началом первой и концом последней строки.

Вы можете узнать больше [здесь](https://yogalayout.com/docs/align-content).

=== "TypeScript"

    <div data-snack-id="@bndby/ts-align-content" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-align-content" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Flex Wrap

Свойство [`flexWrap`]layout-props.md#flexwrap) устанавливается для контейнеров и управляет тем, что происходит, когда дочерние элементы превышают размер контейнера вдоль главной оси. По умолчанию дочерние элементы вытягиваются в одну линию (что может привести к сжатию элементов). Если обертывание разрешено, элементы будут обернуты в несколько линий вдоль главной оси, если это необходимо.

При обертывании строк можно использовать `alignContent`, чтобы указать, как строки будут располагаться в контейнере. Подробнее [здесь](https://yogalayout.com/docs/flex-wrap).

=== "TypeScript"

    <div data-snack-id="@bndby/ts-flex-wrap" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-flex-wrap" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Flex Basis, Grow и Shrink

-   [`flexBasis`]layout-props.md#flexbasis) — это независимый от оси способ задать размер элемента по умолчанию вдоль главной оси. Установка `flexBasis` дочернего элемента аналогична установке `width` этого элемента, если его родитель является контейнером с `flexDirection: row` или установке `height` дочернего элемента, если его родитель является контейнером с `flexDirection: column`. `flexBasis` элемента — это размер элемента по умолчанию, размер элемента до выполнения любых вычислений `flexGrow` и `flexShrink`.

-   [`flexGrow`]layout-props.md#flexgrow) описывает, сколько места внутри контейнера должно быть распределено между его дочерними элементами вдоль главной оси. После размещения дочерних элементов контейнер распределяет оставшееся пространство в соответствии со значениями flex grow, заданными его дочерними элементами.

    `flexGrow` принимает любое значение с плавающей точкой >= 0, при этом 0 является значением по умолчанию. Контейнер будет распределять оставшееся пространство между своими дочерними элементами, взвешивая значения `flexGrow` дочерних элементов.

-   [`flexShrink`]layout-props.md#flexshrink) описывает, как уменьшить дочерние элементы вдоль главной оси в случае, когда общий размер дочерних элементов превышает размер контейнера на главной оси. Свойство `flexShrink` очень похоже на `flexGrow` и может быть рассмотрено таким же образом, если любой переполняющий размер рассматривается как отрицательное оставшееся пространство. Эти два свойства также хорошо работают вместе, позволяя детям увеличиваться и уменьшаться по мере необходимости.

    `flexShrink` принимает любое значение с плавающей точкой >= 0, при этом по умолчанию используется значение 0 (в Интернете по умолчанию используется значение 1). Контейнер будет уменьшать свои дочерние элементы, взвешивая значения `flexShrink` дочерних элементов.

Вы можете узнать больше [здесь](https://yogalayout.com/docs/flex).

=== "TypeScript"

    <div data-snack-id="@bndby/ts-flex-basis-grow-and-shrink" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-flex-basis-grow-and-shrink" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Row Gap, Column Gap и Gap

-   [`rowGap`]layout-props.md#rowgap) задает размер промежутка (желоба) между строками элемента.
-   [`columnGap`]layout-props.md#columngap) задает размер промежутка (желоба) между колонками элемента.
-   [`gap`]layout-props.md#gap) задает размер промежутка (желоба) между строками и столбцами. Это сокращение для `rowGap` и `columnGap`.

Вы можете использовать `flexWrap` и `alignContent` вместе с `gap` для добавления согласованного расстояния между элементами.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-row-gap-and-column-gap" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-row-gap-and-column-gap" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Ширина и высота

Свойство `width` задает ширину области содержимого элемента. Аналогично, свойство `height` определяет высоту области содержимого элемента.

И `width`, и `height` могут принимать следующие значения:

-   `auto` (**значение по умолчанию**) React Native рассчитывает ширину/высоту для элемента на основе его содержимого, будь то другие дочерние элементы, текст или изображение.
-   `pixels` Определяет ширину/высоту в абсолютных пикселях. В зависимости от других стилей, установленных для компонента, это может быть или не быть окончательным размером узла.
-   `percentage` Определяет ширину или высоту в процентах от ширины или высоты родителя, соответственно.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-width-and-height" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-width-and-height" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Абсолютное и относительное позиционирование

Тип `position` элемента определяет, как он располагается внутри своего родителя.

-   `relative` (**значение по умолчанию**) По умолчанию элемент позиционируется относительно. Это означает, что элемент позиционируется в соответствии с нормальным потоком макета, а затем смещается относительно этой позиции на основе значений `top`, `right`, `bottom` и `left`. Смещение не влияет на положение дочерних или родительских элементов.
-   `absolute` При абсолютном позиционировании элемент не участвует в обычном потоке компоновки. Вместо этого он располагается независимо от своих братьев и сестер. Положение определяется на основе значений `top`, `right`, `bottom` и `left`.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-absolute-" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-absolute-" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Углубляемся

Посмотрите интерактивный [yoga playground](https://yogalayout.com/playground), который вы можете использовать для лучшего понимания flexbox.

Мы рассмотрели основы, но существует множество других стилей, которые могут понадобиться для макетов. Полный список пропсов, управляющих компоновкой, задокументирован [здесь]layout-props.md).

Кроме того, вы можете посмотреть некоторые примеры от [Wix Engineers](https://medium.com/wix-engineering/the-full-react-native-layout-cheat-sheet-a4147802405c).
