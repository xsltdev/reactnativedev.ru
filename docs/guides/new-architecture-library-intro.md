# Необходимые условия для библиотек

!!!warning "Внимание"

    Эта документация все еще является экспериментальной, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в обсуждении внутри рабочей группы для этой страницы.

    Более того, она содержит несколько ручных шагов. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

Следующие шаги помогут обеспечить готовность ваших модулей и компонентов к новой архитектуре.

## Определите спецификации в JavaScript

Спецификации JavaScript служат источником истины для методов, предоставляемых каждым родным модулем. Они определяют все API, предоставляемые родным модулем, а также типы этих констант и функций.

Использование **типизированного** файла спецификации позволяет вам быть преднамеренным и объявить все входные аргументы и выходы методов вашего родного модуля.

!!!info ""

    Поддержка **TypeScript** сейчас находится в бета-версии.

Чтобы перейти на новую архитектуру, начните с создания этих спецификаций для ваших родных модулей и родных компонентов. Вы можете сделать это до перехода на новую архитектуру: спецификации будут использоваться позже для генерации кода нативного интерфейса для всех поддерживаемых платформ как способ обеспечения единообразия API на разных платформах.

### Turbo Native Modules

Файлы спецификаций JavaScript **должны** иметь имя `Native<MODULE_NAME>.js`, и они экспортируют объект `Spec` реестра `TurboModuleRegistry`. Соглашение об имени важно потому, что процесс Codegen ищет модули, чей файл спецификации `js` (`jsx`, `ts` или `tsx`) начинается с ключевого слова `Native`.

