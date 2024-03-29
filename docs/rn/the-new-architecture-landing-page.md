# Введение в новую архитектуру

!!!warning "Внимание"

    Эта документация все еще является **экспериментальной**, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в [обсуждении внутри рабочей группы](https://github.com/reactwg/react-native-new-architecture/discussions/8) для этой страницы.

    Более того, она содержит несколько **ручных шагов**. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

Начиная с версии 0.68, React Native предоставляет новую архитектуру, которая предлагает разработчикам новые возможности для создания высокопроизводительных и отзывчивых приложений. Посетите страницу [Why a New Architecture](the-new-architecture-why.md), чтобы узнать больше о том, чем было обусловлено решение о новой архитектуре и какие преимущества она дает.

Чтобы достичь этих преимуществ, нам пришлось переосмыслить принципы работы нативных модулей и нативных компонентов. Это привело нас к разработке [Столпов новой архитектуры](the-new-architecture-pillars.md):

-   [Новая система нативных модулей — турбомодули](the-new-architecture-pillars-turbomodules.md), каркас для поддержки эффективной и гибкой интеграции с нативным кодом
-   [Новый нативный рендерер — Fabric](the-new-architecture-pillars-fabric-components.md), который предлагает улучшенные возможности, кроссплатформенную согласованность и производительность при рендеринге
-   [The Codegen](the-new-architecture-pillars-codegen.md), который генерирует кодовую таблицу C++, необходимую для новой архитектуры, посредством статической типизации в JavaScript.

## Начать работу с новой архитектурой

### Для разработчиков приложений

Чтобы **создать новое приложение** с использованием новой архитектуры, перейдите по ссылке [Creating a New Architecture App](the-new-architecture-use-app-template.md), которая поможет вам в несколько быстрых шагов запустить новое приложение с шаблоном.

Чтобы **перевести существующее приложение** на новую архитектуру, следуйте [Adopting the New Architecture](new-architecture-intro.md).

### Для составителей библиотек

Сначала ознакомьтесь с основными концепциями, изложенными в разделе [Столпы](the-new-architecture-pillars.md).

Затем, чтобы получить **руководство** по поддержке новой архитектуры, ознакомьтесь с руководством [Migration](new-architecture-library-intro.md).

Информацию о **поддержке старой и новой архитектур** смотрите в руководстве [Обратная совместимость] (the-new-architecture-backward-compatibility.md).
