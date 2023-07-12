# Основные компоненты и нативные компоненты

**React Native** — это фреймворк с открытым исходным кодом для создания приложений для Android и iOS с использованием [React](https://reactdev.ru/) и нативных возможностей платформы приложения. В React Native вы используете JavaScript для доступа к API вашей платформы, а также для описания внешнего вида и поведения вашего пользовательского интерфейса с помощью компонентов React: пучков многократно используемого, вложенного кода. Подробнее о React вы сможете узнать в следующем разделе. Но сначала давайте рассмотрим, как работают компоненты в React Native.

## Представления и мобильная разработка

В разработке для Android и iOS **вид** — это основной строительный блок пользовательского интерфейса: небольшой прямоугольный элемент на экране, который может использоваться для отображения текста, изображений или реагирования на ввод пользователя. Даже самые маленькие визуальные элементы приложения, такие как строка текста или кнопка, являются видами представлений. Некоторые виды представлений могут содержать другие виды представлений. Это все виды!

<figure markdown>
![Диаграмма приложений для Android и iOS показывает, что они оба построены на атомарных элементах, называемых представлениями.](diagram_ios-android-views.svg)
<figcaption>Одна лишь выборка из множества представлений, используемых в приложениях для Android и iOS.</figcaption>
</figure>

## Нативные компоненты

При разработке Android вы пишете представления на Kotlin или Java; при разработке iOS вы используете Swift или Objective-C. В React Native вы можете вызывать эти представления с помощью JavaScript, используя компоненты React. Во время выполнения React Native создает соответствующие представления Android и iOS для этих компонентов. Поскольку компоненты React Native поддерживаются теми же представлениями, что и Android и iOS, приложения React Native выглядят, ощущаются и работают как любые другие приложения. Мы называем эти компоненты, поддерживаемые платформой, **Native Components.**.

React Native поставляется с набором основных, готовых к использованию нативных компонентов, которые вы можете использовать, чтобы начать создавать свои приложения уже сегодня. Это **Основные компоненты React Native**.

React Native также позволяет создавать собственные нативные компоненты для [Android](native-components-android.md) и [iOS](native-components-ios.md) в соответствии с уникальными потребностями вашего приложения. У нас также есть процветающая экосистема этих **компонентов, созданных сообществом.** Посмотрите [Native Directory](https://reactnative.directory), чтобы найти то, что было создано сообществом.

<!-- 0001.part.md -->

## Основные компоненты

В React Native есть множество основных компонентов для всего — от элементов управления до индикаторов активности. Вы можете найти их все [документированные в разделе API](../components/components-and-apis.md). В основном вы будете работать со следующими основными компонентами:

| React Native UI Component | Android View   | iOS View         | Web Analog              | Description                                                                                           |
| ------------------------- | -------------- | ---------------- | ----------------------- | ----------------------------------------------------------------------------------------------------- |
| `<View>`                  | `<ViewGroup>`  | `<UIView>`       | A non-scrolling `<div>` | A container that supports layout with flexbox, style, some touch handling, and accessibility controls |
| `<Text>`                  | `<TextView>`   | `<UITextView>`   | `<p>`                   | Displays, styles, and nests strings of text and even handles touch events                             |
| `<Image>`                 | `<ImageView>`  | `<UIImageView>`  | `<img>`                 | Displays different types of images                                                                    |
| `<ScrollView>`            | `<ScrollView>` | `<UIScrollView>` | `<div>`                 | A generic scrolling container that can contain multiple components and views                          |
| `<TextInput>`             | `<EditText>`   | `<UITextField>`  | `<input type="text">`   | Allows the user to enter text                                                                         |

В следующем разделе вы начнете комбинировать эти основные компоненты, чтобы узнать, как работает React. Попробуйте поиграть с ними здесь и сейчас!

<div data-snack-id="@bndby/hello-world" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>
<script async src="https://snack.expo.dev/embed.js"></script>

Поскольку React Native использует ту же структуру API, что и компоненты React, для начала работы вам потребуется понимание API компонентов React. Следующий раздел [next section](intro-react) позволяет быстро ввести или освежить тему. Однако если вы уже знакомы с React, не стесняйтесь [skip ahead](handling-text-input).

![Диаграмма, показывающая, что основные компоненты React Native являются подмножеством компонентов React, которые поставляются с React Native.](diagram_react-native-components.svg)
