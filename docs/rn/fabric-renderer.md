---
description: Fabric — это новая система рендеринга React Native, концептуальная эволюция унаследованной системы рендеринга
---

# Fabric

**Fabric** — это новая система рендеринга React Native, концептуальная эволюция унаследованной системы рендеринга. Основные принципы — унифицировать больше логики рендеринга на C++, улучшить совместимость с [хост-платформами](architecture-glossary.md#host-platform) и раскрыть новые возможности для React Native. Разработка началась в 2018 году, а в 2021 году React Native в приложении Facebook будет поддерживаться новым рендерером.

Эта документация содержит обзор [нового рендера](architecture-glossary.md#fabric-render) и его концепций. Она избегает специфики платформы и не содержит фрагментов кода или указателей. Эта документация охватывает ключевые концепции, мотивацию, преимущества и обзор конвейера рендеринга в различных сценариях.

## Мотивы и преимущества нового рендерера

Архитектура рендеринга была создана для того, чтобы раскрыть лучшие пользовательские возможности, которые были невозможны при использовании старой архитектуры. Некоторые примеры включают:

-   Благодаря улучшенной совместимости между [host views](architecture-glossary.md#host-view-tree-and-host-view) и React views, рендерер может синхронно измерять и рендерить React поверхности. В старой архитектуре компоновка React Native была асинхронной, что приводило к проблеме "перескакивания" компоновки при встраивании отрендеренного представления React Native в _host view_.
-   Благодаря поддержке многоприоритетных и синхронных событий рендерер может определять приоритеты определенных пользовательских взаимодействий, чтобы обеспечить их своевременную обработку.
-   [Интеграция с React Suspense](https://reactjs.org/blog/2019/11/06/building-great-user-experiences-with-concurrent-mode-and-suspense.html), которая позволяет более интуитивно понятно проектировать выборку данных в приложениях React.
-   Включение React [Concurrent Features](https://github.com/reactwg/react-18/discussions/4) на React Native.
-   Более простая реализация рендеринга на стороне сервера для React Native.

Новая архитектура также обеспечивает преимущества в качестве кода, производительности и расширяемости:

-   **Безопасность типов:** генерация кода для обеспечения безопасности типов на JS и [хост-платформах](architecture-glossary.md#host-platform). Генерация кода использует объявления компонентов JavaScript в качестве источника истины для генерации структур C++ для хранения пропсов. Несовпадение пропсов JavaScript и хост-компонента вызывает ошибку сборки.
-   **Общее ядро C++**: рендерер реализован на C++, и ядро разделяется между платформами. Это повышает согласованность и облегчает внедрение React Native на новых платформах.
-   **Лучшая совместимость с хост-платформами**: Синхронный и потокобезопасный расчет макета улучшает пользовательский опыт при встраивании хост-компонентов в React Native, что означает более легкую интеграцию с фреймворками хост-платформ, требующими синхронных API.
-   **Улучшенная производительность**: Благодаря новой кроссплатформенной реализации системы рендеринга каждая платформа получает преимущества от улучшения производительности, которое могло быть обусловлено ограничениями одной платформы. Например, сглаживание представлений изначально было решением для производительности Android, а теперь оно предоставляется по умолчанию как на Android, так и на iOS.
-   **Согласованность**: Новая система рендеринга является кроссплатформенной, поэтому легче поддерживать согласованность между различными платформами.
-   **Быстрее запуск**: Компоненты хоста по умолчанию инициализируются лениво.
-   **Меньше сериализации данных между JS и хост-платформой**: Раньше React передавал данные между JavaScript и _хост-платформой_ в виде сериализованного JSON. Новый рендерер улучшает передачу данных, обращаясь к значениям JavaScript напрямую с помощью [JavaScript Interfaces (JSI)](architecture-glossary.md#javascript-interfaces-jsi).