Ниже приведен базовый шаблон спецификации JavaScript, написанный с использованием синтаксиса [Flow](https://flow.org/) и [TypeScript](https://scriptdev.ru/guide/).

=== "Flow"

    ```js
    // @flow strict

    import type { TurboModule } from 'react-native/Libraries/TurboModule/RCTExport';
    import { TurboModuleRegistry } from 'react-native';

    export interface Spec extends TurboModule {
    	+getConstants: () => {||};

    	// your module methods go here, for example:
    	getString(id: string): Promise<string>;
    }

    export default (TurboModuleRegistry.get<Spec>(
    	'<MODULE_NAME>'
    ): ?Spec);
    ```

=== "TypeScript"

    ```tsx
    import type { TurboModule } from 'react-native';
    import { TurboModuleRegistry } from 'react-native';

    export interface Spec extends TurboModule {
    	readonly getConstants: () => {};

    	// your module methods go here, for example:
    	getString(id: string): Promise<string>;
    }

    export default TurboModuleRegistry.get<Spec>(
    	'<MODULE_NAME>'
    );
    ```

### Fabric Native Components

Файлы спецификаций JavaScript **должны** иметь имя `<FABRIC COMPONENT>NativeComponent.js` (для TypeScript используйте расширение `.ts` или `.tsx`) и экспортировать объект `HostComponent`. Соглашение об имени важно: процесс Codegen ищет компоненты, чей файл спецификации (JavaScript или TypeScript) заканчивается суффиксом `NativeComponent`.

Следующий фрагмент показывает базовый шаблон спецификации JavaScript, написанный на [Flow](https://flow.org/), а также [TypeScript](https://scriptdev.ru/guide/).

=== "Flow"

    ```js
    // @flow strict-local

    import type { ViewProps } from 'react-native/Libraries/Components/View/ViewPropTypes';
    import type { HostComponent } from 'react-native';
    import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';

    type NativeProps = $ReadOnly<{|
    	...ViewProps,
    	// add other props here
    |}>;

    export default (codegenNativeComponent<NativeProps>(
    	'<FABRIC COMPONENT>'
    ): HostComponent<NativeProps>);
    ```

=== "TypeScript"

    ```tsx
    import type { ViewProps } from 'ViewPropTypes';
    import type { HostComponent } from 'react-native';
    import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';

    export interface NativeProps extends ViewProps {
    	// add other props here
    }

    export default codegenNativeComponent<NativeProps>(
    	'<FABRIC COMPONENT>'
    ) as HostComponent<NativeProps>;
    ```

### Поддерживаемые типы

При использовании Flow или TypeScript вы будете использовать [type annotations](https://flow.org/en/docs/types/) для определения спецификации. Учитывая, что целью определения спецификации JavaScript является обеспечение типобезопасности генерируемого кода родного интерфейса, набор поддерживаемых типов будет состоять из тех, которые могут быть сопоставлены один к одному с соответствующим типом на родной платформе.

В общем случае это означает, что вы можете использовать примитивные типы (строки, числа, булевы), типы функций, типы объектов и типы массивов. Союзные типы, с другой стороны, не поддерживаются. Все типы должны быть доступны только для чтения. Для Flow: либо `+`, либо `$ReadOnly<>`, либо `{||}` объекты. Для TypeScript: `readonly` для свойств, `Readonly<>` для объектов и `ReadonlyArray<>` для массивов.

!!!note ""

    См. приложение [II. Сопоставление потоковых типов с нативными типами](new-architecture-appendix#ii-flow-type-to-native-type-mapping).

    См. приложение [III. Сопоставление TypeScript с нативными типами](new-architecture-appendix#iii-typescript-to-native-type-mapping).

### Вспомогательные типы Codegen

Вы можете использовать предопределенные типы для своих JavaScript-спецификаций, вот их список:

-   `Double`
-   `Float`
-   `Int32`
-   `UnsafeObject`
-   `WithDefault<Type, Value>` — Устанавливает значение по умолчанию для типа
-   `BubblingEventHandler<T>` — Для событий, которые распространяются (bubbled) вверх по дереву компонентов от дочернего компонента к родительскому вплоть до корня (например: `onStartShouldSetResponder`).
-   `DirectEventHandler<T>` — Для событий, которые вызываются только на элементе, получающем событие (например: `onClick`) и не распространяются.

Позже эти типы компилируются в соответствующие эквиваленты на целевых платформах.

### Быть последовательным на разных платформах и устранить неоднозначность типов

Прежде чем внедрять новую архитектуру в свой родной модуль, вы должны убедиться, что ваши методы согласованы между платформами. Вы поймете это, когда приступите к написанию спецификации JavaScript для вашего родного модуля — помните, что спецификация JavaScript определяет, как будут выглядеть методы на всех поддерживаемых платформах.

Если в вашем существующем родном модуле есть методы с одинаковым именем на нескольких платформах, но с разным количеством или типом аргументов на разных платформах, вам нужно будет найти способ сделать их согласованными. Если у вас есть методы, которые могут принимать два или более различных типов для одного и того же аргумента, то вам нужно найти способ разрешить этот тип двусмысленности, поскольку объединения типов намеренно не поддерживаются.

## Убедитесь, что _автоссылка_ включена

[Autolinking](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) — это функция React Native CLI, которая упрощает установку сторонних библиотек React Native. Инструкции по включению _autolinking_ доступны в [React Native CLI docs](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md).

### Android

На Android для этого обычно требуется включить `native_modules.gradle` в `settings.gradle` и `build.gradle`.

Если вы использовали шаблон по умолчанию, поставляемый с React Native (т.е. `yarn react-native init <Project>`), то автолинковка уже включена.

Вы можете проверить, включена ли она, используя:

```bash
$ grep -r "native_modules.gradle" android

android/app/build.gradle:apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
android/settings.gradle:apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)
...
```

Если вы этого не сделали, откройте файл `settings.gradle` и добавьте эту строку:

```diff
rootProject.name = <Your App Name>
+ apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)
```

Затем откройте файл `android/app/build.gradle` и добавьте эту строку в конец файла:

```kotlin
apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
```

### iOS

На iOS убедитесь, что ваша библиотека предоставляет Podspec (пример см. в [`react-native-webview`](https://github.com/react-native-community/react-native-webview/blob/master/react-native-webview.podspec)).

!!!info ""

    Чтобы определить, настроена ли ваша библиотека на автолинковку, проверьте вывод CocoaPods после выполнения команды `pod install` (или `arch -x86_64 pod install` в случае Mac M1) в проекте iOS. Если вы увидите "auto linking library name", то все готово к работе.

## Настройте Codegen

[Codegen](the-new-architecture-pillars-codegen.md) — это инструмент, который запускается при сборке приложения для Android или установке зависимостей приложения для iOS. Он создает некоторый код, который вам не придется создавать вручную.

Codegen можно настроить в файле `package.json` вашей Библиотеки. Добавьте в его конец следующий объект JSON.

```diff
  },
+  "codegenConfig": {
+    "name": "<library name>",
+    "type": "all",
+    "jsSrcsDir": ".",
+    "android": {
+      "javaPackageName": "com.facebook.fbreact.specs"
+    }
+  }
}
```

-   Поле `codegenConfig` — это ключ, используемый Codegen для проверки наличия кода для генерации.
-   Поле `name` — это имя библиотеки.
-   Поле `type` используется для определения типа модуля, который мы хотим создать. Мы рекомендуем оставить `all` для поддержки библиотек, содержащих как Turbo Native Module, так и Fabric Native Components.
-   Поле `jsSrcsDir` — это каталог, в котором codegen начнет искать спецификации JavaScript.
-   `android.javaPackageName` — это имя пакета, в который попадает сгенерированный код.

Android также требует, чтобы в вашем приложении был правильно настроен [React Gradle Plugin](new-architecture-app-intro#android-specifics).

## Миграция с JavaScript API `UIManager`.

В новой архитектуре большинство методов `UIManager` станут доступны как методы экземпляра для нативных экземпляров компонентов, полученных через `ref`:

```tsx
function MyComponent(props: Props) {
  const viewRef = useRef(null);

  useEffect(() => {
    viewRef.current.measure(((left, top, width, height, pageX, pageY) => {
      // ...
    });
  }, []);

  return <View ref={viewRef} />;
}
```

Этот новый дизайн API обеспечивает несколько преимуществ:

-   Улучшение эргономики разработчика за счет отсутствия необходимости отдельно импортировать `UIManager` или вызывать `findNodeHandle`.
-   Более высокая производительность благодаря отсутствию шага поиска узлового хэндла.
-   Согласован по направлению с [аналогичной депривацией `findDOMNode`](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage).

В конечном счете мы устареем `UIManager`. Однако мы признаем, что миграции требуют больших затрат для многих авторов приложений и библиотек. Чтобы минимизировать эти затраты, мы планируем продолжать поддерживать как можно больше методов `UIManager` в новой архитектуре.

**Поддержка методов `UIManager` в новой архитектуре активно разрабатывается.** Пока мы делаем успехи, ранние пользователи могут экспериментировать с новой архитектурой, выполнив следующие шаги по миграции с общих API `UIManager`:

1.  Миграция с `setNativeProps`.
2.  Перенести вызов `requireNativeComponent` в отдельный файл
3.  Перенос `dispatchViewManagerCommand`.
4.  Создание NativeCommands с помощью `codegenNativeCommands`

### Перенос `setNativeProps`

`setNativeProps` не будет поддерживаться в пост-фабричном мире. Для перехода переместите все значения `setNativeProp` в состояние компонента.

**Пример**.

```ts
class MyComponent extends React.Component<Props> {
  _viewRef?: React.ElementRef<typeof View>;

  render() {
    const {somePropValue} = this.props;
    return <View
       onPress={this._onSubmit}
       ref={this._captureRef}
       someProp={somePropValue}
       style={styles.view} />
  }

  _captureRef: (ref: React.ElementRef<typeof View>) => {
    this._viewRef = ref;
  }

  _onSubmit: () => {
    this._viewRef.setNativeProps({
       style: styles.submittedView,
       accessibility: true
    });
    // ...other logic for onSubmit
  }
}

const styles = StyleSheet.create({
  view: {backgroundColor: 'white'},
  submittedView: {borderWidth: 1}
});
```

В этом примере при нажатии кнопки View происходит вызов `setNativeProps` для обновления реквизитов стиля и доступности компонента. Чтобы перенести этот компонент, важно понять его текущее поведение с помощью `setNativeProps`.

#### Pre-Fabric, реквизиты компонента сохраняются

При первом рендере реквизиты компонента — это реквизиты, объявленные в функции рендеринга. После нажатия кнопки View `_onSubmit` вызывает `setNativeProps` с обновленными значениями реквизитов.

Результирующий компонент может быть представлен следующим образом:

```tsx
<View
    accessibility={true}
    onPress={this._onSubmit}
    ref={this._captureRef}
    someProp={somePropValue}
    style={[styles.view, styles.submittedView]}
/>
```

Обратите внимание, что все значения реквизитов, установленные в функции рендеринга, остаются неизменными, даже если `setNativeProps` не передал эти реквизиты. Кроме того, `style` теперь является объединенным значением своего значения до `_onSubmit` и `styles.submittedView`. Это важный момент: в нашем текущем мире, предшествующем миру Fabric, **компонентные реквизиты сохраняются.** Представление платформы кэширует значения реквизитов, переданные ему со стороны JS. Если бы это было не так, то после вызова setNativeProps React Native отобразил бы компонент следующим образом:

```tsx
<View accessibility={true} style={styles.submittedView} />
```

Тот факт, что React Native хранит некоторое внутреннее состояние каждого компонента, которое не объявлено явно в последнем рендере, — это то, что Fabric намерена исправить.

#### Перемещение `setNativeProps` в состояние

Принимая во внимание вышеуказанные предостережения, правильный перенос будет выглядеть следующим образом:

```tsx
class MyComponent extends React.Component<Props> {
  state = {
    hasSubmitted: false,
    accessibility: false
  };

  render() {
    const {somePropValue} = this.props;
    const submittedStyle = this.state.hasSubmitted ? styles.submittedView: null;
    return <View
       accessibility={this.state.accessibility}
       onPress={this._onSubmit}
       someProp={somePropValue}
       style={[styles.view, submittedStyle]} />
  }

  _onSubmit: () => {
    this.setState(state => ({...state, hasSubmitted: true}));
    // ...other logic for onSubmit
  }
}


const styles = StyleSheet.create({
  view: {backgroundColor: 'white'},
  submittedView: {borderWidth: 1}
});
```

-   Мы используем флаг `hasSubmitted`, чтобы показать, хотим ли мы применить `styles.submittedView` или нет. Если стиль был динамическим, то имеет смысл хранить объект стиля в состоянии.
-   Флаг `accessibility` теперь явно передается компоненту View в виде булевой величины. Это отличается от предыдущей реализации, где `accessibility` не передавалось в качестве параметра при первоначальном рендере, но в этом случае мы знаем, что неуказание `accessibility` обрабатывается так же, как и `accessibilty={false}`.

Будьте осторожны в своих предположениях, так как не пойманные тонкости могут внести различия в поведение! Хорошей идеей будет провести моментальные тесты вашего компонента, так как они выявят любые различия до и после миграции.

### Перенесите вызов `requireNativeComponent` в отдельный файл.

Это позволит подготовить JS к новой системе codegen для новой архитектуры. Новый файл должен быть назван `<ComponentName>NativeComponent.js.`.

#### Старый способ

```js
const RNTMyNativeView = requireNativeComponent('RNTMyNativeView');

[...]

return <RNTMyNativeView />;
```

#### Новый путь

```js title="RNTMyNativeNativeComponent.js"
import RNTMyNativeViewNativeComponent from './RNTMyNativeViewNativeComponent';

[...]

return <RNTMyNativeViewNativeComponent />;
```

```js title="RNTMyNativeViewNativeComponent.js"
import { requireNativeComponent } from 'react-native';

const RNTMyNativeViewNativeComponent = requireNativeComponent(
    'RNTMyNativeView'
);

export default RNTMyNativeViewNativeComponent;
```

#### Поддержка Flow

Если `requireNativeComponent` не типизирован, вы можете временно использовать `mixed` тип, чтобы исправить предупреждение Flow, например:

```js
// @flow strict-local

import type { HostComponent } from 'react-native/Libraries/Renderer/shims/ReactNativeTypes';
// ...
const RCTWebViewNativeComponent: HostComponent<mixed> = requireNativeComponent<mixed>(
    'RNTMyNativeView'
);
```

#### Позже вы сможете заменить `requireNativeComponent`.

Когда вы будете готовы к переходу на Fabric, вы можете заменить `requireNativeComponent` на `codegenNativeComponent`:

```js title="RNTMyNativeViewNativeComponent.js"
// @flow strict-local

export default (codegenNativeComponent<NativeProps>(
    'RNTMyNativeView'
): HostComponent<NativeProps>);
```

И обновите основной файл:

```js title="RNTMyNativeNativeComponent.js"
// @flow strict-local

export default require('./RNTMyNativeViewNativeComponent')
    .default;
```

### Переход от `dispatchViewManagerCommand`.

Как и в предыдущем случае, в попытке избежать вызова методов UIManager, все методы менеджера представлений теперь вызываются через экземпляр `NativeCommands`. `codegenNativeCommands` — это новый API для генерации кода `NativeCommands` на основе интерфейса команд вашего менеджера представлений.

**До**

```tsx
class MyComponent extends React.Component<Props> {
  _moveToRegion: (region: Region, duration: number) => {
    UIManager.dispatchViewManagerCommand(
      ReactNative.findNodeHandle(this),
      'moveToRegion',
      [region, duration]
    );
  }

  render() {
    return <MyCustomMapNativeComponent onPress={this._moveToRegion} />
  }
}
```

**Создание нативных команд с помощью `codegenNativeCommands`**

```js title="MyCustomMapNativeComponent.js"
// @flow strict-local

import codegenNativeCommands from 'react-native/Libraries/Utilities/codegenNativeCommands';
import type { HostComponent } from 'react-native/Libraries/Renderer/shims/ReactNativeTypes';

type MyCustomMapNativeComponentType = HostComponent<NativeProps>;

interface NativeCommands {
    +moveToRegion: (
        viewRef: React.ElementRef<MyCustomMapNativeComponentType>,
        region: MapRegion,
        duration: number
    ) => void;
}

export const Commands: NativeCommands = codegenNativeCommands<NativeCommands>(
    {
        supportedCommands: ['moveToRegion'],
    }
);
```

Примечание:

-   Первым аргументом команды `moveToRegion` является ссылка на HostComponent нативного компонента.
-   Аргументы команды `moveToRegion` перечислены в сигнатуре.
-   Определение команды располагается вместе с родным компонентом. Это поощряемый шаблон
-   Убедитесь, что вы включили имя своей команды в массив `supportedCommands`.

#### Использование вашей команды

```js
// @flow strict-local

import {Commands, ...} from './MyCustomMapNativeComponent';

class MyComponent extends React.Component<Props> {
  _ref: ?React.ElementRef<typeof MyCustomMapNativeComponent>;

  _captureRef: (ref: React.ElementRef<typeof MyCustomMapNativeComponent>) => {
    this._ref = ref;
  }

  _moveToRegion: (region: Region, duration: number) => {
    if (this._ref != null) {
      Commands.moveToRegion(this._ref, region, duration);
    }
  }

  render() {
    return <MyCustomMapNativeComponent
       ref={this._captureRef}
       onPress={this._moveToRegion} />
  }
}
```

#### Обновление нативной реализации

В примере сгенерированный код `Commands` отправит вызов `moveToRegion` в менеджер представлений нативного компонента. В дополнение к написанию JS-интерфейса, вам нужно будет обновить сигнатуры нативной реализации, чтобы они соответствовали диспетчеризированному вызову метода. Для справки смотрите отображение для [типов аргументов Android](https://facebook.github.io/react-native/docs/native-modules-android#argument-types) и [типов аргументов iOS](https://facebook.github.io/react-native/docs/native-modules-ios#argument-types).

**iOS**

```objc
RCT_EXPORT_METHOD(moveToRegion:(nonnull NSNumber *)reactTag
                        region:(NSDictionary *)region
                      duration:(double)duration
{
   ...
}
```

**Android**

=== "Kotlin"

    ```kotlin
    	fun receiveCommand(
    		view: ReactMapDrawerView?, commandId: String?, args: ReadableArray?
    	) {
    		when (commandId) {
    			"moveToRegion" -> {
    				if (args != null) {
    					val region: ReadableMap = args.getMap(0)
    					val durationMs: Int = args.getInt(1)
    					// ... act on the view...
    				}
    			}
    		}
    	}
    ```

=== "Java"

    ```java
    // receiveCommand signature has changed to receive String commandId
    @Override
    public void receiveCommand(
    	ReactMapDrawerView view, String commandId, @Nullable ReadableArray args) {
    	switch (commandId) {
    	case "moveToRegion":
    		if (args == null) {
    		break;
    		}

    		ReadableMap region = args.getMap(0);
    		int durationMs = args.getInt(1);
    		// ... act on the view...
    		break;
    	}
    }
    ```
