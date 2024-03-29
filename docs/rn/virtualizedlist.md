# VirtualizedList

Базовая реализация для более удобных компонентов [`<FlatList>`](flatlist.md) и [`<SectionList>`](sectionlist.md), которые также лучше документированы. В целом, их следует использовать только в том случае, если вам нужна большая гибкость, чем обеспечивает [`FlatList`](flatlist.md), например, для использования с неизменяемыми данными вместо обычных массивов.

Виртуализация значительно улучшает потребление памяти и производительность больших списков, поддерживая конечное окно рендеринга активных элементов и заменяя все элементы за пределами окна рендеринга пустым пространством соответствующего размера. Окно адаптируется к поведению прокрутки, и элементы отображаются постепенно с низким прайсом (после любых запущенных взаимодействий), если они находятся далеко от видимой области, или с высоким прайсом в противном случае, чтобы минимизировать возможность увидеть пустое пространство.

## Пример

=== "TypeScript"

    <div data-snack-id="@bndby/virtualizedlistexample-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/virtualizedlistexample-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Некоторые предостережения:

-   Внутреннее состояние не сохраняется при прокрутке содержимого за пределы окна рендеринга. Убедитесь, что все ваши данные хранятся в данных элемента или во внешних хранилищах, таких как Flux, Redux или Relay.
-   Это `PureComponent`, что означает, что он не будет перерисовываться, если `props` неглубоко равны. Убедитесь, что все, от чего зависит функция `renderItem`, передается в качестве пропса (например, `extraData`), который не становится `===` после обновления, иначе ваш пользовательский интерфейс может не обновляться при изменениях. Это относится к пропсу `data` и состоянию родительского компонента.
-   Чтобы ограничить память и обеспечить плавную прокрутку, содержимое отображается асинхронно вне экрана. Это означает, что можно прокручивать страницу быстрее, чем скорость заполнения, и на мгновение увидеть пустое содержимое. Это компромисс, который может быть скорректирован в соответствии с потребностями каждого приложения, и мы работаем над его улучшением за кулисами.
-   По умолчанию список ищет свойство `key` у каждого элемента и использует его для ключа React. В качестве альтернативы вы можете предоставить собственный пропс `keyExtractor`.

## пропсы

### [ScrollView Props](scrollview.md#props)

