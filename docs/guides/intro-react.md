# Основы React

React Native работает на базе [React](https://reactdev.ru/), популярной библиотеки с открытым исходным кодом для создания пользовательских интерфейсов на JavaScript. Чтобы максимально использовать возможности React Native, необходимо понять сам React. Этот раздел может помочь вам начать работу или послужить в качестве дополнительного курса.

Мы рассмотрим основные концепции, лежащие в основе React:

-   компоненты
-   JSX
-   пропсы
-   состояние

Если вы хотите копнуть глубже, мы рекомендуем вам ознакомиться с [официальной документацией React](https://react.dev/learn).

## Ваш первый компонент

Остальная часть этого введения в React использует в своих примерах кошек: дружелюбных, доступных существ, которым нужны имена и кафе для работы. Вот ваш самый первый компонент Cat:

<div data-snack-id="@bndby/your-cat" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Вот как это делается: Чтобы определить свой компонент `Cat`, сначала используйте JavaScript [`import`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import) для импорта React и React Native [`Text`](../components/text.md) Core Component:

```ts
import React from 'react';
import { Text } from 'react-native';
```

Ваш компонент начинается как функция:

```ts
const Cat = () => {};
```

Компоненты можно рассматривать как чертежи. Все, что возвращает функциональный компонент, отображается как **React-элемент.** React-элементы позволяют вам описать то, что вы хотите видеть на экране.

Здесь компонент `Cat` отображает элемент `<Text>`:

```ts
const Cat = () => {
    return <Text>Здравствуйте, я ваш кот!</Text>;
};
```

Вы можете экспортировать свой компонент функции с помощью JavaScript [`export default`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export) для использования в вашем приложении следующим образом:

```ts
const Cat = () => {
    return <Text>Здравствуйте, я ваш кот!</Text>;
};

export default Cat;
```

!!!warning ""

    Это один из многих способов экспорта вашего компонента. Этот вид экспорта хорошо работает с проигрывателем Snack Player. Однако, в зависимости от структуры файлов вашего приложения, вам может понадобиться использовать другие соглашения. В этом вам поможет [удобная шпаргалка по импорту и экспорту JavaScript](https://medium.com/dailyjs/javascript-module-cheatsheet-7bd474f1d829).

Теперь посмотрите внимательнее на оператор `return`. `<Text>Hello, I am your cat!</Text>` использует разновидность синтаксиса JavaScript, которая делает удобным написание элементов: JSX.

## JSX

React и React Native используют **JSX,** синтаксис, который позволяет писать элементы внутри JavaScript, например: `<Text>Hello, I am your cat!</Text>`. В документации React есть [полное руководство по JSX](https://reactdev.ru/learn/writing-markup-with-jsx), которое вы можете посмотреть, чтобы узнать еще больше. Поскольку JSX — это JavaScript, вы можете использовать в нем переменные. Здесь вы объявляете имя для кошки, `name`, и вставляете его в фигурные скобки внутри `<Text>`.

<div data-snack-id="@bndby/curly-braces" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Любое выражение JavaScript будет работать между фигурными скобками, включая вызовы функций типа `{getFullName("Rum", "Tum", "Tugger")}`:

=== "TypeScript"

    <div data-snack-id="@bndby/curly-braces-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/curly-braces-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Вы можете думать о фигурных скобках как о создании портала в функциональность JS в вашем JSX!

!!!warning ""

    Поскольку JSX включен в библиотеку React, он не будет работать, если у вас нет `import React from 'react'` в верхней части вашего файла!

## Пользовательские компоненты

Вы уже познакомились с [основными компонентами React Native](intro-react-native-components.md). React позволяет вкладывать эти компоненты друг в друга для создания новых компонентов. Эти вложенные, многократно используемые компоненты лежат в основе парадигмы React.

Например, вы можете вложить [`Text`](../components/text.md) и [`TextInput`](../components/textinput.md) внутри [`View`](../components/view.md) ниже, и React Native отобразит их вместе:

<div data-snack-id="@bndby/custom-components" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Примечания разработчика

=== "Web"

    Если вы знакомы с веб-разработкой, `<View>` и `<Text>` могут напомнить вам HTML! Вы можете думать о них как о тегах `<div>` и `<p>` при разработке приложений.

=== "Android"

    В Android вы обычно помещаете свои представления внутрь `LinearLayout`, `FrameLayout`, `RelativeLayout` и т.д., чтобы определить, как дочерние элементы представления будут расположены на экране. В React Native `View` использует Flexbox для компоновки своих дочерних элементов. Вы можете узнать больше в [нашем руководстве по компоновке с Flexbox](flexbox.md).

Вы можете отобразить этот компонент несколько раз и в нескольких местах без повторения кода, используя `<Cat>`:

<div data-snack-id="@bndby/multiple-components" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Любой компонент, который отображает другие компоненты, является **родительским компонентом.** Здесь `Cafe` является родительским компонентом, а каждый `Cat` является **потомственным компонентом.**.

Вы можете поместить в кафе столько кошек, сколько захотите. Каждая `<Cat>` отображает уникальный элемент, который вы можете настроить с помощью пропсов.

## Пропсы

**Пропсы** — это сокращение от "свойства". Пропсы позволяют вам настраивать компоненты React. Например, здесь вы передаете каждому `<Cat>` разное `имя` для рендеринга `Cat`:

=== "TypeScript"

    <div data-snack-id="@bndby/multiple-props-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/multiple-props-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Большинство основных компонентов React Native также могут быть настроены с помощью пропсов. Например, при использовании [`Image`](../components/image.md) вы передаете ему пропс [`source`](../components/image.md), чтобы определить, какое изображение он показывает:

<div data-snack-id="@bndby/props" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

У `Image` есть [множество различных пропсов](../components/image.md), включая [`style`](../components/image.md), который принимает JS-объект пар свойств-значений, связанных с дизайном и компоновкой.

!!!warning ""

    Обратите внимание на двойные фигурные скобки `{{ }}`, окружающие ширину и высоту `style`. В JSX на значения JavaScript ссылаются с помощью `{}`. Это удобно, если вы передаете в качестве пропса не строку, а что-то другое, например массив или число: `<Cat food={["рыба", "киббл"]} age={2} />`. Однако JS-объекты **_также_** обозначаются фигурными скобками: `{width: 200, height: 200}`. Поэтому, чтобы передать JS-объект в JSX, вы должны обернуть объект в **еще одну пару** фигурных скобок: `{{width: 200, height: 200}}`.

Вы можете создать множество вещей с помощью пропсов и основных компонентов [`Text`](../components/text.md), [`Image`](../components/image.md) и [`View`](../components/view.md)! Но чтобы создать что-то интерактивное, вам понадобится состояние.

## Состояние

Если пропсы можно рассматривать как аргументы, которые используются для настройки отображения компонентов, то **состояние** — это как личное хранилище данных компонента. Состояние полезно для обработки данных, которые изменяются со временем или возникают в результате взаимодействия с пользователем. Состояние дает вашим компонентам память!

!!!warning ""

    Как правило, используйте props для настройки компонента при его рендеринге. Используйте состояние для отслеживания любых данных компонента, которые, как вы ожидаете, будут меняться со временем.

Следующий пример происходит в кошачьем кафе, где две голодные кошки ждут, когда их покормят. Их голод, который, как мы ожидаем, будет меняться со временем (в отличие от их имен), хранится как состояние. Чтобы покормить кошек, нажмите на их кнопки, что приведет к обновлению их состояния.

Вы можете добавить состояние в компонент, вызвав [React's `useState` Hook](https://reactdev.ru/learn/state-a-components-memory). Хук — это своего рода функция, которая позволяет вам "подключаться" к функциям React. Например, `useState` — это хук, позволяющий добавлять состояние в компоненты функций. Вы можете узнать больше о [других видах хуков в документации по React](https://reactdev.ru/reference/).

=== "TypeScript"

    <div data-snack-id="@bndby/state-ts" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/state-js" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Во-первых, вы должны импортировать `useState` из React следующим образом:

<!-- 0025.part.md -->

```ts
import React, { useState } from 'react';
```

<!-- 0026.part.md -->

Затем вы объявляете состояние компонента, вызывая `useState` внутри его функции. В этом примере `useState` создает переменную состояния `isHungry`:

<!-- 0027.part.md -->

```ts
const Cat = (props: CatProps) => {
    const [isHungry, setIsHungry] = useState(true);
    // ...
};
```

<!-- 0028.part.md -->

!!!warning ""

    Вы можете использовать `useState` для отслеживания любого типа данных: строк, чисел, булевых функций, массивов, объектов. Например, вы можете отслеживать количество раз, когда кошку гладили, используя `const [timesPetted, setTimesPetted] = useState(0)`!

Вызов `useState` делает две вещи:

-   создает "переменную состояния" с начальным значением — в данном случае переменная состояния — `isHungry` и ее начальное значение — `true`
-   она создает функцию для установки значения этой переменной состояния — `setIsHungry`.

Неважно, какие имена вы используете. Но может быть удобно представить паттерн как `[<getter>, <setter>] = useState(<initialValue>)`.

Далее вы добавляете основной компонент [`Button`](../components/button.md) и задаете ему свойство `onPress`:

<!-- 0029.part.md -->

```ts
<Button
    onPress={() => {
        setIsHungry(false);
    }}
    //..
/>
```

<!-- 0030.part.md -->

Теперь, когда кто-то нажимает на кнопку, срабатывает `onPress`, вызывая `setIsHungry(false)`. Это установит переменную состояния `isHungry` в значение `false`. Когда `isHungry` равно false, параметр `disabled` кнопки `Button` устанавливается в `true` и ее `title` также изменяется:

<!-- 0031.part.md -->

```ts
<Button
    //..
    disabled={!isHungry}
    title={
        isHungry
            ? 'Pour me some milk, please!'
            : 'Thank you!'
    }
/>
```

<!-- 0032.part.md -->

!!!warning ""

    Вы могли заметить, что хотя `isHungry` является [const](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/const), она, похоже, переназначаема! Происходит так: когда вызывается функция, задающая состояние, такая как `setIsHungry`, ее компонент перерисовывается. В этом случае функция `Cat` будет запущена снова, и на этот раз `useState` даст нам следующее значение `isHungry`.

Наконец, поместите ваших кошек внутрь компонента `Cafe`:

<!-- 0033.part.md -->

```ts
const Cafe = () => {
    return (
        <>
            <Cat name="Munkustrap" />
            <Cat name="Spot" />
        </>
    );
};
```

<!-- 0034.part.md -->

!!!warning ""

    Видите `<>` и <code>&lt;/&gt;</code> выше? Эти кусочки JSX являются [фрагментами](https://reactdev.ru/reference/Fragment/). Смежные элементы JSX должны быть обернуты в окружающий тег. Фрагменты позволяют сделать это без вложения дополнительного, ненужного элемента обертки, такого как `View`.

Теперь, когда вы рассмотрели основные компоненты React и React Native, давайте углубимся в некоторые из этих основных компонентов, рассмотрев [handling `<TextInput>`](handling-text-input.md).

<!-- 0035.part.md -->
