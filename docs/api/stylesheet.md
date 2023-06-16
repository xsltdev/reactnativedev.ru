# StyleSheet

**`StyleSheet`** - это абстракция, аналогичная таблицам стилей CSS.

<div data-snack-id="@bndby/stylesheet" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Советы по качеству кода:

-   Перенося стили из функции рендеринга, вы облегчаете понимание кода.
-   Именование стилей - хороший способ придать смысл компонентам низкого уровня в функции рендеринга.

## Методы

### `compose()`

```tsx
static compose(style1: Object, style2: Object): Object | Object[];
```

Объединяет два стиля таким образом, что `style2` отменяет все стили в `style1`. Если один из стилей является ложным, возвращается другой без выделения массива, что экономит выделение и сохраняет равенство ссылок при проверке `PureComponent`.

<div data-snack-id="@bndby/stylesheet-compose" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### `create()`

```tsx
static create(styles: Object): Object;
```

Создает ссылку стиля `StyleSheet` из заданного объекта.

### `flatten()`

```tsx
static flatten(style: Object[]): Object;
```

Сплющивает массив объектов стиля в один объединенный объект стиля. Также этот метод можно использовать для поиска идентификаторов, возвращаемых функцией `StyleSheet.register`.

!!!warning "ПРИМЕЧАНИЕ"

    Соблюдайте осторожность, так как злоупотребление этим может привести к оптимизации. Идентификаторы позволяют оптимизировать работу моста и памяти в целом. Прямое обращение к объектам стиля лишит вас этих оптимизаций.

<div data-snack-id="@bndby/stylesheet-flatten" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Этот метод использует `StyleSheetRegistry.getStyleByID(style)` для разрешения объектов стиля, представленных идентификаторами. Таким образом, массив объектов стиля (экземпляры `StyleSheet.create()`), индивидуально разрешаются в соответствующие объекты, объединяются как один и затем возвращаются. Это также объясняет альтернативное использование.

### `setStyleAttributePreprocessor()`

!!!warning "ПРЕДУПРЕЖДЕНИЕ: ЭКСПЕРИМЕНТАЛЬНО."

    Изменения будут происходить часто и не будут достоверно объявлены. Все это может быть удалено, кто знает? Используйте на свой страх и риск.

```ts
static setStyleAttributePreprocessor(
  property: string,
  process: (propValue: any) => any,
);
```

Устанавливает функцию для предварительной обработки значения свойства стиля. Эта функция используется для внутренней обработки значений цвета и трансформации. Не следует использовать эту функцию, если вы не знаете, что делаете, и не исчерпали другие возможности.

## Свойства

### `absoluteFill`

Очень распространенным шаблоном является создание оверлеев с абсолютной позицией и нулевым позиционированием (`position: 'absolute', left: 0, right: 0, top: 0, bottom: 0`), поэтому `absoluteFill` можно использовать для удобства и уменьшения дублирования этих повторяющихся стилей. Если вы хотите, absoluteFill можно использовать для создания настраиваемой записи в таблице стилей, например:

<div data-snack-id="@bndby/stylesheet-absolutefill" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### `absoluteFillObject`

Иногда вам может понадобиться `absoluteFill`, но с некоторыми изменениями - `absoluteFillObject` можно использовать для создания настраиваемой записи в `StyleSheet`, например:

<div data-snack-id="@bndby/stylesheet-absolutefillobject" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### `hairlineWidth`

Определяется как ширина тонкой линии на платформе. Она может использоваться в качестве толщины границы или разделения между двумя элементами. Пример:

<div data-snack-id="@bndby/stylesheet-hairlinewidth" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Эта константа всегда будет круглым числом пикселей (поэтому линия, определяемая ею, может выглядеть четкой) и будет стараться соответствовать стандартной ширине тонкой линии на базовой платформе. Однако не следует полагаться на то, что она будет иметь постоянный размер, поскольку на разных платформах и при разной плотности экрана ее значение может рассчитываться по-разному.

Линия шириной с волосок может быть не видна, если ваш симулятор уменьшен.
