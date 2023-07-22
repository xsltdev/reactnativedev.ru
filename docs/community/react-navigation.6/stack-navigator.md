---
description: Навигатор стеков обеспечивает переход от одного экрана к другому, при этом каждый новый экран размещается на вершине стека
---

# Stack навигатор

**Навигатор Stack** обеспечивает переход от одного экрана к другому, при этом каждый новый экран размещается на вершине стека.

По умолчанию стековый навигатор настроен на привычный для iOS и Android внешний вид: новые экраны задвигаются справа на iOS, на Android используется анимация по умолчанию ОС. Однако [анимации могут быть настроены](#animation-related-options) в соответствии с вашими потребностями.

<video playsInline autoPlay muted loop>
<source src="/community/react-navigation.6/stack.mov" />
</video>

Следует помнить, что, хотя `@react-navigation/stack` является чрезвычайно настраиваемым, он реализован на JavaScript. Хотя анимация и жесты выполняются нативно, производительность может быть не такой высокой, как у нативной реализации. Для многих приложений это не является проблемой, но если вы испытываете проблемы с производительностью во время навигации, рассмотрите возможность использования вместо него [`@react-navigation/native-stack`](native-stack-navigator.md), который использует нативные навигационные примитивы.

## Установка

Чтобы использовать этот навигатор, убедитесь, что у вас есть [`@react-navigation/native` и его зависимости (следуйте этому руководству)](getting-started.md), затем установите [`@react-navigation/stack`](https://github.com/react-navigation/react-navigation/tree/main/packages/stack):

```bash
npm install @react-navigation/stack
```

Затем необходимо установить и настроить библиотеки, необходимые для работы навигатора стека:

1.  Сначала установите [`react-native-gesture-handler`](https://docs.swmansion.com/react-native-gesture-handler/).

    Если у вас есть проект, управляемый Expo, то в каталоге проекта выполните команду:

    ```bash
    npx expo install react-native-gesture-handler
    ```

    Если у вас есть "голый" проект React Native, в каталоге проекта выполните команду:

    ```bash
    npm install react-native-gesture-handler
    ```

2.  Чтобы завершить установку `react-native-gesture-handler`, добавьте следующее в **top** (убедитесь, что он находится в самом верху и перед ним нет ничего другого) вашего входного файла, например `index.js` или `App.js`:

    ```js
    import 'react-native-gesture-handler';
    ```

    !!!note "Примечание"

        Если вы создаете приложение для Android или iOS, не пропускайте этот шаг, иначе оно может упасть в производстве, даже если в разработке оно работает нормально. Это не относится к другим платформам.

3.  Дополнительно можно установить [`@react-native-masked-view/masked-view`](https://github.com/react-native-masked-view/masked-view). Это необходимо, если вы хотите использовать анимацию в стиле UIKit для заголовка ([`HeaderStyleInterpolators.forUIKit`](#headerstyleinterpolators)).

    Если у вас есть проект, управляемый Expo, в каталоге проекта выполните команду:

    ```bash
    npx expo install @react-native-masked-view/masked-view
    ```

    Если у вас есть "голый" проект React Native, в каталоге проекта выполните команду:

    ```bash
    npm install @react-native-masked-view/masked-view
    ```

4.  Если вы работаете на Mac и разрабатываете под iOS, то для завершения связывания необходимо также установить pods (через [Cocoapods](https://cocoapods.org/)).

    ```bash
    npx pod-install ios
    ```

## Определение API

Чтобы использовать этот навигатор, импортируйте его из `@react-navigation/stack`:

```js
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function MyStack() {
    return (
        <Stack.Navigator>
            <Stack.Screen name="Home" component={Home} />
            <Stack.Screen
                name="Notifications"
                component={Notifications}
            />
            <Stack.Screen
                name="Profile"
                component={Profile}
            />
            <Stack.Screen
                name="Settings"
                component={Settings}
            />
        </Stack.Navigator>
    );
}
```

### Props

Компонент `Stack.Navigator` принимает следующие параметры:

#### `id`

Необязательный уникальный идентификатор навигатора. Он может быть использован с помощью [`navigation.getParent`](navigation-prop.md#getparent) для ссылки на этот навигатор в дочернем навигаторе.

#### `initialRouteName`

Имя маршрута, которое должно отображаться при первой загрузке навигатора.

#### `screenOptions`

Параметры по умолчанию, используемые для экранов в навигаторе.

#### `detachInactiveScreens`

Булево значение, используемое для указания того, следует ли отделять неактивные экраны от иерархии представлений для экономии памяти. Это обеспечивает интеграцию с [react-native-screens](https://github.com/software-mansion/react-native-screens). По умолчанию имеет значение `true`.

Если необходимо отключить эту оптимизацию для конкретных экранов (например, чтобы экран оставался в поле зрения даже при расфокусировке), можно воспользоваться опцией [`detachPreviousScreen`](#detachpreviousscreen).

#### `keyboardHandlingEnabled`

Если `false`, то клавиатура НЕ будет автоматически отключаться при переходе на новый экран с этого экрана. По умолчанию установлено значение `true`.

### Параметры {#options}

Для настройки экранов в навигаторе можно использовать следующие [options](screen-options.md). Они могут быть указаны в свойствах `screenOptions` свойства `Stack.navigator` или `options` свойства `Stack.Screen`.

#### `title`

Строка, которая может быть использована в качестве запасного варианта для `headerTitle`.

#### `cardShadowEnabled`

Используйте этот параметр для получения видимых теней во время переходов. По умолчанию имеет значение `true`.

#### `cardOverlayEnabled`

Используйте этот параметр, чтобы во время переходов под картой была видна полупрозрачная темная накладка. По умолчанию имеет значение `true` для Android и `false` для iOS.

#### `cardOverlay`

Функция возвращает React-элемент для отображения в качестве наложения на карточку. При использовании этой функции обязательно установите значение `cardOverlayEnabled` в `true`.

#### `cardStyle`

Объект стиля для карточки в стопке. Здесь можно указать пользовательский цвет фона, который будет использоваться вместо фона по умолчанию.

Также можно указать `{ backgroundColor: 'transparent' }`, чтобы под ним был виден предыдущий экран (для прозрачных модалов). Это полезно для реализации таких вещей, как модальные диалоги. При использовании прозрачного фона следует также указать в опциях `presentation: 'modal'`, чтобы предыдущие экраны не отделялись и оставались видимыми под ними.

В Web высота экрана не ограничивается высотой области просмотра. Это сделано специально для того, чтобы адресная строка браузера могла скрываться при прокрутке. Если такое поведение нежелательно, можно установить `cardStyle` в значение `{ flex: 1 }`, чтобы заставить экран заполнять область просмотра.

#### `presentation`

Это опция быстрого доступа, которая позволяет настроить несколько параметров для конфигурирования стиля рендеринга и переходов:

-   `card`: Использовать анимацию ОС по умолчанию для переходов между экранами iOS и Android.
-   `modal`: Использовать модальные анимации. Это изменяет несколько параметров:
    -   Устанавливает `headerMode` в `screen` для экрана, если не указано иное.
    -   Изменяет анимацию экрана в соответствии с поведением платформы для модалов.
-   `transparentModal`: Аналогичен `modal`. При этом изменяется следующее:
    -   Устанавливает для экрана `headerMode` в `screen`, если не указано иное.
    -   Устанавливает цвет фона экрана прозрачным, чтобы предыдущий экран был виден
    -   Настраивает опцию `detachPreviousScreen` таким образом, чтобы предыдущий экран оставался отрисованным.
    -   Предотвращает анимацию предыдущего экрана из его последней позиции.
    -   Изменяет анимацию экрана на вертикальную слайд-анимацию.

Подробнее о настройке `transparentModal` см. в разделе [Прозрачные модалы](#transparent-modals).

#### `animationEnabled`

Должна ли быть включена анимация перехода на экране. Если установить значение `false`, экран не будет анимироваться при нажатии или отталкивании. По умолчанию имеет значение `true` для iOS и Android, `false` для Web.

#### `animationTypeForReplace`

Тип анимации, который будет использоваться, когда этот экран заменяет другой экран. Он принимает следующие значения:

-   `push` - используется анимация выталкивания нового экрана.
-   `pop` - используется анимация вываливания экрана.

По умолчанию используется `push`.

При использовании `pop` к заменяемому экрану применяется анимация `pop`.

#### `gestureEnabled`

Можно ли использовать жесты для закрытия этого экрана. По умолчанию имеет значение `true` для iOS, `false` для Android.

Жесты не поддерживаются в Web.

#### `gestureResponseDistance`

Число, переопределяющее расстояние начала касания от края экрана для распознавания жестов.

Оно будет задавать горизонтальное или вертикальное расстояние в зависимости от значения [`gestureDirection`](#gesturedirection).

По умолчанию используются следующие значения:

-   `50` - когда `gestureDirection` имеет значение `horizontal` или `horizontal-inverted`.
-   `135` - если `gestureDirection` имеет значение `vertical` или `vertical-inverted`.

Это не поддерживается в Web.

#### `gestureVelocityImpact`

Число, определяющее значимость скорости для жеста. По умолчанию принимается значение `0.3`.

Это значение не поддерживается в Web.

#### `gestureDirection`

Направление жестов. Подробности см. в разделе [Анимации](#animations).

В Web эта функция не поддерживается.

#### `transitionSpec`

Объект конфигурации для экранного перехода. Подробности см. в разделе [Анимации](#animations).

#### `cardStyleInterpolator`

Интерполированные стили для различных частей карты. Подробности см. в разделе [Анимации](#animations).

#### `headerStyleInterpolator`

Интерполированные стили для различных частей заголовка. Подробности см. в разделе [Анимации](#animations).

#### `detachPreviousScreen`

Булево значение, указывающее, следует ли отсоединять предыдущий экран от иерархии просмотра для экономии памяти. Установите значение `false`, если необходимо, чтобы предыдущий экран был виден через активный экран. Применяется только в том случае, если `detachInactiveScreens` не установлено в `false`.

Этот параметр автоматически настраивается при использовании [`presentation`](#presentation) в качестве `transparentModal` или `modal` для сохранения видимости необходимых экранов. В остальных случаях значение по умолчанию равно `true`.

#### `freezeOnBlur`

Булево значение, указывающее, следует ли запретить повторное отображение неактивных экранов. По умолчанию имеет значение `false`.

По умолчанию принимает значение `true`, если `enableFreeze()` из пакета `react-native-screens` запущен в верхней части приложения.

Требуется версия `react-native-screens` >=3.16.0.

Поддерживается только на iOS и Android.

### Опции, связанные с заголовками

Список опций, связанных с заголовками, можно найти [здесь](elements.md#header). Эти [опции](screen-options.md) могут быть указаны в свойствах `screenOptions` в `Stack.navigator` или `options` в `Stack.Screen`. Для использования этих опций не обязательно использовать непосредственно `@react-navigation/elements`, они просто документированы на этой странице.

Кроме них, в стеке также поддерживаются следующие опции:

#### `header`

Пользовательский заголовок, используемый вместо заголовка по умолчанию.

Функция принимает функцию, возвращающую React-элемент для отображения в качестве заголовка. В качестве аргумента функция получает объект, содержащий следующие свойства:

-   `navigation` - Объект навигации для текущего экрана.
-   `route` - Объект маршрута для текущего экрана.
-   `options` - Опции для текущего экрана.
-   `layout` - Размеры экрана, содержит свойства `height` и `width`.
-   `progress` - Анимированные узлы, отображающие ход анимации.
-   `back` - Опции для кнопки "Назад", содержит объект со свойством `title` для использования в качестве метки кнопки "Назад".
-   `styleInterpolator` - Функция, возвращающая интерполированные стили для различных элементов заголовка.

При использовании пользовательского заголовка обязательно установите `headerMode` в `screen` (подробнее см. ниже).

Пример:

```js
import { getHeaderTitle } from '@react-navigation/elements';

// ..

header: ({ navigation, route, options, back }) => {
    const title = getHeaderTitle(options, route.name);

    return (
        <MyHeader
            title={title}
            leftButton={
                back ? (
                    <MyBackButton
                        onPress={navigation.goBack}
                    />
                ) : undefined
            }
            style={options.headerStyle}
        />
    );
};
```

Чтобы задать пользовательский заголовок для всех экранов навигатора, можно указать эту опцию в свойстве `screenOptions` навигатора.

При использовании пользовательского заголовка следует помнить о двух моментах:

##### Указывайте "height" в `headerStyle` во избежание глюков.

Если высота вашего заголовка отличается от высоты заголовка по умолчанию, то возможны глюки, связанные с тем, что измерение происходит несинхронно. Явное указание высоты позволит избежать таких глюков.

Пример:

```js
headerStyle: {
  height: 80, // Specify the height of your custom header
};
```

Обратите внимание, что по умолчанию этот стиль не применяется к заголовку, поскольку вы сами управляете стилем вашего пользовательского заголовка. Если вы хотите применить этот стиль и к заголовку, используйте `headerStyle` из реквизита.

##### Установите `headerMode` в `float` для анимации пользовательских заголовков.

По умолчанию существует один плавающий заголовок, который отображает заголовки для нескольких экранов на iOS для немодальных устройств. Эти заголовки включают анимацию для плавного перехода друг к другу.

Если вы укажете пользовательский заголовок, React Navigation автоматически изменит его на `screen`, чтобы заголовок анимировался вместе с экраном. Это означает, что вам не нужно реализовывать анимацию для его отдельного анимирования.

Однако вы можете захотеть сохранить плавающий заголовок, чтобы иметь другую анимацию перехода между заголовками. Для этого в опциях нужно указать `headerMode: 'float'`, а затем интерполировать на реквизиты `progress.current` и `progress.next` в вашем пользовательском заголовке. Например, в следующем примере заголовок будет перекрестно затухать:

```js
const opacity = Animated.add(
    progress.current,
    progress.next || 0
).interpolate({
    inputRange: [0, 1, 2],
    outputRange: [0, 1, 0],
});

return (
    <Animated.View style={{ opacity }}>
        {/* Header content */}
    </Animated.View>
);
```

#### `headerMode`

Определяет способ отображения заголовка:

-   `float` - отображение одного заголовка, который остается вверху и анимируется при смене экранов. Этот способ используется по умолчанию в iOS.
-   `screen` - к каждому экрану прикрепляется заголовок, и он исчезает вместе с экраном. Этот вариант используется по умолчанию на других платформах.

#### `headerShown`

Показывать или скрывать заголовок экрана. По умолчанию заголовок отображается. Установка этого значения в `false` скрывает заголовок.

#### `headerBackAllowFontScaling`

Должен ли шрифт заголовка кнопки "Назад" масштабироваться с учетом настроек доступности Text Size. По умолчанию имеет значение `false`.

#### `headerBackAccessibilityLabel`

Метка доступности для кнопки возврата в заголовок.

#### `headerBackImage`

Функция, возвращающая React-элемент для отображения пользовательского изображения в кнопке "Назад" заголовка. При использовании функции она получает в качестве аргумента объект `tintColor`. По умолчанию используется компонент Image с источником изображения кнопки "Назад", который представляет собой изображение иконки кнопки "Назад", используемое по умолчанию для данной платформы (шеврон на iOS и стрелка на Android).

#### `headerBackTitle`

Строка заголовка, используемая кнопкой "Назад" на iOS. По умолчанию используется `headerTitle` предыдущей сцены.

#### `headerBackTitleVisible`

По умолчанию задается разумное значение того, должен ли заголовок кнопки "Назад" быть видимым или нет, но если вы хотите переопределить его, то можете использовать `true` или `false` в этой опции.

#### `headerTruncatedBackTitle`

Строка заголовка, используемая кнопкой "Назад", когда `headerBackTitle` не помещается на экране. По умолчанию `"Назад"`.

#### `headerBackTitleStyle`

Объект стиля для заднего заголовка.

### Events

Навигатор может [выдавать события](navigation-events.md) на определенные действия. Поддерживаются следующие события:

#### `transitionStart`

Это событие вызывается, когда начинается анимация перехода для текущего экрана.

Данные события:

-   `e.data.closing` - Булево значение, указывающее, открыт или закрыт экран.

Пример:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'transitionStart',
        (e) => {
            // Do something
        }
    );

    return unsubscribe;
}, [navigation]);
```

#### `transitionEnd`

Это событие вызывается, когда заканчивается анимация перехода для текущего экрана.

Данные события:

-   `e.data.closing` - булево значение, указывающее, был ли экран открыт или закрыт.

Пример:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'transitionEnd',
        (e) => {
            // Do something
        }
    );

    return unsubscribe;
}, [navigation]);
```

#### `gestureStart`

Это событие вызывается, когда для текущего экрана начинается выполнение жеста пролистывания.

Пример:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'gestureStart',
        (e) => {
            // Do something
        }
    );

    return unsubscribe;
}, [navigation]);
```

#### `gestureEnd`

Это событие вызывается, когда жест пролистывания заканчивается для текущего экрана. Например, экран был успешно закрыт.

Пример:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'gestureEnd',
        (e) => {
            // Do something
        }
    );

    return unsubscribe;
}, [navigation]);
```

#### `gestureCancel`

Это событие вызывается, когда жест пролистывания отменен для текущего экрана. Например, экран не был отменен жестом.

Пример:

```js
React.useEffect(() => {
    const unsubscribe = navigation.addListener(
        'gestureCancel',
        (e) => {
            // Do something
        }
    );

    return unsubscribe;
}, [navigation]);
```

### Хелперы {#helpers}

Навигатор стека добавляет в навигационный реквизит следующие методы:

#### `replace`

Заменяет текущий экран на новый экран в стеке. Метод принимает следующие аргументы:

-   `name` - _string_ - Имя маршрута, который необходимо поместить в стек.
-   `params` - _object_ - Параметры экрана для передачи маршруту назначения.

```js
navigation.replace('Profile', { owner: 'Michaś' });
```

#### `push`

Помещает новый экран на вершину стека и осуществляет переход к нему. Метод принимает следующие аргументы:

-   `name` - _string_ - Имя маршрута, который необходимо поместить в стек.
-   `params` - _object_ - Параметры экрана для передачи маршруту назначения.

```js
navigation.push('Profile', { owner: 'Michaś' });
```

#### `pop`

Выводит текущий экран из стека и осуществляет переход к предыдущему экрану. Принимает один необязательный аргумент (`count`), который позволяет указать, на сколько экранов нужно вернуться назад.

```js
navigation.pop();
```

#### `popToTop`

Вызывает все экраны в стопке, кроме первого, и осуществляет переход к нему.

```js
navigation.popToTop();
```

## Пример {#example}

```js
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function MyStack() {
    return (
        <Stack.Navigator
            initialRouteName="Home"
            screenOptions={{
                headerMode: 'screen',
                headerTintColor: 'white',
                headerStyle: { backgroundColor: 'tomato' },
            }}
        >
            <Stack.Screen
                name="Home"
                component={Home}
                options={{
                    title: 'Awesome app',
                }}
            />
            <Stack.Screen
                name="Profile"
                component={Profile}
                options={{
                    title: 'My profile',
                }}
            />
            <Stack.Screen
                name="Settings"
                component={Settings}
                options={{
                    gestureEnabled: false,
                }}
            />
        </Stack.Navigator>
    );
}
```

## Анимации

### Опции, связанные с анимацией

Stack Navigator предоставляет различные опции для настройки анимации перехода при добавлении или удалении экрана. Эти анимации перехода могут быть настроены для каждого экрана путем указания опций в свойстве `options` для каждого экрана.

-   `gestureDirection` - направление жестов пролистывания:

    -   `horizontal` - жест закрытия экрана будет начинаться слева, а в RTL - справа. Для анимации экран будет сдвигаться вправо с помощью `SlideFromRightIOS`, а в RTL - влево.
    -   `horizontal-inverted` - Жест закрытия экрана будет начинаться справа, а в RTL - слева. Для анимации экран будет сдвигаться слева при `SlideFromRightIOS`, и справа в RTL, так как направление инвертировано.
    -   `vertical` - жест закрытия экрана будет начинаться сверху. Для анимации экран будет сдвигаться снизу.
    -   `vertical-inverted` - Жест закрытия экрана будет начинаться снизу. Для анимации экран будет сдвигаться сверху.

    Вместе с `gestureDirection` можно указать и соответствующую горизонтальную/вертикальную анимацию. Для анимаций, включенных в библиотеку, если задать для `gestureDirection` одно из инвертированных значений, то это также перевернет направление анимации.

-   `transitionSpec` - объект, определяющий тип анимации (`timing` или `pring`) и их параметры (например, `duration` для `timing`). Он принимает 2 свойства:

    -   `open` - Конфигурация перехода при добавлении экрана
    -   `close` - конфигурация перехода при удалении экрана.

    Каждый из объектов должен задавать 2 свойства:

    -   `animation` - Функция анимации, которую следует использовать для анимации. Поддерживаются следующие значения: `timing` и `pring`.
    -   `config` - объект конфигурации для функции синхронизации. Для `timing` это может быть `duration` и `easing`. Для `spring` это могут быть `stiffness`, `damping`, `mass`, `overshootClamping`, `restDisplacementThreshold` и `restSpeedThreshold`.

    Конфигурация, использующая пружинную анимацию, выглядит следующим образом:

    ```js
    const config = {
        animation: 'spring',
        config: {
            stiffness: 1000,
            damping: 500,
            mass: 3,
            overshootClamping: true,
            restDisplacementThreshold: 0.01,
            restSpeedThreshold: 0.01,
        },
    };
    ```

    Мы можем передать эту конфигурацию в опции `transitionSpec`:

    ```js
    <Stack.Screen
        name="Profile"
        component={Profile}
        options={{
            transitionSpec: {
                open: config,
                close: config,
            },
        }}
    />
    ```

-   `cardStyleInterpolator` - Это функция, задающая интерполированные стили для различных частей карты. Это позволяет настраивать переходы при переходе от экрана к экрану. Ожидается, что она вернет как минимум пустой объект, возможно, содержащий интерполированные стили для контейнера, самой карты, наложения и тени. Поддерживаются следующие свойства:

    -   `containerStyle` - Стиль для представления контейнера, обертывающего карточку.
    -   `cardStyle` - стиль представления, представляющего карточку.
    -   `overlayStyle` - стиль вида, представляющего полупрозрачный оверлей, расположенный ниже
    -   `shadowStyle` - Стиль представления, представляющего тень карты.

    В качестве аргумента функция получает следующие свойства:

    -   `current` - Значения для текущего экрана:
        -   `progress` - Анимированный узел, представляющий значение прогресса на текущем экране.
    -   `next` - Значения для экрана, следующего за этим в стеке. Это значение может быть `неопределенным` в случае, если анимируемый экран является последним.
        -   `progress` - Анимированный узел, представляющий значение прогресса для следующего экрана.
    -   `index` - Индекс карточки в стопке.
    -   `closing` - Анимированный узел, показывающий, закрывается ли карточка. `1` - если закрывается, `0` - если нет.
    -   `layouts` - Размеры макетов для различных элементов, используемых для анимации.
        -   `screen` - Макет всего экрана. Содержит свойства `height` и `width`.

    !!!tip ""

        Обратите внимание, что если экран не последний, то он будет использовать конфигурацию перехода следующего экрана. Это связано с тем, что многие переходы включают анимацию предыдущего экрана, и поэтому эти два перехода должны быть вместе, чтобы не запускать два разных вида переходов на двух экранах (например, слайд и модал). Чтобы узнать, нужно ли анимировать предыдущий экран, можно воспользоваться параметром `next`. Подробнее об этом параметре см. в разделе [Анимация](stack-navigator.md#animations).

    Конфигурация, которая просто затухает на экране, выглядит следующим образом:

    ```js
    const forFade = ({ current }) => ({
        cardStyle: {
            opacity: current.progress,
        },
    });
    ```

    Мы можем передать эту функцию в опции `cardStyleInterpolator`:

    ```js
    <Stack.Screen
        name="Profile"
        component={Profile}
        options={{ cardStyleInterpolator: forFade }}
    />
    ```

    Интерполятор будет вызываться для каждого экрана. Например, в стеке имеется два экрана, A и B. B - это новый экран, попадающий в фокус, а A - предыдущий экран. Интерполятор будет вызываться для каждого экрана:

    -   Интерполятор вызывается для `B`: Здесь значение `current.progress` представляет собой прогресс перехода, который начнется в `0` и закончится в `1`. Следующего значения `next.progress` не будет, поскольку `B` является последним экраном.
    -   Интерполятор вызывается для `A`: Здесь `current.progress` останется на значении `1` и не изменится, так как текущий переход выполняется для `B`, а не для `A`. Значение `next.progress` представляет собой прогресс для `B` и начнется с `0` и закончится на `1`.

    Допустим, мы хотим анимировать оба экрана во время перехода. Самый простой способ сделать это - объединить значения прогресса текущего и следующего экранов:

    ```js
    const progress = Animated.add(
        current.progress.interpolate({
            inputRange: [0, 1],
            outputRange: [0, 1],
            extrapolate: 'clamp',
        }),
        next
            ? next.progress.interpolate({
                  inputRange: [0, 1],
                  outputRange: [0, 1],
                  extrapolate: 'clamp',
              })
            : 0
    );
    ```

    Здесь на экране `A` будут присутствовать и `current.progress`, и `next.progress`, и поскольку `current.progress` остается на `1`, а `next.progress` меняется, то в совокупности прогресс будет меняться с `1` на `2`. На экране `B` будет только `current.progress`, который изменится с `0` на `1`. Таким образом, мы можем применить различные интерполяции для `0-1` и `1-2` для анимации сфокусированного и несфокусированного экрана соответственно.

    Конфигурация, которая переводит предыдущий экран немного влево, а текущий экран переводит от правого края, будет выглядеть следующим образом:

    ```js
    const forSlide = ({
        current,
        next,
        inverted,
        layouts: { screen },
    }) => {
        const progress = Animated.add(
            current.progress.interpolate({
                inputRange: [0, 1],
                outputRange: [0, 1],
                extrapolate: 'clamp',
            }),
            next
                ? next.progress.interpolate({
                      inputRange: [0, 1],
                      outputRange: [0, 1],
                      extrapolate: 'clamp',
                  })
                : 0
        );

        return {
            cardStyle: {
                transform: [
                    {
                        translateX: Animated.multiply(
                            progress.interpolate({
                                inputRange: [0, 1, 2],
                                outputRange: [
                                    // Focused, but offscreen in the beginning
                                    screen.width,
                                    0, // Fully focused
                                    screen.width * -0.3, // Fully unfocused
                                ],
                                extrapolate: 'clamp',
                            }),
                            inverted
                        ),
                    },
                ],
            },
        };
    };
    ```

-   `headerStyleInterpolator` - Это функция, задающая интерполированные стили для различных частей заголовка. Ожидается, что она вернет как минимум пустой объект, возможно, содержащий интерполированные стили для левого ярлыка и кнопки, правой кнопки, заголовка и фона. Поддерживаются следующие свойства:

    -   `leftLabelStyle` - Стиль для метки левой кнопки (метка кнопки назад).
    -   `leftButtonStyle` - Стиль для левой кнопки (обычно это кнопка "Назад").
    -   `rightButtonStyle` - Стиль для правой кнопки.
    -   `titleStyle` - Стиль текста заголовка.
    -   `backgroundStyle` - Стиль фона заголовка.

    В качестве аргумента функция получает следующие свойства:

    -   `current` - Значения для текущего экрана (экрана, которому принадлежит данный заголовок).
        -   `progress` - Анимированный узел, представляющий значение прогресса для текущего экрана. `0` - когда экран должен начать появляться в поле зрения, `0.5` - когда он находится на середине пути, `1` - когда он должен быть полностью в поле зрения.
    -   `next` - Значение для экрана, следующего за этим в стеке. Это значение может быть `undefined` в случае, если анимируемый экран является последним.
        -   `progress` - Анимированный узел, представляющий значение прогресса для следующего экрана.
    -   `layouts` - Размеры макетов для различных элементов, используемых для анимации. Каждый объект макета содержит свойства `height` и `width`.
        -   `screen` - Макет всего экрана.
        -   `title` - Макет элемента заголовка. Может быть `неопределенным`, если заголовок не отрисовывается.
        -   `leftLabel` - Макет метки кнопки "Назад". Может быть `неопределенным`, если не отрисовывается ярлык кнопки "Назад".

    Конфигурация, в которой элементы просто затухают, выглядит следующим образом:

    ```js
    const forFade = ({ current, next }) => {
        const opacity = Animated.add(
            current.progress,
            next ? next.progress : 0
        ).interpolate({
            inputRange: [0, 1, 2],
            outputRange: [0, 1, 0],
        });

        return {
            leftButtonStyle: { opacity },
            rightButtonStyle: { opacity },
            titleStyle: { opacity },
            backgroundStyle: { opacity },
        };
    };
    ```

    Мы можем передать эту функцию в опции `headerStyleInterpolator`:

    ```js
    <Stack.Screen
        name="Profile"
        component={Profile}
        options={{ headerStyleInterpolator: forFade }}
    />
    ```

### Готовые конфигурации

С помощью этих опций можно создавать собственные анимации перехода для экранов. Мы также экспортируем различные конфигурации из библиотеки с готовыми анимациями, которые вы можете использовать:

#### `TransitionSpecs`

-   `TransitionIOSSpec` - Точные значения из конфигурации анимации UINavigationController.
-   `FadeInFromBottomAndroidSpec` - Конфигурация анимации открытия активности из Android Nougat.
-   `FadeOutToBottomAndroidSpec` - Конфигурация для анимации закрытия активности из Android Nougat.
-   `RevealFromBottomAndroidSpec` - Примерная конфигурация анимации открытия активности из Android Pie.

Пример:

```js
import { TransitionSpecs } from '@react-navigation/stack';

// ...

<Stack.Screen
    name="Profile"
    component={Profile}
    options={{
        transitionSpec: {
            open: TransitionSpecs.TransitionIOSSpec,
            close: TransitionSpecs.TransitionIOSSpec,
        },
    }}
/>;
```

#### `CardStyleInterpolators`

-   `forHorizontalIOS` - Стандартный заход в iOS-стиле справа.
-   `forVerticalIOS` - Стандартный заход снизу в стиле iOS (используется для модалов).
-   `forModalPresentationIOS` - Стандартная модальная анимация в стиле iOS в iOS 13.
-   `forFadeFromBottomAndroid` - Стандартное затухание снизу в стиле Android для Android Oreo.
-   `forRevealFromBottomAndroid` - Стандартное раскрытие снизу в стиле Android для Android Pie.

Пример настройки вертикальной анимации затухания экрана в стиле Android Oreo:

```js
import { CardStyleInterpolators } from '@react-navigation/stack';

// ...

<Stack.Screen
    name="Profile"
    component={Profile}
    options={{
        title: 'Profile',
        cardStyleInterpolator:
            CardStyleInterpolators.forFadeFromBottomAndroid,
    }}
/>;
```

#### `HeaderStyleInterpolators`

-   `forUIKit` - Стандартная анимация в стиле UIKit для заголовка, где заголовок перетекает в метку кнопки "Назад".
-   `forFade` - Простая анимация затухания для элементов заголовка.
-   `forStatic` - Простая анимация перевода для перевода заголовка вместе с раздвижным экраном.

Пример настройки iOS-анимации по умолчанию для элементов заголовка, где заголовок переходит в кнопку "Назад":

```js
import { HeaderStyleInterpolators } from '@react-navigation/stack';

// ...

<Stack.Screen
    name="Profile"
    component={Profile}
    options={{
        title: 'Profile',
        headerStyleInterpolator:
            HeaderStyleInterpolators.forUIKit,
    }}
/>;
```

!!!note ""

    Чтобы ссылки не менялись при повторном рендеринге, всегда задавайте конфигурацию анимации на верхнем уровне файла. Это важно для плавной и надежной анимации переходов.

#### `TransitionPresets`

Мы экспортируем различные пресеты переходов, которые объединяют различные наборы этих опций для соответствия определенным собственным анимациям. Пресет перехода - это объект, содержащий несколько связанных с анимацией экранных опций, экспортируемых в папку `TransitionPresets`. В настоящее время доступны следующие пресеты:

-   `SlideFromRightIOS` - стандартный навигационный переход iOS.
-   `ModalSlideFromBottomIOS` - стандартный навигационный переход iOS для модалов.
-   `ModalPresentationIOS` - стандартный стиль представления модалов в iOS (появился в iOS 13).
-   `FadeFromBottomAndroid` - Стандартный навигационный переход Android при открытии или закрытии Activity на Android < 9 (Oreo).
-   `RevealFromBottomAndroid` - стандартный навигационный переход Android при открытии или закрытии Activity на Android 9 (Pie).
-   `ScaleFromCenterAndroid` - стандартный навигационный переход Android при открытии или закрытии Активности на Android >= 10.
-   `DefaultTransition` - Переход навигации по умолчанию для текущей платформы.
-   `ModalTransition` - Модальный переход по умолчанию для текущей платформы.

Эти предустановки можно распространить в `options`, чтобы настроить анимацию для экрана:

```js
import { TransitionPresets } from '@react-navigation/stack';

// ...

<Stack.Screen
    name="Profile"
    component={Profile}
    options={{
        title: 'Profile',
        ...TransitionPresets.ModalSlideFromBottomIOS,
    }}
/>;
```

Если необходимо настроить анимацию перехода для всех экранов навигатора, то это можно указать в свойстве `screenOptions` для навигатора.

Пример конфигурации для модального стиля представления iOS:

```js
import { TransitionPresets } from '@react-navigation/stack';

// ...

<Stack.Navigator
    initialRouteName="Home"
    screenOptions={({ route, navigation }) => ({
        headerShown: false,
        gestureEnabled: true,
        ...TransitionPresets.ModalPresentationIOS,
    })}
>
    <Stack.Screen name="Home" component={Home} />
    <Stack.Screen name="Profile" component={Profile} />
</Stack.Navigator>;
```

### Прозрачные модалы

Прозрачный модал - это модальный диалог, который перекрывает экран. При этом предыдущий экран остается видимым. Чтобы получить прозрачный модальный экран, в опциях экрана можно указать `presentation: 'transparentModal'`.

Пример:

```js
<Stack.Navigator>
    <Stack.Screen name="Home" component={HomeStack} />
    <Stack.Screen
        name="Modal"
        component={ModalScreen}
        options={{ presentation: 'transparentModal' }}
    />
</Stack.Navigator>
```

Теперь при переходе к экрану `Modal` он будет иметь прозрачный фон, а под ним будет виден экран `Home`.

В дополнение к `presentation` можно дополнительно указать еще несколько параметров, чтобы добиться поведения, похожего на модальный диалог:

-   Отключить заголовок с помощью `headerShown: false`.
-   Включить оверлей с помощью `cardOverlayEnabled: true` (при этом нельзя нажать на оверлей, чтобы закрыть экран, альтернативные варианты см. ниже).

Если вы хотите дополнительно настроить анимацию диалога, закрыть экран при нажатии на оверлей и т.д., то можно использовать хук `useCardAnimation` для настройки элементов внутри экрана.

Пример:

```js
import {
    Animated,
    View,
    Text,
    Pressable,
    Button,
    StyleSheet,
} from 'react-native';
import { useTheme } from '@react-navigation/native';
import { useCardAnimation } from '@react-navigation/stack';

function ModalScreen({ navigation }) {
    const { colors } = useTheme();
    const { current } = useCardAnimation();

    return (
        <View
            style={{
                flex: 1,
                alignItems: 'center',
                justifyContent: 'center',
            }}
        >
            <Pressable
                style={[
                    StyleSheet.absoluteFill,
                    {
                        backgroundColor:
                            'rgba(0, 0, 0, 0.5)',
                    },
                ]}
                onPress={navigation.goBack}
            />
            <Animated.View
                style={{
                    padding: 16,
                    width: '90%',
                    maxWidth: 400,
                    borderRadius: 3,
                    backgroundColor: colors.card,
                    transform: [
                        {
                            scale: current.progress.interpolate(
                                {
                                    inputRange: [0, 1],
                                    outputRange: [0.9, 1],
                                    extrapolate: 'clamp',
                                }
                            ),
                        },
                    ],
                }}
            >
                <Text>
                    Mise en place is a French term that
                    literally means “put in place.” It also
                    refers to a way cooks in professional
                    kitchens and restaurants set up their
                    work stations—first by gathering all
                    ingredients for a recipes, partially
                    preparing them (like measuring out and
                    chopping), and setting them all near
                    each other. Setting up mise en place
                    before cooking is another top tip for
                    home cooks, as it seriously helps with
                    organization. It’ll pretty much
                    guarantee you never forget to add an
                    ingredient and save you time from
                    running back and forth from the pantry
                    ten times.
                </Text>
                <Button
                    title="Okay"
                    color={colors.primary}
                    style={{ alignSelf: 'flex-end' }}
                    onPress={navigation.goBack}
                />
            </Animated.View>
        </View>
    );
}
```

Здесь мы анимируем масштаб диалога, а также добавляем накладку для его закрытия.

## Ссылки

-   [Stack Navigator](https://reactnavigation.org/docs/stack-navigator/)
