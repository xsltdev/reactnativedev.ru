# Глоссарий

## Dev Menu

Меню разработчика приложения (доступно в сборках разработки), которое предоставляет доступ к различным действиям по разработке и отладке. [Подробнее о меню разработчика в документации](../guides/debugging.md).

## Рендерер Fabric

React Native выполняет тот же код фреймворка React, что и React для веб. Однако React Native выполняет рендеринг в общие представления платформы (host views), а не в узлы DOM (которые можно считать host views в web). Рендеринг в представления хостов возможен благодаря Fabric Renderer. Fabric позволяет React общаться с каждой платформой и управлять ее экземплярами хост-представлений. Fabric Renderer существует на JavaScript и ориентируется на интерфейсы, доступные в коде C++. [Подробнее о рендерерах React читайте в этой статье блога](https://overreacted.io/react-as-a-ui-runtime/#renderers).

## Платформа хоста

Платформа, на которой внедрена React Native (например, Android, iOS, macOS, Windows).

## Дерево представления хоста (и представление хоста)

Древовидное представление представлений на хост-платформе (например, Android, iOS). На Android представления хоста являются экземплярами `android.view.ViewGroup`, `android.widget.TextView` и т.д., которые являются строительными блоками дерева представлений хоста. Размер и расположение каждого хост-представления основаны на `LayoutMetrics`, вычисляемых с помощью Yoga, а стиль и содержание каждого хост-представления основаны на информации из React Shadow Tree.

## Интерфейсы JavaScript (JSI)

Легкий API для встраивания JavaScript-движка в приложение на C++. Fabric использует его для связи между ядром C++ Fabric и React.

## Java Native Interface (JNI)

[API для написания нативных методов Java](https://docs.oracle.com/javase/8/docs/technotes/guides/jni/), используемый для связи между C++ ядром Fabric и Android, написанным на Java.

## React Component

Функция или класс JavaScript, который инструктирует, как создать элемент React. [Подробнее о компонентах и элементах React читайте в этой статье блога.](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)

## Композитные компоненты React

React Components с реализациями `render`, которые сводятся к другим React Composite Components или React Host Components.

## Компоненты хоста React или компоненты хоста

Компоненты React, чья реализация представления обеспечивается хост-представлением (например, `<View>`, `<Text>`). В Интернете хост-компоненты ReactDOM — это такие компоненты, как `<p>` и `<div>`.

## React Element Tree (и React Element)

Дерево _React Element Tree_ создается React на JavaScript и состоит из React Elements. Элемент _React Element_ — это обычный объект JavaScript, который описывает то, что должно появиться на экране. Он включает в себя реквизиты, стили и дочерние элементы. React Elements существуют только в JavaScript и могут представлять собой инстансы либо React Composite Components, либо React Host Components. [Подробнее о компонентах и элементах React читайте в этой статье блога](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html).

## React Shadow Tree (и React Shadow Node)

Дерево _React Shadow Tree_ создается рендерером Fabric и состоит из узлов React Shadow Nodes. Узел React Shadow Node — это объект, представляющий монтируемый компонент React Host, который содержит реквизиты, происходящие из JavaScript. Они также содержат информацию о компоновке (x, y, ширина, высота). В Fabric объекты React Shadow Node существуют в C++. До Fabric они существовали в куче среды выполнения мобильных приложений (например, Android JVM).

## Дерево йоги (и узел йоги)

Дерево _Yoga Tree_ используется [Yoga](https://yogalayout.com/) для расчета информации о компоновке дерева React Shadow Tree. Каждый узел React Shadow Node обычно создает _Yoga Node_, поскольку React Native использует Yoga для расчета компоновки. Однако это не является жестким требованием. Fabric также может создавать узлы React Shadow, которые не используют Yoga; реализация каждого узла React Shadow определяет способ вычисления компоновки.
