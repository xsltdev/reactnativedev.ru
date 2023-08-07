---
description: Библиотека компонентов, содержащая элементы пользовательского интерфейса и вспомогательные средства, используемые в React Navigation
---

# Библиотека Elements

Библиотека компонентов, содержащая элементы пользовательского интерфейса и вспомогательные средства, используемые в React Navigation. Она может быть полезна, если вы создаете свой собственный навигатор или хотите повторно использовать стандартную функциональность в своем приложении.

## Установка

Чтобы использовать этот пакет, убедитесь, что у вас есть [`@react-navigation/native` и его зависимости (следуйте этому руководству)](getting-started.md), затем установите [`@react-navigation/elements`](https://github.com/react-navigation/react-navigation/tree/main/packages/elements):

```bash
npm install @react-navigation/elements
```

## Компоненты {#components}

### `Header`

Компонент, который может быть использован в качестве заголовка. Он принимает следующие параметры:

**`headerTitle`**

Строка или функция, возвращающая React-элемент, который будет использоваться заголовком. По умолчанию используется сцена `title`. При указании функции ей передается объект, содержащий свойства `allowFontScaling`, `tintColor`, `style` и `children`. Свойство `children` содержит строку заголовка.

**`headerTitleAlign`**

Как выровнять заголовок заголовка. Возможные значения:

-   `left`
-   `center`

По умолчанию используется значение `center` на iOS и `left` на Android.

**`headerTitleAllowFontScaling`**

Должен ли шрифт заголовка заголовка масштабироваться для соблюдения настроек доступности Text Size. По умолчанию имеет значение `false`.

**`headerLeft`**

Функция, возвращающая React-элемент для отображения в левой части заголовка. Ее можно использовать, например, для реализации пользовательской левой кнопки:

```js
<Stack.Screen
    name="Home"
    component={HomeScreen}
    options={{
        headerLeft: (props) => (
            <MyButton
                {...props}
                onPress={() => {
                    // Do something
                }}
            />
        ),
    }}
/>
```

**`headerRight`**

Функция, возвращающая React-элемент для отображения в правой части заголовка.

**`headerShadowVisible`**

Нужно ли скрывать тень подъема (Android) или нижнюю границу (iOS) заголовка.

Это сокращение для следующих стилей:

```js
{
  elevation: 0,
  shadowOpacity: 0,
  borderBottomWidth: 0,
}
```

Если в `headerStyle` наряду с `headerShadowVisible: false` указаны перечисленные выше стили, то приоритет будет иметь `headerShadowVisible: false`.

**`headerStyle`**

Объект стиля для заголовка. Здесь можно задать, например, пользовательский цвет фона.

**`headerTitleStyle`**

Объект стиля для компонента `title`

**`headerLeftContainerStyle`**

Настройте стиль для контейнера компонента `headerLeft`, например, добавьте `padding`.

**`headerRightContainerStyle`**

Настройте стиль для контейнера компонента `headerRight`, например, добавьте `padding`.

**`headerTitleContainerStyle`**

Настраивает стиль контейнера компонента `headerTitle`, например, добавляет подложку.

По умолчанию `headerTitleContainerStyle` имеет абсолютный стиль положения и смещен как `left`, так и `right`. Это может привести к появлению белого пространства или перекрытию между `headerLeft` и `headerTitle`, если используется пользовательский `headerLeft`. Это можно решить, настроив стили `left` и `right` в `headerTitleContainerStyle` и `marginHorizontal` в `headerTitleStyle`.

**`headerBackgroundContainerStyle`**

Объект стиля для контейнера элемента `headerBackground`.

**`headerTintColor`**

Цвет оттенка для заголовка

**`headerPressColor`**

Цвет для пульсации материала (только для Android >= 5.0)

**`headerPressOpacity`**

Непрозрачность кнопок в заголовке (Android < 5.0 и iOS)

**`headerTransparent`**

По умолчанию имеет значение `false`. Если `true`, то заголовок не будет иметь фона, если только вы явно не укажете его с помощью `headerBackground`. Кроме того, заголовок будет плавать по экрану так, что он будет перекрывать содержимое под ним.

Это удобно, если требуется отобразить полупрозрачный заголовок или размытый фон.

Обратите внимание, что если вы не хотите, чтобы содержимое отображалось под заголовком, вам необходимо вручную добавить верхнее поле к содержимому. React Navigation не сделает этого автоматически.

Чтобы получить высоту заголовка, можно использовать [`HeaderHeightContext`](#headerheightcontext) с помощью [React's Context API](https://reactjs.org/docs/context.html#contextconsumer) или [`useHeaderHeight`](#useheaderheight).

**`headerBackground`**

Функция, возвращающая React-элемент для отображения в качестве фона заголовка. Это полезно для использования таких фонов, как изображение или градиент.

Например, ее можно использовать вместе с `headerTransparent` для рендеринга размытого вида, чтобы создать полупрозрачный заголовок.

```js
import { BlurView } from 'expo-blur';

// ...

<Stack.Screen
    name="Home"
    component={HomeScreen}
    options={{
        headerTransparent: true,
        headerBackground: () => (
            <BlurView
                tint="light"
                intensity={100}
                style={StyleSheet.absoluteFill}
            />
        ),
    }}
/>;
```

**`headerStatusBarHeight`**

Дополнительный отступ, добавляемый в верхней части заголовка для учета полупрозрачной строки состояния. По умолчанию используется значение top из вставки безопасной области устройства. Передайте `0` или пользовательское значение, чтобы отключить поведение по умолчанию и настроить высоту.

### `HeaderBackground`

Компонент, содержащий стили, используемые для фона заголовка, такие как цвет фона и тень. По умолчанию используется для `headerBackground`. Принимает те же параметры, что и [`View`](../../rn/view.md).

Использование:

```js
<HeaderBackground style={{ backgroundColor: 'tomato' }} />
```

### `HeaderTitle`

Компонент, используемый для отображения текста заголовка в заголовке. По умолчанию используется для `headerTitle`. Он принимает те же реквизиты, что и [`Text`](../../rn/text.md).

Цвет заголовка по умолчанию равен [theme text color](themes.md). Вы можете переопределить его, передав реквизит `tintColor`.

Использование:

```js
<HeaderTitle>Hello</HeaderTitle>
```

### `HeaderBackButton`

Компонент, используемый для отображения заголовка кнопки "Назад". Является компонентом по умолчанию для `headerLeft` в [stack navigator](stack-navigator.md). Он принимает следующие реквизиты:

-   `disabled` - булево значение, определяющее, отключена ли кнопка.
-   `onPress` - Обратный вызов для вызова при нажатии кнопки.
-   `pressColor` - Цвет для пульсации материала (только для Android >= 5.0).
-   `backImage` - Функция, возвращающая React-элемент для отображения пользовательского изображения в кнопке "Назад" заголовка.
-   `tintColor` - Цвет оттенка для заголовка.
-   `label` - Текст метки для кнопки. Обычно это заголовок предыдущего экрана. По умолчанию отображается только на iOS.
-   `truncatedLabel` - Текст метки, который будет отображаться, если не хватает места для полной метки.
-   `labelVisible` - Виден ли текст метки. По умолчанию имеет значение `true` для iOS и `false` для Android.
-   `labelStyle` - объект стиля для ярлыка.
-   `allowFontScaling` - Должен ли шрифт метки масштабироваться для соблюдения настроек доступности Text Size.
-   `onLabelLayout` - Обратный вызов, срабатывающий при изменении размера метки.
-   `screenLayout` - Макет экрана.
-   `titleLayout` - Макет элемента заголовка в шапке.
-   `canGoBack` - Булево значение, указывающее, можно ли перемещаться назад по стеку.
-   `accessibilityLabel` - Метка доступности кнопки для программ чтения с экрана.
-   `testID` - ID для определения местоположения этой кнопки в тестах.
-   `style` - Объект стиля для кнопки.

Использование:

```js
<HeaderBackButton
    label="Hello"
    onPress={() => console.log('back pressed')}
/>
```

### `MissingIcon`

Компонент, отображающий символ отсутствующей иконки. Он может использоваться в качестве запасного варианта для иконок, чтобы показать, что значок отсутствует. Он принимает следующие реквизиты:

-   `color` - Цвет значка.
-   `size` - Размер значка.
-   `style` - Дополнительные стили для значка.

### `PlatformPressable`

Компонент, предоставляющий абстракцию поверх [`Pressable`](../../rn/pressable.md) для работы с различиями платформ. В дополнение к реквизитам `Pressable` он принимает следующие дополнительные реквизиты:

-   `pressColor` - Цвет пульсации материала на Android при нажатии
-   `pressOpacity` - непрозрачность при нажатии, если пульсация материала не поддерживается платформой

### `ResourceSavingView`

Компонент, позволяющий повысить производительность для неактивных экранов за счет использования [`removeClippedSubviews`](../../rn/view.md#removeclippedsubviews). Он принимает параметр `visible`, указывающий, должен ли экран быть обрезан.

Использование:

```js
<ResourceSavingView visible={0}>
    {/* Content */}
</ResourceSavingView>
```

## Утилиты {#utilities}

### `SafeAreaProviderCompat`

Обертка над компонентом `SafeAreaProvider` из [`react-native-safe-area-context](https://github.com/th3rdwave/react-native-safe-area-context), включающая начальные значения.

Использование:

```js
<SafeAreaProviderCompat>
    {/* Your components */}
</SafeAreaProviderCompat>
```

### `HeaderBackContext`

Контекст React, который может быть использован для получения обратного заголовка родительского экрана.

```js
import { HeaderBackContext } from '@react-navigation/elements';

// ...

<HeaderBackContext.Consumer>
    {(headerBack) => {
        if (headerBack) {
            const backTitle = headerBack.title;

            /* render something */
        }

        /* render something */
    }}
</HeaderBackContext.Consumer>;
```

### `HeaderShownContext`

React-контекст, который можно использовать для проверки видимости заголовка на родительском экране.

```js
import { HeaderShownContext } from '@react-navigation/elements';

// ...

<HeaderShownContext.Consumer>
    {(headerShown) => {
        /* render something */
    }}
</HeaderShownContext.Consumer>;
```

### `HeaderHeightContext`

Контекст React, который можно использовать для получения высоты ближайшего видимого заголовка на родительском экране.

```js
import { HeaderHeightContext } from '@react-navigation/elements';

// ...

<HeaderHeightContext.Consumer>
    {(headerHeight) => {
        /* render something */
    }}
</HeaderHeightContext.Consumer>;
```

### `useHeaderHeight`

Хук, возвращающий высоту ближайшего видимого заголовка на родительском экране.

```js
import { useHeaderHeight } from '@react-navigation/elements';

// ...

const headerHeight = useHeaderHeight();
```

### `getDefaultHeaderHeight`

Помощник, возвращающий высоту заголовка по умолчанию. Он принимает следующие параметры:

-   `layout` - макет экрана, т.е. объект, содержащий свойства `height` и `width`.
-   `statusBarHeight` - высота строки состояния

### `getHeaderTitle`

Помощник, возвращающий текст заголовка для использования в заголовке. Он принимает следующие параметры:

-   `options` - Объект опций экрана.
-   `fallback` - Строка заголовка, если в опциях не было найдено заголовка.

## Ссылки

-   [Elements Library](https://reactnavigation.org/docs/elements)
