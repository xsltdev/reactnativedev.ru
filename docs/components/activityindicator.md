---
description: Отображает круговой индикатор загрузки
---

# ActivityIndicator

Компонент **`ActivityIndicator`** отображает круговой индикатор загрузки.

## Пример {#example}

<div data-snack-id="@bndby/activityindicator-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Пропсы {#props}

### [View Props](view.md#props)

Наследует [View Props](view.md#props).

### animating

Показывать ли индикатор (`true`) или скрывать его (`false`).

| Тип    | По-умолчанию |
| ------ | ------------ |
| `bool` | `true`       |

### color

Цвет переднего плана волчка.

| Тип                            | По-умолчанию                                                                                |
| ------------------------------ | ------------------------------------------------------------------------------------------- |
| [`color`](../guides/colors.md) | `null` (системный акцент цвет по умолчанию) :simple-android: <hr/> `'#999999'` :simple-ios: |

### hidesWhenStopped :simple-ios:

Должен ли индикатор скрываться, когда он не анимирован.

| Тип    | По-умолчанию |
| ------ | ------------ |
| `bool` | `true`       |

### size

Размер индикатора.

| Тип                                                    | По-умолчанию |
| ------------------------------------------------------ | ------------ |
| `enum('small', 'large')`<hr/>`number` :simple-android: | `'small'`    |
