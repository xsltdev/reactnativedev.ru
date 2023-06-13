

---
id: react-18-and-react-native
title: React 18 & React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';
import NewArchitectureWarning from './\_markdown-new-architecture-warning.mdx';

<NewArchitectureWarning/>

На этой странице описано, как использовать React 18 с React Native, используя новую архитектуру React Native.



> **tl;dr:** Первая версия React Native, совместимая с React 18 - **0.69.0**. Чтобы использовать новые возможности React 18, включая автоматическое пакетирование, `startTransition` и `useDeferredValue`, вы должны перевести ваше приложение React Native на новую архитектуру.




## React 18 и новая архитектура React Native




React 18 представляет [несколько новых возможностей] (https://reactjs.org/blog/2022/03/29/react-v18.html), включая:




- Автоматическое пакетирование

- Новое поведение в строгом режиме

- Новые хуки (`useId`, `useSyncExternalStore`).




Он также включает новые одновременные функции:




- `startTransition`

- `useTransition`

- `useDeferredValue`.

- Полная поддержка приостановки




Функции одновременности в React 18 построены на базе нового механизма одновременного рендеринга. Параллельный рендеринг - это новый закулисный механизм, который позволяет React подготавливать несколько версий пользовательского интерфейса одновременно.




Предыдущие версии React Native, построенные на старой архитектуре, **не могут** поддерживать одновременный рендеринг или одновременные функции. Это связано с тем, что старая архитектура полагалась на мутирование нативных деревьев, что не позволяет React подготавливать несколько версий дерева одновременно.




К счастью, новая архитектура была написана снизу вверх с учетом одновременного рендеринга и полностью совместима с React 18. Это означает, что для перехода на React 18 в вашем приложении React Native, ваше приложение должно быть переведено на Новую архитектуру React Native, включая Fabric Native Components и Turbo Native Modules.




## React 18 включен по умолчанию




Начиная с версии React Native 0.69, React 18 **включается по умолчанию**, когда вы включаете новую архитектуру.

Это означает, что вы сможете использовать новые возможности React 18 сразу после миграции. Поскольку новые одновременные функции являются опциональными, используя такие функции, как `startTransition` или `Suspense`, мы ожидаем, что React 18 будет работать "из коробки" с минимальными изменениями для пользователей, которые переходят на новую архитектуру или создают новое приложение с включенной новой архитектурой.



Однако если у вас возникнут проблемы, мы предоставляем возможность отказаться от использования нового корня в React 18. Отказ означает, что ваше приложение будет работать в режиме React 17, и ни одна из функций React 18 не будет доступна.




### Отказ от React 18 на Android




На Android вы сможете переопределить `isConcurrentRootEnabled` в вашем ActivityDelegate (в файле `MainActivity`) и включить/выключить Concurrent React.

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```diff
public class MainActivity extends ReactActivity {

  public static class MainActivityDelegate extends ReactActivityDelegate {
    public MainActivityDelegate(ReactActivity activity, String mainComponentName) {
      super(activity, mainComponentName);
    }

    @Override
    protected ReactRootView createRootView() {
      ReactRootView reactRootView = new ReactRootView(getContext());
      // If you opted-in for the New Architecture, we enable the Fabric Renderer.
      reactRootView.setIsFabric(BuildConfig.IS_NEW_ARCHITECTURE_ENABLED);
      return reactRootView;
    }

+   @Override
+   protected boolean isConcurrentRootEnabled() {
+     // If you opted-in for the New Architecture, we enable Concurrent Root (i.e. React 18).
+     // More on this on https://reactjs.org/blog/2022/03/29/react-v18.html
+     return BuildConfig.IS_NEW_ARCHITECTURE_ENABLED;
+   }
  }
}
```

</TabItem>

<TabItem value="kotlin">

```diff
class MainActivity : ReactActivity() {

    open class MainActivityDelegate(activity: ReactActivity?, mainComponentName: String?) : ReactActivityDelegate(activity, mainComponentName) {
        override fun createRootView(): ReactRootView = ReactRootView(context).apply {
            // If you opted-in for the New Architecture, we enable the Fabric Renderer.
            setIsFabric(BuildConfig.IS_NEW_ARCHITECTURE_ENABLED)
        }

+       // If you opted-in for the New Architecture, we enable Concurrent Root (i.e. React 18).
+       // More on this on https://reactjs.org/blog/2022/03/29/react-v18.html
+       override fun isConcurrentRootEnabled() = BuildConfig.IS_NEW_ARCHITECTURE_ENABLED
    }
}
```

</TabItem>
</Tabs>

### Отказ от React 18 на iOS



На iOS у вас будет доступ к методу `concurrentRootEnabled` в вашем файле `AppDelegate.mm`. Вы должны изменить возвращаемое значение на `false` (или `NO`), чтобы отключить эту функцию.

```objc
/// This method controls whether the `concurrentRoot`feature of React18 is turned on or off.
///
/// @see: https://reactjs.org/blog/2022/03/29/react-v18.html
/// @note: This requires to be rendering on Fabric (i.e. on the New Architecture).
/// @return: `true` if the `concurrentRoot` feature is enabled. Otherwise, it returns `false`.
- (BOOL)concurrentRootEnabled
{
  // Switch this bool to turn on and off the concurrent root
  return true;
}
```

### Пользователи на React Native 0.69, еще не перешедшие на новую архитектуру



Примечание: Пользователи на React Native 0.69, но все еще на старой архитектуре, будут использовать режим React 17, даже если React 18 установлен в файле `package.json`.




Переопределение метода `isConcurrentRootEnabled` не окажет никакого влияния на ваше приложение.

