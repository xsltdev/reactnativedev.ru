# ImageBackground

Частым запросом разработчиков, знакомых с вебом, является функция `background-image`. Для решения этой задачи вы можете использовать компонент **`<ImageBackground>`**, который имеет те же реквизиты, что и [`<Image>`](image.md), и добавить к нему дочерние элементы, которые вы хотите разместить поверх него.

В некоторых случаях вы можете не использовать `<ImageBackground>`, поскольку его реализация является базовой. Обратитесь к [исходному коду `<ImageBackground>`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Image/ImageBackground.js), чтобы получить больше информации, и создайте свой собственный компонент, если это необходимо.

Обратите внимание, что вы должны указать некоторые атрибуты стиля ширины и высоты.

## Пример

<div data-snack-id="@bndby/imagebackground-component" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы

### [Image Props](image.md#props)

Наследует [Image Props](image.md#props).

### `imageStyle`

| Тип                                       |
| ----------------------------------------- |
| [Стиль изображения](image-style-props.md) |

### `imageRef`

Позволяет установить ссылку на внутренний компонент `Image`.

| Тип                                                   |
| ----------------------------------------------------- |
| [Ref](https://reactjs.org/docs/refs-and-the-dom.html) |

### `style`

| Тип                               |
| --------------------------------- |
| [View Style](view-style-props.md) |
