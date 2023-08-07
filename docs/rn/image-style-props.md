---
description: Параметры стиля изображения
---

# Image Style Props

## Примеры {#examples}

### Режим изменения размера изображения

<div data-snack-id="@bndby/image-resize-modes-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Граница изображения

<div data-snack-id="@bndby/style-borderwidth-and-bordercolor-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Радиус границы изображения

<div data-snack-id="@bndby/style-border-radius-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Оттенок изображения

<div data-snack-id="@bndby/style-tintcolor-function-component" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы {#props}

### backfaceVisibility

Свойство определяет, должна ли быть видна задняя поверхность повернутого изображения.

| Тип                         | По умолчанию |
| --------------------------- | ------------ |
| `enum('visible', 'hidden')` | `'visible'`  |

### backgroundColor

| Тип                |
| ------------------ |
| [color](colors.md) |

### borderBottomLeftRadius

| Тип   |
| ----- |
| число |

### borderBottomRightRadius

| Тип   |
| ----- |
| число |

### borderColor

| Тип                |
| ------------------ |
| [color](colors.md) |

### borderRadius

| Тип   |
| ----- |
| число |

### borderTopLeftRadius

| Тип   |
| ----- |
| число |

### borderTopRightRadius

| Тип   |
| ----- |
| число |

### borderWidth

| Тип   |
| ----- |
| число |

### opacity

Устанавливает значение непрозрачности для изображения. Число должно быть в диапазоне от `0.0` до `1.0`.

| Тип   | По умолчанию |
| ----- | ------------ |
| число | `1.0`        |

### overflow

| Тип                         | По умолчанию |
| --------------------------- | ------------ |
| `enum('visible', 'hidden')` | `'visible'`  |

### overlayColor :simple-android:

Когда изображение имеет закругленные углы, указание `overlayColor` приведет к тому, что оставшееся пространство в углах будет заполнено сплошным цветом. Это полезно в случаях, которые не поддерживаются реализацией закругленных углов в Android:

-   Некоторые режимы изменения размера, такие как `содержит`.
-   Анимированные GIF-файлы

Типичным способом использования этого свойства является отображение изображений на сплошном фоне и установка `overlayColor` в тот же цвет, что и фон.

Подробнее о том, как это работает под капотом, смотрите [Документация Fresco](https://frescolib.org/docs/rounded-corners-and-circles.html).

| Тип    |
| ------ |
| строка |

### resizeMode

Определяет, как изменить размер изображения, когда рамка не соответствует размерам необработанного изображения. По умолчанию используется `cover`.

-   `cover`: Масштабировать изображение равномерно (сохраняя соотношение сторон изображения) так, чтобы:
    -   оба размера (ширина и высота) изображения были равны или больше, чем соответствующий размер представления (минус подложка).
    -   хотя бы одно измерение масштабированного изображения было равно соответствующему измерению представления (минус `padding`).
-   `contain`: Масштабировать изображение равномерно (сохраняя соотношение сторон изображения) так, чтобы оба размера (ширина и высота) изображения были равны или меньше соответствующего размера представления (минус подложка).
-   `stretch`: Масштабировать ширину и высоту независимо, это может изменить соотношение сторон `src`.
-   `repeat`: Повторять изображение, чтобы покрыть рамку представления. Изображение сохранит свой размер и соотношение сторон, если только оно не больше, чем вид, в этом случае оно будет равномерно уменьшено, чтобы оно поместилось в вид.
-   `center`: Центрирует изображение в представлении по обоим измерениям. Если изображение больше, чем вид, равномерно уменьшите его масштаб так, чтобы оно вместилось в вид.

| Тип                                                       | По умолчанию |
| --------------------------------------------------------- | ------------ |
| `enum('cover', 'contain', 'stretch', 'repeat', 'center')` | `'cover'`    |

### objectFit

Определяет, как изменить размер изображения, когда рамка не соответствует размерам необработанного изображения.

| Тип                                              | По умолчанию |
| ------------------------------------------------ | ------------ |
| `enum('cover', 'contain', 'fill', 'scale-down')` | `'cover'`    |

### tintColor

Изменяет цвет всех непрозрачных пикселей на `tintColor`.

| Тип                |
| ------------------ |
| [color](colors.md) |
