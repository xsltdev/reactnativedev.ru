# ViewToken Object Type

Объект `ViewToken` возвращается в качестве одного из свойств в обратном вызове `onViewableItemsChanged`, например, в компоненте [FlatList](flatlist.md). Он экспортируется [`ViewabilityHelper.js`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Lists/ViewabilityHelper.js).

## Пример

```js
{
  item: {key: "key-12"},
  key: "key-12",
  index: 11,
  isViewable: true
}
```

## Ключи и значения

### `index`

Уникальный числовой идентификатор, присвоенный элементу данных.

| Type   | Optional |
| ------ | -------- |
| number | Yes      |

### `isViewable`

Определяет, видна ли хотя бы часть элемента списка в области просмотра.

| Type    | Optional |
| ------- | -------- |
| boolean | No       |

### `item`

Item data

| Type | Optional |
| ---- | -------- |
| any  | No       |

### `key`

Ключевой идентификатор, присвоенный элементу данных, извлеченному на верхний уровень.

| Type   | Optional |
| ------ | -------- |
| string | No       |

### `section`

Данные секции элемента при использовании с `SectionList`.

| Type | Optional |
| ---- | -------- |
| any  | Yes      |

## Используется в

-   [`FlatList`](flatlist.md)
-   [`SectionList`](sectionlist.md)
-   [`VirtualizedList`](virtualizedlist.md)