Наследует [ScrollView Props](scrollview.md#props).

### data

Непрозрачный тип данных, передаваемый в `getItem` и `getItemCount` для получения элементов.

| Тип   |
| ----- |
| любой |

### getItem

Обязательно.

```ts
(data: any, index: number) => any;
```

Общий аксессор для извлечения элемента из любого вида сгустка данных.

| Тип     |
| ------- |
| функция |

### getItemCount

Обязательно.

```tsx
(data: any) => number;
```

Определяет, сколько элементов находится в блоке данных.

| Тип     |
| ------- |
| функция |

### renderItem

Обязательно.

```tsx
 (info: any) => ?React.Element<any>
```

Берет элемент из `data` и отображает его в списке

| Тип     |
| ------- |
| функция |

### CellRendererComponent

`CellRendererComponent` позволяет настроить, как ячейки, отображаемые `renderItem`/`ListItemComponent`, будут обернуты при помещении в основной `ScrollView`. Этот компонент должен принимать обработчики событий, которые уведомляют `VirtualizedList` об изменениях в ячейке.

| Тип                                      |
| ---------------------------------------- |
| `React.ComponentType<CellRendererProps>` |

### ItemSeparatorComponent

Рендерится между каждым элементом, но не сверху или снизу. По умолчанию предоставляются пропсы `highlighted` и `leadingItem`. `renderItem` предоставляет `separators.highlight`/`unhighlight`, которые обновляют пропс `highlighted`, но вы также можете добавить пользовательские пропсы с помощью `separators.updateProps`. Может быть компонентом React (например, `SomeComponent`) или элементом React (например, `<SomeComponent />`).

| Тип                         |
| --------------------------- |
| компонент, функция, элемент |

### ListEmptyComponent

Отображается, когда список пуст. Может быть React компонентом (например, `SomeComponent`) или React элементом (например, `<SomeComponent />`).

| Тип                |
| ------------------ |
| компонент, элемент |

### ListItemComponent

Каждый элемент данных отображается с помощью этого элемента. Это может быть класс React Component или функция рендеринга.

| Тип                |
| ------------------ |
| компонент, функция |

### ListFooterComponent

Отображается в нижней части всех элементов. Может быть React компонентом (например, `SomeComponent`) или React элементом (например, `<SomeComponent />`).

| Тип                |
| ------------------ |
| компонент, элемент |

### ListFooterComponentStyle

Стилизация для внутреннего представления для `ListFooterComponent`.

| Type          | Required |
| ------------- | -------- |
| ViewStyleProp | Нет      |

### ListHeaderComponent

Отображается в верхней части всех элементов. Может быть компонентом React (например, `SomeComponent`) или элементом React (например, `<SomeComponent />`).

| Тип                |
| ------------------ |
| компонент, элемент |

### ListHeaderComponentStyle

Стилизация для внутреннего представления для `ListHeaderComponent`.

| Type                                       |
| ------------------------------------------ |
| [Стиль представления](view-style-props.md) |

### debug

`debug` включит дополнительное протоколирование и визуальные наложения для помощи в отладке использования и реализации, но со значительным снижением производительности.

| Тип     |
| ------- |
| boolean |

### disableVirtualization

!!!danger "Устарело"

    Виртуализация обеспечивает значительную оптимизацию производительности и памяти, но полностью размонтирует экземпляры реактора, находящиеся за пределами окна рендеринга. Вы должны отключать ее только в целях отладки.

| Тип     |
| ------- |
| boolean |

### extraData

Свойство-маркер для указания списку на повторный рендеринг (поскольку он реализует `PureComponent`). Если какая-либо из ваших функций `renderItem`, `Header`, `Footer` и т.д. зависит от чего-либо вне свойства `data`, поместите его сюда и обращайтесь с ним неизменно.

| Type  |
| ----- |
| любой |

### getItemLayout

```ts
(
  data: any,
  index: number,
) => {length: number, offset: number, index: number}
```

| Тип     |
| ------- |
| функция |

### horizontal

Если `true`, элементы отображаются рядом друг с другом горизонтально, а не вертикально.

| Тип     |
| ------- |
| boolean |

### initialNumToRender

Сколько элементов нужно отобразить в начальной партии. Этого должно быть достаточно, чтобы заполнить экран, но не больше. Обратите внимание, что эти элементы никогда не будут размонтированы как часть оконного рендеринга, чтобы улучшить воспринимаемую производительность действий прокрутки к вершине.

| Тип   | По умолчанию |
| ----- | ------------ |
| Число | `10`         |

### initialScrollIndex

Вместо того чтобы начинать сверху с первого элемента, начните с `initialScrollIndex`. Это отключает оптимизацию "прокрутки к вершине", которая сохраняет первые элементы `initialNumToRender` всегда отрисованными и сразу отрисовывает элементы, начинающиеся с этого начального индекса. Требует реализации `getItemLayout`.

| Тип   |
| ----- |
| число |

### inverted

Изменяет направление прокрутки. Использует масштабные преобразования `-1`.

| Тип     |
| ------- |
| boolean |

### listKey

Уникальный идентификатор для данного списка. Если существует несколько `VirtualizedList` на одном уровне вложенности внутри другого `VirtualizedList`, этот ключ необходим для правильной работы виртуализации.

| Type   | Required |
| ------ | -------- |
| строка | True     |

### keyExtractor

```ts
(item: any, index: number) => string;
```

Используется для извлечения уникального ключа для данного элемента по указанному индексу. Ключ используется для кэширования и в качестве ключа react для отслеживания переупорядочивания элементов. По умолчанию экстрактор проверяет `item.key`, затем `item.id`, а затем возвращается к использованию индекса, как это делает React.

| Тип     |
| ------- |
| функция |

### maxToRenderPerBatch

Максимальное количество элементов для рендеринга в каждом инкрементальном пакете рендеринга. Чем больше элементов отрисовывается одновременно, тем лучше скорость заполнения, но отзывчивость может пострадать, поскольку отрисовка содержимого может мешать реагированию на нажатия кнопок или другие взаимодействия.

| Тип   |
| ----- |
| число |

### onEndReached

Вызывается один раз, когда позиция прокрутки оказывается в пределах `onEndReachedThreshold` от логического конца списка.

| Type                                     |
| ---------------------------------------- |
| (info: {distanceFromEnd: число}) => void |

### onEndReachedThreshold

На каком расстоянии от конца (в единицах видимой длины списка) должен находиться край списка от конца содержимого, чтобы сработал обратный вызов `onEndReached`. Таким образом, значение `0.5` вызовет `onEndReached`, когда конец содержимого будет находиться в пределах половины видимой длины списка.

| Тип   | По умолчанию |
| ----- | ------------ |
| Число | `2`          |

### onRefresh

```ts
() => void;
```

Если он указан, будет добавлен стандартный `RefreshControl` для функциональности "Pull to Refresh". Убедитесь, что вы также правильно установили параметр `refreshing`.

| Тип     |
| ------- |
| функция |

### onScrollToIndexFailed

```ts
(info: {
  index: number,
  highestMeasuredFrameIndex: number,
  averageItemLength: number,
}) => void;
```

Используется для обработки сбоев при прокрутке до индекса, который еще не был измерен. Рекомендуется либо вычислить собственное смещение и `scrollTo` его, либо прокрутить как можно дальше, а затем повторить попытку после того, как будет отображено больше элементов.

| Тип     |
| ------- |
| функция |

### onStartReached

Вызывается один раз, когда позиция прокрутки оказывается в пределах `onStartReachedThreshold` от логического начала списка.

| Type                                       |
| ------------------------------------------ |
| (info: {distanceFromStart: число}) => void |

### onStartReachedThreshold

На каком расстоянии от начала (в единицах видимой длины списка) должен находиться передний край списка от начала содержимого, чтобы сработал обратный вызов `onStartReached`. Таким образом, значение `0.5` будет вызывать `onStartReached`, когда начало содержимого находится в пределах половины видимой длины списка.

| Тип   | По умолчанию |
| ----- | ------------ |
| Число | `2`          |

### onViewableItemsChanged

Вызывается, когда изменяется просматриваемость строк, как определено параметром `viewabilityConfig`.

| Type                                                                                                                         |
| ---------------------------------------------------------------------------------------------------------------------------- |
| (обратный вызов: {changed: array of [ViewToken](viewtoken.md)s, viewableItems: array of [ViewToken](viewtoken.md)s}) => void |

### persistentScrollbar

| Type |
| ---- |
| bool |

### progressViewOffset

Установите это значение, если необходимо смещение для правильного отображения индикатора загрузки.

| Тип   |
| ----- |
| число |

### refreshControl

Пользовательский элемент управления обновлением. Когда он установлен, он заменяет компонент по умолчанию `<RefreshControl>`, созданный внутри. пропсы `onRefresh` и `refreshing` также игнорируются. Работает только для вертикального `VirtualizedList`.

| Тип     |
| ------- |
| элемент |

### refreshing

Установите значение `true` в ожидании новых данных от обновления.

| Тип     |
| ------- |
| boolean |

### removeClippedSubviews

Это может улучшить производительность прокрутки для больших списков.

!!!note "Примечание"

    В некоторых случаях может иметь ошибки (отсутствие содержимого) - используйте на свой страх и риск.

| Тип     |
| ------- |
| boolean |

### renderScrollComponent

```ts
(props: object) => element;
```

Отображение пользовательского компонента прокрутки, например, с по-другому оформленным `RefreshControl`.

| Тип     |
| ------- |
| функция |

### viewabilityConfig

Смотрите `ViewabilityHelper.js` для типа потока и дальнейшей документации.

| Type              |
| ----------------- |
| ViewabilityConfig |

### viewabilityConfigCallbackPairs

Список пар `ViewabilityConfig`/`onViewableItemsChanged`. Конкретный `onViewableItemsChanged` будет вызван при выполнении условий соответствующего `ViewabilityConfig`. Смотрите `ViewabilityHelper.js` для типа потока и дальнейшей документации.

| Тип                                  |
| ------------------------------------ |
| Массив ViewabilityConfigCallbackPair |

### updateCellsBatchingPeriod

Количество времени между партиями рендеринга элементов с низким приоритетом, например, для рендеринга элементов, расположенных далеко от экрана. Аналогичный компромисс между скоростью заполнения и отзывчивостью, как и `maxToRenderPerBatch`.

| Тип   |
| ----- |
| число |

### windowSize

Определяет максимальное количество элементов, отображаемых за пределами видимой области, в единицах видимой длины. Так, если ваш список заполняет весь экран, то `windowSize={21}` (по умолчанию) будет отображаться видимая область экрана плюс до `10` экранов выше и `10` ниже области просмотра. Уменьшение этого числа уменьшит потребление памяти и может улучшить производительность, но увеличит вероятность того, что при быстрой прокрутке могут появиться мгновенные пустые области не отрендеренного содержимого.

| Тип   |
| ----- |
| число |

## Методы

### flashScrollIndicators()

```ts
flashScrollIndicators();
```

### getScrollableNode()

```ts
getScrollableNode(): any;
```

### getScrollRef()

```ts
getScrollRef():
  | React.ElementRef<typeof ScrollView>
  | React.ElementRef<typeof View>
  | null;
```

### getScrollResponder()

```ts
getScrollResponder () => ScrollResponderMixin | null;
```

Предоставляет обращение к базовому ответчику прокрутки. Обратите внимание, что `this._scrollRef` может не быть `ScrollView`, поэтому нам нужно проверить, что он отвечает на `getScrollResponder` перед вызовом.

### scrollToEnd()

```ts
scrollToEnd(params?: {animated?: boolean});
```

Прокручивает содержимое до конца. Может быть неаккуратным без пропса `getItemLayout`.

**Параметры:**

| Имя    | Тип    |
| ------ | ------ |
| params | object |

Допустимыми ключами `params` являются:

-   `'animated'` (boolean) - Должен ли список делать анимацию при прокрутке. По умолчанию `true`.

### scrollToIndex()

```ts
scrollToIndex(params: {
  index: number;
  animated?: boolean;
  viewOffset?: number;
  viewPosition?: number;
});
```

Valid `params` consist of:

-   'index' (number). Required.
-   'animated' (boolean). Optional.
-   'viewOffset' (number). Optional.
-   'viewPosition' (number). Optional.

### scrollToItem()

```ts
scrollToItem(params: {
  item: ItemT;
  animated?: boolean;
  viewOffset?: number;
  viewPosition?: number;
});
```

Допустимые `params` состоят из:

-   'item' (элемент). Требуется.
-   'animated' (булево). Необязательно.
-   'viewOffset' (число). Необязательно.
-   'viewPosition' (число). Необязательно.

### scrollToOffset()

```ts
scrollToOffset(params: {
  offset: number;
  animated?: boolean;
});
```

Прокрутка до определенного смещения пикселя содержимого в списке.

Параметр `offset` указывает на смещение, до которого нужно прокрутить список. В случае, если `horizontal` равен `true`, смещением будет значение `x`, в любом другом случае смещением будет значение `y`.

Параметр `animated` (по умолчанию `true`) определяет, будет ли список делать анимацию при прокрутке.
