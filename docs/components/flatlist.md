---
id: flatlist
title: FlatList
---

Производительный интерфейс для вывода основных плоских списков, поддерживающий наиболее удобные функции:

-   Полностью кроссплатформенный.
-   Дополнительный горизонтальный режим.
-   Настраиваемые обратные вызовы для просмотра.
-   Поддержка заголовков.
-   Поддержка нижнего колонтитула.
-   Поддержка разделителей.
-   Нажатие для обновления.
-   Загрузка прокрутки.
-   Поддержка `ScrollToIndex`.
-   Поддержка нескольких столбцов.

Если вам нужна поддержка секций, используйте [`<SectionList>`](sectionlist.md).

## Пример

=== "Typescript"

    <div data-snack-id="@bndby/ts-flatlist-simple" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "Javascript"

    <div data-snack-id="@bndby/js-flatlist-simple" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Для отображения нескольких колонок используйте параметр [`numColumns`](flatlist.md#numcolumns). Использование этого подхода вместо макета `flexWrap` может предотвратить конфликты с логикой высоты элементов.

Более сложный, выбираемый пример ниже.

-   Передавая `extraData={selectedId}` в `FlatList`, мы убеждаемся, что `FlatList` сам будет перерисовываться при изменении состояния. Без установки этого параметра `FlatList` не будет знать, что ему нужно перерисовать элементы, потому что он является `PureComponent` и сравнение параметров не покажет никаких изменений.
-   `keyExtractor` указывает списку использовать `id` для ключей реакции вместо свойства по умолчанию `key`.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-flatlist-selectable" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-flatlist-selectable" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Это удобная обертка вокруг [`<VirtualizedList>`](virtualizedlist.md), и поэтому наследует его реквизиты (а также реквизиты [`<ScrollView>`](scrollview.md)), которые не перечислены здесь явно, со следующими оговорками:

-   Внутреннее состояние не сохраняется при прокрутке содержимого за пределы окна рендеринга. Убедитесь, что все ваши данные хранятся в данных элемента или во внешних хранилищах, таких как Flux, Redux или Relay.
-   Это `PureComponent`, что означает, что он не будет перерисовываться, если `props` остаются неглубоко равными. Убедитесь, что все, от чего зависит функция `renderItem`, передается в качестве реквизита (например, `extraData`), который не становится `===` после обновления, иначе ваш пользовательский интерфейс может не обновляться при изменениях. Это относится к реквизиту `data` и состоянию родительского компонента.

-   Чтобы ограничить память и обеспечить плавную прокрутку, содержимое отображается асинхронно вне экрана. Это означает, что можно прокручивать страницу быстрее, чем скорость заполнения, и на мгновение увидеть пустое содержимое. Это компромисс, который может быть скорректирован в соответствии с потребностями каждого приложения, и мы работаем над его улучшением за кулисами.

-   По умолчанию список ищет свойство `key` у каждого элемента и использует его для ключа React. В качестве альтернативы вы можете предоставить собственный реквизит `keyExtractor`.

## Реквизиты

### [Реквизиты ScrollView](scrollview.md#props)

Наследует [ScrollView Props](scrollview.md#props), если он не вложен в другой `FlatList` той же ориентации.

### `renderItem` (обязательно)

```ts
renderItem({
  item: ItemT,
  index: number,
  separators: {
    highlight: () => void;
    unhighlight: () => void;
    updateProps: (select: 'leading' | 'trailing', newProps: any) => void;
  }
}): JSX.Element;
```

Берет элемент из `data` и отображает его в списке.

Предоставляет дополнительные метаданные, такие как `index`, если они вам нужны, а также более общую функцию `separators.updateProps`, которая позволяет вам установить любой реквизит, который вы хотите изменить отображение ведущего или последующего разделителя в случае, если более распространенные `highlight` и `unhighlight` (которые устанавливают реквизит `highlighted: boolean`) недостаточны для вашего случая использования.

| Type     |
| -------- |
| function |

-   `item` (Объект): Выводимый элемент из `data`.
-   `index` (число): Индекс, соответствующий этому элементу в массиве `data`.
-   `separators` (Объект).
    -   `highlight` (Функция)
    -   `unhighlight` (Функция)
    -   `updateProps` (Функция)
        -   `select` (enum('leading', 'trailing'))
        -   `newProps` (Object)

Пример использования:

```tsx
<FlatList
    ItemSeparatorComponent={
        Platform.OS !== 'android' &&
        (({ highlighted }) => (
            <View
                style={[
                    style.separator,
                    highlighted && { marginLeft: 0 },
                ]}
            />
        ))
    }
    data={[{ title: 'Title Text', key: 'item1' }]}
    renderItem={({ item, index, separators }) => (
        <TouchableHighlight
            key={item.key}
            onPress={() => this._onPress(item)}
            onShowUnderlay={separators.highlight}
            onHideUnderlay={separators.unhighlight}
        >
            <View style={{ backgroundColor: 'white' }}>
                <Text>{item.title}</Text>
            </View>
        </TouchableHighlight>
    )}
/>
```

### `data` (обязательно)

Массив (или список, похожий на массив) элементов для рендеринга. Другие типы данных можно использовать, обращаясь непосредственно к [`VirtualizedList`](virtualizedlist.md).

| Type      |
| --------- |
| ArrayLike |

### `ItemSeparatorComponent`

Рендерится между каждым элементом, но не сверху или снизу. По умолчанию предоставляются реквизиты `highlighted` и `leadingItem`. `renderItem` предоставляет `separators.highlight`/`unhighlight`, которые обновляют реквизит `highlighted`, но вы также можете добавить пользовательские реквизиты с помощью `separators.updateProps`. Может быть React Component (например, `SomeComponent`) или React элемент (например, `<SomeComponent />`).

| Type                         |
| ---------------------------- |
| component, function, element |

### `ListEmptyComponent`

Отображается, когда список пуст. Может быть компонентом React (например, `SomeComponent`) или элементом React (например, `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

### `ListFooterComponent`

Отображается в нижней части всех элементов. Может быть компонентом React (например, `SomeComponent`) или элементом React (например, `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

### `ListFooterComponentStyle`

Стилизация для внутреннего представления для `ListFooterComponent`.

| Type                              |
| --------------------------------- |
| [View Style](view-style-props.md) |

### `ListHeaderComponent`

Отображается в верхней части всех элементов. Может быть компонентом React (например, `SomeComponent`) или элементом React (например, `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

### `ListHeaderComponentStyle`

Стилизация для внутреннего представления для `ListHeaderComponent`.

| Type                              |
| --------------------------------- |
| [View Style](view-style-props.md) |

### `columnWrapperStyle`

Необязательный пользовательский стиль для строк с несколькими элементами, создаваемых, когда `numColumns > 1`.

| Type                              |
| --------------------------------- |
| [View Style](view-style-props.md) |

### `extraData`

Свойство-маркер для указания списку на повторный рендеринг (поскольку он реализует `PureComponent`). Если какая-либо из ваших функций `renderItem`, `Header`, `Footer` и т. д. зависит от чего-либо вне свойства `data`, поместите его сюда и обращайтесь с ним неизменно.

| Type |
| ---- |
| any  |

### `getItemLayout`

```tsx
(data, index) => {length: number, offset: number, index: number}
```

`getItemLayout` - это дополнительная оптимизация, которая позволяет пропустить измерение динамического содержимого, если вы заранее знаете размер (высоту или ширину) элементов. `getItemLayout` эффективен, если у вас есть элементы фиксированного размера, например:

```tsx
  getItemLayout={(data, index) => (
    {length: ITEM_HEIGHT, offset: ITEM_HEIGHT * index, index}
  )}
```

Добавление `getItemLayout` может значительно повысить производительность для списков из нескольких сотен элементов. Не забудьте включить длину разделителя (высоту или ширину) в расчет смещения, если вы указываете `ItemSeparatorComponent`.

| Type     |
| -------- |
| function |

### `horizontal`

Если `true`, элементы отображаются рядом друг с другом горизонтально, а не вертикально.

| Type    |
| ------- |
| boolean |

### `initialNumToRender`

Сколько элементов нужно отобразить в начальной партии. Этого должно быть достаточно, чтобы заполнить экран, но не больше. Обратите внимание, что эти элементы никогда не будут размонтированы как часть оконного рендеринга, чтобы улучшить восприятие действий прокрутки к вершине.

| Type   | Default |
| ------ | ------- |
| number | `10`    |

### `initialScrollIndex`

Вместо того чтобы начинать сверху с первого элемента, начните с `initialScrollIndex`. Это отключает оптимизацию "прокрутки к вершине", которая сохраняет первые элементы `initialNumToRender` всегда отрисованными и сразу отрисовывает элементы, начинающиеся с этого начального индекса. Требуется реализация `getItemLayout`.

| Type   |
| ------ |
| number |

### `inverted`

Изменяет направление прокрутки. Использует масштабные преобразования `-1`.

| Type    |
| ------- |
| boolean |

### `keyExtractor`

```tsx
(item: ItemT, index: number) => string;
```

Используется для извлечения уникального ключа для данного элемента по указанному индексу. Ключ используется для кэширования и в качестве ключа react для отслеживания переупорядочивания элементов. Экстрактор по умолчанию проверяет `item.key`, затем `item.id`, а затем возвращается к использованию индекса, как это делает React.

| Type     |
| -------- |
| function |

### `numColumns`

Несколько колонок могут быть отображены только с `horizontal={false}` и будут зигзагообразно перемещаться, как в макете `flexWrap`. Все элементы должны быть одинаковой высоты - кладочные макеты не поддерживаются.

| Type   |
| ------ |
| number |

### `onEndReached`

```tsx
(info: {distanceFromEnd: number}) => void;
```

Вызывается один раз, когда позиция прокрутки оказывается в пределах `onEndReachedThreshold` от отображаемого содержимого.

| Type     |
| -------- |
| function |

### `onEndReachedThreshold`

На каком расстоянии от конца (в единицах видимой длины списка) должен находиться нижний край списка от конца содержимого, чтобы сработал обратный вызов `onEndReached`. Таким образом, значение `0.5` будет вызывать `onEndReached`, когда конец содержимого находится в пределах половины видимой длины списка.

| Type   |
| ------ |
| number |

### `onRefresh`

```tsx
() => void;
```

Если указано, будет добавлен стандартный `RefreshControl` для функциональности "Pull to Refresh". Не забудьте также правильно установить параметр `refreshing`.

| Type     |
| -------- |
| function |

### `onViewableItemsChanged`

Вызывается, когда изменяется просматриваемость строк, как определено параметром `viewabilityConfig`.

| Type                                                                                                   |
| ------------------------------------------------------------------------------------------------------ |
| (callback: {changed: [ViewToken](viewtoken.md)[], viewableItems: [ViewToken](viewtoken.md)[]} => void; |

### `progressViewOffset`

Установите этот параметр, если для правильного отображения индикатора загрузки требуется смещение.

| Type   |
| ------ |
| number |

### `refreshing`

Установите значение `true` в ожидании новых данных от обновления.

| Type    |
| ------- |
| boolean |

### `removeClippedSubviews`

Это может повысить производительность прокрутки для больших списков. В Android значение по умолчанию - `true`.

!!!warning ""

    Примечание: Может иметь ошибки (отсутствующее содержимое) в некоторых обстоятельствах - используйте на свой страх и риск.

| Type    |
| ------- |
| boolean |

### `viewabilityConfig`

Тип потока и дальнейшую документацию смотрите в [`ViewabilityHelper.js`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Lists/ViewabilityHelper.js).

| Type              |
| ----------------- |
| ViewabilityConfig |

`viewabilityConfig` принимает тип `ViewabilityConfig` объект со следующими свойствами

| Property                         | Type    |
| -------------------------------- | ------- |
| minimumViewTime                  | number  |
| viewAreaCoveragePercentThreshold | number  |
| itemVisiblePercentThreshold      | number  |
| waitForInteraction               | boolean |

Требуется хотя бы одно из значений `viewAreaCoveragePercentThreshold` или `itemVisiblePercentThreshold`. Это необходимо сделать в `конструкторе`, чтобы избежать следующей ошибки ([ссылка](https://github.com/facebook/react-native/issues/17408)):

```
  Error: Changing viewabilityConfig on the fly is not supported
```

```tsx
constructor (props) {
  super(props)

  this.viewabilityConfig = {
      waitForInteraction: true,
      viewAreaCoveragePercentThreshold: 95
  }
}
```

```tsx
<FlatList
    viewabilityConfig={this.viewabilityConfig}
  ...
```

#### minimumViewTime

Минимальное количество времени (в миллисекундах), в течение которого элемент должен быть физически доступен для просмотра, прежде чем сработает обратный вызов функции просмотра. Большое число означает, что прокрутка содержимого без остановки не будет отмечена как доступная для просмотра.

#### viewAreaCoveragePercentThreshold

Процент области просмотра, который должен быть покрыт, чтобы частично закрытый элемент считался "просматриваемым", 0-100. Полностью видимые элементы всегда считаются просматриваемыми. Значение 0 означает, что один пиксель в области просмотра делает элемент просматриваемым, а значение 100 означает, что элемент должен быть либо полностью видимым, либо занимать всю область просмотра, чтобы считаться просматриваемым.

#### itemVisiblePercentThreshold

Аналогичен `viewAreaCoveragePercentThreshold`, но учитывает процент видимого элемента, а не долю области просмотра, которую он занимает.

#### waitForInteraction

Ничто не считается доступным для просмотра, пока пользователь не прокрутит страницу или не вызовет `recordInteraction` после рендеринга.

### `viewabilityConfigCallbackPairs`

Список пар `ViewabilityConfig`/`onViewableItemsChanged`. Конкретный `onViewableItemsChanged` будет вызван при выполнении условий соответствующего `ViewabilityConfig`. Смотрите `ViewabilityHelper.js` для типа потока и дальнейшей документации.

| Type                                   |
| -------------------------------------- |
| array of ViewabilityConfigCallbackPair |

## Методы

### `flashScrollIndicators()`

```tsx
flashScrollIndicators();
```

Кратковременно отображает индикаторы прокрутки.

### `getNativeScrollRef()`

```tsx
getNativeScrollRef(): React.ElementRef<typeof ScrollViewComponent>;
```

Предоставляет ссылку на базовый компонент прокрутки

### `getScrollResponder()`

```tsx
getScrollResponder(): ScrollResponderMixin;
```

Предоставляет обращение к базовому ответчику прокрутки.

### `getScrollableNode()`

```tsx
getScrollableNode(): any;
```

Предоставляет управление базовым узлом прокрутки.

### `scrollToEnd()`

```tsx
scrollToEnd(params?: {animated?: boolean});
```

Прокручивает содержимое до конца. Может быть неаккуратным без свойства `getItemLayout`.

**Параметры:**

| Name   | Type   |
| ------ | ------ |
| params | object |

Допустимыми ключами `params` являются:

-   'animated' (boolean) - Должен ли список делать анимацию при прокрутке. По умолчанию имеет значение `true`.

### `scrollToIndex()`

```tsx
scrollToIndex: (params: {
  index: number;
  animated?: boolean;
  viewOffset?: number;
  viewPosition?: number;
});
```

Прокручивается до элемента с указанным индексом таким образом, что он располагается в области просмотра так, что `viewPosition` `0` располагает его сверху, `1` - снизу, а `0.5` - посередине.

!!!warning "Примечание"

    Прокрутка в места за пределами окна рендеринга невозможна без указания свойства `getItemLayout`.

**Параметры:**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

Допустимыми ключами `params` являются:

-   `'animated'` (boolean) - Должен ли список делать анимацию при прокрутке. По умолчанию `true`.
-   `'index'` (число) - Индекс для прокрутки. Требуется.
-   `'viewOffset'` (число) - фиксированное число пикселей для смещения конечной позиции цели.
-   `'viewPosition'` (число) - При значении `0` элемент, указанный индексом, располагается сверху, `1` - снизу, а `0.5` - по центру.

### `scrollToItem()`

```tsx
scrollToItem(params: {
  animated?: ?boolean,
  item: Item,
  viewPosition?: number,
});
```

Требует линейного сканирования данных - используйте `scrollToIndex` вместо этого, если это возможно.

!!!warning "Примечание"

    Прокрутка в места за пределами окна рендеринга невозможна без указания свойства `getItemLayout`.

**Параметры:**

| Name              | Type   |
| ----------------- | ------ |
| params (Required) | object |

Допустимыми ключами `params` являются:

-   `'animated'` (boolean) - Должен ли список делать анимацию при прокрутке. По умолчанию `true`.
-   `'item'` (объект) - Элемент для прокрутки. Требуется.
-   `'viewPosition'` (число)

### `scrollToOffset()`

```tsx
scrollToOffset(params: {
  offset: number;
  animated?: boolean;
});
```

Прокрутка до определенного смещения пикселя содержимого в списке.

**Параметры:**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

Допустимыми ключами `params` являются:

-   `'offset'` (число) - Смещение для прокрутки. В случае, если `horizontal` является истиной, смещением является значение `x`, в любом другом случае смещением является значение `y`. Требуется.
-   `'animated'` (булево) - Должен ли список делать анимацию при прокрутке. По умолчанию `true`.
