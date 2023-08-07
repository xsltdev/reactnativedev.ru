---
description: RootTag — это непрозрачный идентификатор, присвоенный корневому представлению вашей поверхности React Native
---

# RootTag

**`RootTag`** — это непрозрачный идентификатор, присвоенный корневому представлению вашей поверхности React Native — т.е. экземпляру `ReactRootView` или `RCTRootView` для Android или iOS соответственно. Короче говоря, это идентификатор поверхности.

## Когда использовать RootTag? {#when-to-use-a-roottag}

Для большинства разработчиков React Native, скорее всего, вам не придется иметь дело с `RootTag`.

Теги RootTag` полезны, когда приложение отображает **многочисленные корневые представления React Native** и вам нужно обрабатывать вызовы нативных API по-разному в зависимости от поверхности. Примером может служить ситуация, когда приложение использует нативную навигацию, и каждый экран является отдельным корневым представлением React Native.

В нативной навигации каждое корневое представление React Native отображается в навигационном представлении платформы (например, `Activity` для Android, `UINavigationViewController` для iOS). Благодаря этому вы можете использовать навигационные парадигмы платформы, такие как родной внешний вид и навигационные переходы. Функциональность для взаимодействия с родными API навигации может быть открыта в React Native через [native module]native-modules-intro.md).

Например, чтобы обновить строку заголовка экрана, можно вызвать API навигационного модуля `setTitle("Updated Title")`, но ему нужно знать, какой экран в стеке нужно обновить. Здесь необходим тег `RootTag` для идентификации корневого представления и его хостингового контейнера.

Другой случай использования `RootTag` — когда вашему приложению нужно приписать определенный вызов JavaScript к native на основе его происхождения из корневого представления. Тег `RootTag` необходим для того, чтобы отличить источник вызова от различных поверхностей.

## Как получить доступ к RootTag... если он вам нужен {#how-to-access-the-roottag-if-you-need-it}

В версиях 0.65 и ниже доступ к RootTag осуществляется через [legacy context](https://github.com/facebook/react-native/blob/v0.64.1/Libraries/ReactNative/AppContainer.js#L56). Чтобы подготовить React Native к функциям Concurrent, которые появятся в React 18 и последующих версиях, мы переходим на новейший [Context API](https://reactdev.ru/learn/passing-data-deeply-with-context/) через `RootTagContext` в 0.66. Версия 0.65 поддерживает как устаревший контекст, так и рекомендуемый `RootTagContext`, чтобы дать разработчикам время на миграцию их сайтов вызова. Смотрите сводку изменений.

Как получить доступ к `RootTag` через `RootTagContext`.

```js
import { RootTagContext } from 'react-native';
import NativeAnalytics from 'native-analytics';
import NativeNavigation from 'native-navigation';

function ScreenA() {
    const rootTag = useContext(RootTagContext);

    const updateTitle = (title) => {
        NativeNavigation.setTitle(rootTag, title);
    };

    const handleOneEvent = () => {
        NativeAnalytics.logEvent(rootTag, 'one_event');
    };

    // ...
}

class ScreenB extends React.Component {
    static contextType: typeof RootTagContext = RootTagContext;

    updateTitle(title) {
        NativeNavigation.setTitle(this.context, title);
    }

    handleOneEvent() {
        NativeAnalytics.logEvent(this.context, 'one_event');
    }

    // ...
}
```

### Ломающее изменение в 0.65

`RootTagContext` ранее назывался `unstable_RootTagContext` и был заменен на `RootTagContext` в 0.65. Пожалуйста, обновите все случаи использования `unstable_RootTagContext` в вашей базе данных.

### Ломающее изменение в 0.66

Устаревший контекстный доступ к `RootTag` будет удален и заменен на `RootTagContext`. Начиная с версии 0.65, мы призываем разработчиков активно переносить доступ к `RootTag` на `RootTagContext`.

## Планы на будущее {#future-plans}

По мере развития новой архитектуры React Native будут появляться будущие итерации `RootTag`, с намерением сохранить тип `RootTag` непрозрачным и предотвратить трэш в кодовых базах React Native. Пожалуйста, не полагайтесь на тот факт, что RootTag в настоящее время является псевдонимом числа! Если ваше приложение использует RootTag, следите за нашими журналами изменений версий, которые вы можете найти [здесь](https://github.com/facebook/react-native/blob/main/CHANGELOG.md).
