---
description: useWindowDimensions автоматически обновляет все свои значения при изменении размера экрана или масштаба шрифта
---

# useWindowDimensions

```ts
import { useWindowDimensions } from 'react-native';
```

**`useWindowDimensions`** автоматически обновляет все свои значения при изменении размера экрана или масштаба шрифта. Вы можете получить ширину и высоту окна вашего приложения следующим образом:

```ts
const { height, width } = useWindowDimensions();
```

## Пример {#example}

<div data-snack-id="@bndby/usewindowdimensions-api" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

-   Хук [`useDimensions`](https://github.com/react-native-community/hooks#usedimensions) из библиотеки сообщества [React Native Hooks](https://github.com/react-native-community/hooks) призван облегчить работу с изменениями размеров экрана/окна.
-   [React Native Responsive Dimensions](https://github.com/react-native-toolkit/react-native-responsive-dimensions) также поставляется с отзывчивыми хуками.

## Свойства {#properties}

### fontScale

```ts
useWindowDimensions().fontScale;
```

Масштаб используемого в данный момент шрифта. Некоторые операционные системы позволяют пользователям масштабировать размер шрифта в большую или меньшую сторону для удобства чтения. Это свойство позволит вам узнать, что именно используется.

### height

```ts
useWindowDimensions().height;
```

Высота в пикселях окна или экрана, занимаемого вашим приложением.

### scale

```ts
useWindowDimensions().scale;
```

Соотношение пикселей устройства, на котором работает ваше приложение. Значения могут быть:

-   `1`, что указывает на то, что одна точка равна одному пикселю (обычно PPI/DPI составляет 96, 76 на некоторых платформах).
-   `2` или `3`, что указывает на дисплей Retina или высокий DPI.

### width

```ts
useWindowDimensions().width;
```

Ширина в пикселях окна или экрана, занимаемого вашим приложением.
