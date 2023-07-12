# Нативные модули Android

!!!info ""

    Native Module и Native Components - это наши стабильные технологии, используемые в унаследованной архитектуре. Они будут устаревшими в будущем, когда новая архитектура станет стабильной. Новая архитектура использует [Turbo Native Module](the-new-architecture-pillars-turbomodules.md) и [Fabric Native Components](the-new-architecture-pillars-fabric-components.md) для достижения аналогичных результатов.

Добро пожаловать в раздел "Нативные модули для Android". Пожалуйста, начните с чтения [Native Modules Intro](native-modules-intro), чтобы узнать, что такое нативные модули.

## Создание нативного модуля календаря

В следующем руководстве вы создадите нативный модуль `CalendarModule`, который позволит вам получить доступ к API календаря Android из JavaScript. В конце вы сможете вызывать `CalendarModule.createCalendarEvent('Dinner Party', 'My House');` из JavaScript, вызывая метод Java/Kotlin, который создает событие календаря.

### Установка

Чтобы начать работу, откройте проект Android внутри вашего приложения React Native в Android Studio. Вы можете найти свой проект Android здесь в приложении React Native:

<figure markdown>
![Image of opening up an Android project within a React Native app inside of Android Studio.](native-modules-android-open-project.png)
<figcaption>Image of where you can find your Android project</figcaption>
</figure>

Мы рекомендуем использовать Android Studio для написания нативного кода. Android Studio - это IDE, созданная для разработки Android, и ее использование поможет вам быстро решить мелкие проблемы, такие как синтаксические ошибки кода.

Мы также рекомендуем включить [Gradle Daemon](https://docs.gradle.org/2.9/userguide/gradle_daemon.html), чтобы ускорить сборку по мере итерации кода на Java/Kotlin.

### Создание файла пользовательского нативного модуля

Первым шагом будет создание Java/Kotlin файла (`CalendarModule.java` или `CalendarModule.kt`) в папке `android/app/src/main/java/com/your-app-name/` (папка одинакова для Kotlin и Java). Этот Java/Kotlin файл будет содержать ваш нативный модуль Java/Kotlin класса.

<figure markdown>
![Image of adding a class called CalendarModule.java within the Android Studio.](native-modules-android-add-class.png)
<figcaption>Image of how to add the CalendarModuleClass</figcaption>
</figure>

Затем добавьте следующее содержание:

=== "Java"

    ```java
    package com.your-apps-package-name; // replace your-apps-package-name with your app’s package name
    import com.facebook.react.bridge.NativeModule;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.bridge.ReactContext;
    import com.facebook.react.bridge.ReactContextBaseJavaModule;
    import com.facebook.react.bridge.ReactMethod;
    import java.util.Map;
    import java.util.HashMap;

    public class CalendarModule extends ReactContextBaseJavaModule {
    CalendarModule(ReactApplicationContext context) {
    	super(context);
    }
    }
    ```

=== "Kotlin"

    ```kotlin
    package com.your-apps-package-name; // replace your-apps-package-name with your app’s package name
    import com.facebook.react.bridge.NativeModule
    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.bridge.ReactContext
    import com.facebook.react.bridge.ReactContextBaseJavaModule
    import com.facebook.react.bridge.ReactMethod

    class CalendarModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {...}
    ```

Как вы видите, ваш класс `CalendarModule` расширяет класс `ReactContextBaseJavaModule`. Для Android нативные модули Java/Kotlin пишутся как классы, которые расширяют `ReactContextBaseJavaModule` и реализуют функциональность, требуемую JavaScript.

!!!info ""

    Стоит отметить, что технически классы Java/Kotlin должны расширять класс `BaseJavaModule` или реализовывать интерфейс `NativeModule`, чтобы считаться нативным модулем в React Native.

    Однако мы рекомендуем использовать `ReactContextBaseJavaModule`, как показано выше. `ReactContextBaseJavaModule` предоставляет доступ к `ReactApplicationContext` (RAC), что полезно для нативных модулей, которым необходимо подключаться к методам жизненного цикла активности. Использование `ReactContextBaseJavaModule` также упростит задачу обеспечения типобезопасности вашего нативного модуля в будущем. Для обеспечения безопасности типов нативных модулей, которая появится в будущих релизах, React Native просматривает спецификацию JavaScript каждого нативного модуля и генерирует абстрактный базовый класс, который расширяет `ReactContextBaseJavaModule`.

### Имя модуля

Все нативные модули Java/Kotlin в Android должны реализовывать метод `getName()`. Этот метод возвращает строку, которая представляет собой имя нативного модуля. Затем к нативному модулю можно обратиться в JavaScript, используя его имя. Например, в приведенном ниже фрагменте кода `getName()` возвращает `"CalendarModule"`.

=== "Java"

    ```java
    // add to CalendarModule.java
    @Override
    public String getName() {
    	return "CalendarModule";
    }
    ```

=== "Kotlin"

    ```kotlin
    // add to CalendarModule.kt
    override fun getName() = "CalendarModule"
    ```

Затем к нативному модулю можно получить доступ в JS следующим образом:

```ts
const { CalendarModule } = ReactNative.NativeModules;
```

### Экспорт нативного метода в JavaScript

Далее вам нужно будет добавить метод в ваш нативный модуль, который будет создавать события календаря и может быть вызван в JavaScript. Все методы нативного модуля, предназначенные для вызова из JavaScript, должны быть аннотированы `@ReactMethod`.

Создайте метод `createCalendarEvent()` для `CalendarModule`, который может быть вызван в JS через `CalendarModule.createCalendarEvent()`. На данный момент метод принимает имя и местоположение в виде строк. Варианты типов аргументов будут рассмотрены в ближайшее время.

=== "Java"

    ```java
    @ReactMethod
    	public void createCalendarEvent(String name, String location) {
    }
    ```

=== "Kotlin"

    ```kotlin
    @ReactMethod fun createCalendarEvent(name: String, location: String) {}
    ```

Добавьте отладочный журнал в метод, чтобы подтвердить, что он был вызван, когда вы вызываете его из своего приложения. Ниже приведен пример того, как можно импортировать и использовать класс [Log](https://developer.android.com/reference/android/util/Log) из пакета Android util:

=== "Java"

    ```java
    import android.util.Log;

    @ReactMethod
    public void createCalendarEvent(String name, String location) {
    	Log.d("CalendarModule", "Create event called with name: " + name
    	+ " and location: " + location);
    }
    ```

=== "Kotlin"

    ```kotlin
    import android.util.Log

    @ReactMethod
    fun createCalendarEvent(name: String, location: String) {
    	Log.d("CalendarModule", "Create event called with name: $name and location: $location")
    }
    ```

Как только вы завершите реализацию нативного модуля и подключите его в JavaScript, вы можете выполнить [эти шаги](https://developer.android.com/studio/debug/am-logcat.html) для просмотра журналов вашего приложения.

### Синхронные методы

Вы можете передать `isBlockingSynchronousMethod = true` нативному методу, чтобы пометить его как синхронный метод.

=== "Java"

    ```java
    @ReactMethod(isBlockingSynchronousMethod = true)
    ```

=== "Kotlin"

    ```kotlin
    @ReactMethod(isBlockingSynchronousMethod = true)
    ```

В настоящее время мы не рекомендуем этого делать, поскольку синхронный вызов методов может привести к значительным потерям производительности и внести ошибки, связанные с потоками, в ваши родные модули. Кроме того, обратите внимание, что если вы решите включить `isBlockingSynchronousMethod`, ваше приложение больше не сможет использовать отладчик Google Chrome. Это связано с тем, что синхронные методы требуют, чтобы JS VM делила память с приложением. Для отладчика Google Chrome, React Native работает внутри JS VM в Google Chrome и асинхронно взаимодействует с мобильными устройствами через WebSockets.

### Регистрация модуля (специфика Android)

После того как нативный модуль написан, его необходимо зарегистрировать в React Native. Для этого необходимо добавить нативный модуль в `ReactPackage` и зарегистрировать `ReactPackage` в React Native. Во время инициализации React Native перебирает все пакеты и для каждого `ReactPackage` регистрирует каждый нативный модуль.

React Native вызывает метод `createNativeModules()` на `ReactPackage`, чтобы получить список нативных модулей для регистрации. Для Android, если модуль не инстанцирован и не возвращен в createNativeModules, он не будет доступен из JavaScript.

Чтобы добавить ваш нативный модуль в `ReactPackage`, сначала создайте новый Java/Kotlin класс с именем (`MyAppPackage.java` или `MyAppPackage.kt`), который реализует `ReactPackage` в папке `android/app/src/main/java/com/your-app-name/`:

Затем добавьте следующее содержимое:

=== "Java"

    ```java
    package com.your-app-name; // replace your-app-name with your app’s name
    import com.facebook.react.ReactPackage;
    import com.facebook.react.bridge.NativeModule;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.uimanager.ViewManager;

    import java.util.ArrayList;
    import java.util.Collections;
    import java.util.List;

    public class MyAppPackage implements ReactPackage {

    	@Override
    	public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    		return Collections.emptyList();
    	}

    	@Override
    	public List<NativeModule> createNativeModules(
    			ReactApplicationContext reactContext) {
    		List<NativeModule> modules = new ArrayList<>();

    		modules.add(new CalendarModule(reactContext));

    		return modules;
    	}
    }
    ```

=== "Kotlin"

    ```kotlin
    package com.your-app-name // replace your-app-name with your app’s name

    import android.view.View
    import com.facebook.react.ReactPackage
    import com.facebook.react.bridge.NativeModule
    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.uimanager.ReactShadowNode
    import com.facebook.react.uimanager.ViewManager

    class MyAppPackage : ReactPackage {

    	override fun createViewManagers(
    		reactContext: ReactApplicationContext
    	): MutableList<ViewManager<View, ReactShadowNode<*>>> = mutableListOf()

    	override fun createNativeModules(
    		reactContext: ReactApplicationContext
    	): MutableList<NativeModule> = listOf(CalendarModule(reactContext)).toMutableList()
    }
    ```

Этот файл импортирует созданный вами нативный модуль `CalendarModule`. Затем он инстанцирует `CalendarModule` в функции `createNativeModules()` и возвращает его в виде списка `NativeModules` для регистрации. Если в дальнейшем вы будете добавлять дополнительные нативные модули, вы также можете инстанцировать их и добавить в возвращаемый список.

> Стоит отметить, что такой способ регистрации нативных модулей инициализирует все нативные модули при запуске приложения, что увеличивает время запуска приложения. В качестве альтернативы можно использовать [TurboReactPackage](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/TurboReactPackage.java). Вместо `createNativeModules`, который возвращает список инстанцированных объектов нативных модулей, TurboReactPackage реализует метод `getModule(String name, ReactApplicationContext rac)`, который создает объект нативного модуля, когда это необходимо. TurboReactPackage на данный момент реализован немного сложнее. Помимо реализации метода `getModule()`, необходимо реализовать метод `getReactModuleInfoProvider()`, который возвращает список всех нативных модулей, которые пакет может инстанцировать, вместе с функцией, которая их инстанцирует, пример [здесь](https://github.com/facebook/react-native/blob/8ac467c51b94c82d81930b4802b2978c85539925/ReactAndroid/src/main/java/com/facebook/react/CoreModulesPackage.java#L86-L165). Опять же, использование TurboReactPackage позволит вашему приложению иметь более быстрое время запуска, но в настоящее время он немного громоздок в написании. Поэтому будьте осторожны, если решите использовать TurboReactPackages.

Чтобы зарегистрировать пакет `CalendarModule`, необходимо добавить `MyAppPackage` в список пакетов, возвращаемых методом `getPackages()` от ReactNativeHost. Откройте файл `MainApplication.java` или `MainApplication.kt`, который можно найти по следующему пути: `android/app/src/main/java/com/your-app-name/`.

Найдите метод ReactNativeHost `getPackages()` и добавьте свой пакет в список пакетов, который возвращает `getPackages()`:

=== "Java"

    ```java
    @Override
    protected List<ReactPackage> getPackages() {
    	@SuppressWarnings("UnnecessaryLocalVariable")
    	List<ReactPackage> packages = new PackageList(this).getPackages();
    	// below MyAppPackage is added to the list of packages returned
    	packages.add(new MyAppPackage());
    	return packages;
    }
    ```

=== "Kotlin"

    ```kotlin
    override fun getPackages(): List<ReactPackage> =
    	PackageList(this).packages.apply {
    		// Packages that cannot be autolinked yet can be added manually here, for example:
    		// packages.add(new MyReactNativePackage());
    		add(MyAppPackage())
    	}
    ```

Теперь вы успешно зарегистрировали свой нативный модуль для Android!

### Протестируйте то, что вы создали

На данном этапе вы создали базовые подмостки для вашего нативного модуля в Android. Проверьте это, обратившись к родному модулю и вызвав его экспортированный метод в JavaScript.

Найдите место в вашем приложении, где вы хотите добавить вызов метода `createCalendarEvent()` родного модуля. Ниже приведен пример компонента `NewModuleButton`, который вы можете добавить в свое приложение. Вы можете вызвать нативный модуль внутри функции `NewModuleButton` `onPress()`.

```ts
import React from 'react';
import { NativeModules, Button } from 'react-native';

const NewModuleButton = () => {
    const onPress = () => {
        console.log(
            'We will invoke the native module here!'
        );
    };

    return (
        <Button
            title="Click to invoke your native module!"
            color="#841584"
            onPress={onPress}
        />
    );
};

export default NewModuleButton;
```

Чтобы получить доступ к нативному модулю из JavaScript, сначала нужно импортировать `NativeModules` из React Native:

```ts
import { NativeModules } from 'react-native';
```

Затем вы можете получить доступ к нативному модулю `CalendarModule` из `NativeModules`.

```ts
const { CalendarModule } = NativeModules;
```

Теперь, когда у вас есть нативный модуль CalendarModule, вы можете вызвать нативный метод `createCalendarEvent()`. Ниже он добавлен в метод `onPress()` в `NewModuleButton`:

```ts
const onPress = () => {
    CalendarModule.createCalendarEvent(
        'testName',
        'testLocation'
    );
};
```

Последний шаг - пересобрать приложение React Native, чтобы у вас был доступен самый свежий нативный код (с вашим новым нативным модулем!). В командной строке, где находится приложение react native, выполните следующее:

```shell
npx react-native run-android
```

### Создание по мере итерации

По мере того, как вы будете работать по этим руководствам и итерационно дорабатывать свой родной модуль, вам нужно будет перестроить ваше приложение, чтобы получить доступ к последним изменениям из JavaScript. Это связано с тем, что код, который вы пишете, находится в нативной части вашего приложения. В то время как metro bundler в React Native может следить за изменениями в JavaScript и перестраивать его на лету, он не будет делать этого для нативного кода. Поэтому, если вы хотите протестировать последние изменения в нативном коде, вам нужно перестроиться с помощью команды `npx react-native run-android`.

### Recap✨

Теперь вы должны иметь возможность вызвать метод `createCalendarEvent()` на вашем нативном модуле в приложении. В нашем примере это происходит при нажатии кнопки `NewModuleButton`. Вы можете подтвердить это, просмотрев журнал, который вы установили в методе `createCalendarEvent()`. Вы можете выполнить [эти шаги](https://developer.android.com/studio/debug/am-logcat.html) для просмотра журналов ADB в вашем приложении. Вы сможете найти сообщение `Log.d` (в нашем примере "Create event called with name: testName and location: testLocation") и увидеть, что ваше сообщение записывается в журнал каждый раз, когда вы вызываете метод родного модуля.

<figure markdown>
![Recap✨](native-modules-android-logs.png)
<figcaption>Изображение журналов ADB в Android Studio</figcaption>
</figure>

На данном этапе вы создали нативный модуль Android и вызвали его нативный метод из JavaScript в своем приложении React Native. Вы можете прочитать дальше, чтобы узнать больше о таких вещах, как типы аргументов, доступных для метода нативного модуля, и как настроить обратные вызовы и обещания.

## За пределами нативного модуля календаря

### Улучшенный экспорт нативного модуля

Импорт нативного модуля путем извлечения его из `NativeModules`, как описано выше, немного неудобен.

Чтобы избавить потребителей вашего нативного модуля от необходимости делать это каждый раз, когда они хотят получить доступ к вашему нативному модулю, вы можете создать JavaScript-обертку для модуля. Создайте новый файл JavaScript с именем `CalendarModule.js` со следующим содержимым:

```ts
/**
* This exposes the native CalendarModule module as a JS module. This has a
* function 'createCalendarEvent' which takes the following parameters:

* 1. String name: A string representing the name of the event
* 2. String location: A string representing the location of the event
*/
import { NativeModules } from 'react-native';
const { CalendarModule } = NativeModules;
export default CalendarModule;
```

Этот файл JavaScript также становится хорошим местом для добавления любой функциональности на стороне JavaScript. Например, если вы используете систему типов, такую как TypeScript, вы можете добавить сюда аннотации типов для вашего нативного модуля. Хотя React Native пока не поддерживает безопасность типов Native to JS, весь ваш JS-код будет безопасен для типов. Это также облегчит вам переход на безопасные для типов нативные модули в будущем. Ниже приведен пример добавления безопасности типов в модуль CalendarModule:

```ts
/**
 * This exposes the native CalendarModule module as a JS module. This has a
 * function 'createCalendarEvent' which takes the following parameters:
 *
 * 1. String name: A string representing the name of the event
 * 2. String location: A string representing the location of the event
 */
import { NativeModules } from 'react-native';
const { CalendarModule } = NativeModules;
interface CalendarInterface {
    createCalendarEvent(
        name: string,
        location: string
    ): void;
}
export default CalendarModule as CalendarInterface;
```

В других файлах JavaScript вы можете обратиться к родному модулю и вызвать его метод следующим образом:

```ts
import CalendarModule from './CalendarModule';
CalendarModule.createCalendarEvent('foo', 'bar');
```

!!!info ""

    Это предполагает, что место, куда вы импортируете `CalendarModule`, находится в той же иерархии, что и `CalendarModule.js`. Пожалуйста, обновите относительный импорт при необходимости.

### Типы аргументов

Когда метод нативного модуля вызывается на JavaScript, React Native преобразует аргументы из JS-объектов в их аналоги из Java/Kotlin. Так, например, если ваш метод нативного модуля Java принимает двойку, в JS вам нужно вызвать метод с числом. React Native выполнит преобразование за вас. Ниже приведен список типов аргументов, поддерживаемых методами нативного модуля, и их JavaScript-эквивалентов.

| Java          | Kotlin        | JavaScript |
| ------------- | ------------- | ---------- |
| Boolean       | Boolean       | ?boolean   |
| boolean       |               | boolean    |
| Double        | Double        | ?number    |
| double        |               | number     |
| String        | String        | string     |
| Callback      | Callback      | Function   |
| ReadableMap   | ReadableMap   | Object     |
| ReadableArray | ReadableArray | Array      |

!!!info ""

    Следующие типы поддерживаются в настоящее время, но не будут поддерживаться в TurboModules. Пожалуйста, избегайте их использования:

    -   Integer Java/Kotlin --> ?number
    -   Float Java/Kotlin --> ?число
    -   int Java --> число
    -   float Java --> число

Для типов аргументов, не перечисленных выше, вам придется самостоятельно выполнять преобразование. Например, в Android преобразование `Date` не поддерживается из коробки. Вы можете самостоятельно выполнить преобразование к типу `Date` в родном методе следующим образом:

=== "Java"

    ```java
    	String dateFormat = "yyyy-MM-dd";
    	SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
    	Calendar eStartDate = Calendar.getInstance();
    	try {
    		eStartDate.setTime(sdf.parse(startDate));
    	}
    ```

=== "Kotlin"

    ```kotlin
    	val dateFormat = "yyyy-MM-dd"
    	val sdf = SimpleDateFormat(dateFormat, Locale.US)
    	val eStartDate = Calendar.getInstance()
    	try {
    		sdf.parse(startDate)?.let {
    			eStartDate.time = it
    		}
    	}
    ```

### Экспорт констант

Родной модуль может экспортировать константы, реализуя родной метод `getConstants()`, который доступен в JS. Ниже вы реализуете `getConstants()` и вернете Map, содержащий константу `DEFAULT_EVENT_NAME`, к которой можно получить доступ в JavaScript:

=== "Java"

    ```java
    @Override
    public Map<String, Object> getConstants() {
    final Map<String, Object> constants = new HashMap<>();
    constants.put("DEFAULT_EVENT_NAME", "New Event");
    return constants;
    }
    ```

=== "Kotlin"

    ```kotlin
    override fun getConstants(): MutableMap<String, Any> =
    	hashMapOf("DEFAULT_EVENT_NAME" to "New Event")
    ```

Затем к константе можно получить доступ, вызвав `getConstants` на родном модуле в JS:

```ts
const {
    DEFAULT_EVENT_NAME,
} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

Технически можно получить доступ к константам, экспортируемым в `getConstants()` непосредственно из объекта родного модуля. Это больше не будет поддерживаться в TurboModules, поэтому мы призываем сообщество перейти на описанный выше подход, чтобы избежать необходимой миграции в будущем.

!!!info ""

    В настоящее время константы экспортируются только во время инициализации, поэтому если вы измените значения getConstants во время выполнения, это не повлияет на среду JavaScript. Это изменится с появлением Turbomodules. С Turbomodules, `getConstants()` станет обычным методом нативного модуля, и каждый вызов будет бить по нативной стороне.

### Обратные вызовы

Нативные модули также поддерживают уникальный вид аргумента: обратный вызов. Обратные вызовы используются для передачи данных из Java/Kotlin в JavaScript для асинхронных методов. Они также могут быть использованы для асинхронного выполнения JavaScript с нативной стороны.

Чтобы создать метод нативного модуля с обратным вызовом, сначала импортируйте интерфейс `Callback`, а затем добавьте новый параметр в метод нативного модуля типа `Callback`. Есть несколько нюансов с аргументами обратного вызова, которые вскоре будут устранены в TurboModules. Во-первых, вы можете иметь только два обратных вызова в аргументах функции - successCallback и failureCallback. Кроме того, последний аргумент вызова метода нативного модуля, если это функция, рассматривается как successCallback, а предпоследний аргумент вызова метода нативного модуля, если это функция, рассматривается как failure Callback.

=== "Java"

    ```java
    import com.facebook.react.bridge.Callback;

    @ReactMethod
    public void createCalendarEvent(String name, String location, Callback callBack) {
    }
    ```

=== "Kotlin"

    ```kotlin
    import com.facebook.react.bridge.Callback

    @ReactMethod fun createCalendarEvent(name: String, location: String, callback: Callback) {}
    ```

Вы можете вызвать обратный вызов в своем методе Java/Kotlin, предоставляя любые данные, которые вы хотите передать JavaScript. Обратите внимание, что вы можете передавать только сериализуемые данные из нативного кода в JavaScript. Если вам нужно передать обратно нативный объект, вы можете использовать `WriteableMaps`, если вам нужно использовать коллекцию, используйте `WritableArrays`. Также важно отметить, что обратный вызов не вызывается сразу после завершения нативной функции. Ниже в обратный вызов передается ID события, созданного в предыдущем вызове.

=== "Java"

    ```java
    @ReactMethod
    public void createCalendarEvent(String name, String location, Callback callBack) {
    	Integer eventId = ...
    	callBack.invoke(eventId);
    }
    ```

=== "Kotlin"

    ```kotlin
    @ReactMethod
    fun createCalendarEvent(name: String, location: String, callback: Callback) {
    	val eventId = ...
    	callback.invoke(eventId)
    }
    ```

Затем к этому методу можно получить доступ в JavaScript, используя:

```ts
const onPress = () => {
    CalendarModule.createCalendarEvent(
        'Party',
        'My House',
        (eventId) => {
            console.log(
                `Created a new event with id ${eventId}`
            );
        }
    );
};
```

Еще одна важная деталь, которую следует отметить, заключается в том, что метод родного модуля может вызывать только один обратный вызов, один раз. Это означает, что вы можете вызвать либо обратный вызов успеха, либо обратный вызов неудачи, но не оба, и каждый обратный вызов может быть вызван не более одного раза. Родной модуль может, однако, сохранить обратный вызов и вызвать его позже.

Существует два подхода к обработке ошибок с помощью обратных вызовов. Первый - следовать соглашению Node и рассматривать первый аргумент, переданный обратному вызову, как объект ошибки.

=== "Java"

    ```java
    @ReactMethod
    public void createCalendarEvent(String name, String location, Callback callBack) {
    	Integer eventId = ...
    	callBack.invoke(null, eventId);
    }
    ```

=== "Kotlin"

    ```kotlin
    @ReactMethod
    fun createCalendarEvent(name: String, location: String, callback: Callback) {
    	val eventId = ...
    	callback.invoke(null, eventId)
    }
    ```

В JavaScript вы можете проверить первый аргумент, чтобы узнать, была ли передана ошибка:

```ts
const onPress = () => {
    CalendarModule.createCalendarEvent(
        'testName',
        'testLocation',
        (error, eventId) => {
            if (error) {
                console.error(`Error found! ${error}`);
            }
            console.log(`event id ${eventId} returned`);
        }
    );
};
```

Другой вариант - использовать обратный вызов `onSuccess` и `onFailure`:

=== "Java"

    ```java
    @ReactMethod
        public void createCalendarEvent(
            String name,
            String location,
            Callback myFailureCallback,
            Callback mySuccessCallback
        ) {
    }
    ```

=== "Kotlin"

    ```kotlin
    @ReactMethod
    fun createCalendarEvent(
        name: String,
        location: String,
        myFailureCallback: Callback,
        mySuccessCallback: Callback
    ) {}
    ```

Затем в JavaScript вы можете добавить отдельный обратный вызов для ответов об ошибке и успехе:

```ts
const onPress = () => {
    CalendarModule.createCalendarEvent(
        'testName',
        'testLocation',
        (error) => {
            console.error(`Error found! ${error}`);
        },
        (eventId) => {
            console.log(`event id ${eventId} returned`);
        }
    );
};
```

### Промисы

Нативные модули также могут выполнять [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise), что может упростить ваш JavaScript, особенно при использовании синтаксиса [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) ES2016. Когда последним параметром метода нативного модуля Java/Kotlin является Promise, его соответствующий JS-метод вернет JS-объект Promise.

Рефакторинг приведенного выше кода для использования обещания вместо обратных вызовов выглядит следующим образом:

=== "Java"

    ```java
    import com.facebook.react.bridge.Promise;

    @ReactMethod
    public void createCalendarEvent(String name, String location, Promise promise) {
        try {
            Integer eventId = ...
            promise.resolve(eventId);
        } catch(Exception e) {
            promise.reject("Create Event Error", e);
        }
    }
    ```

=== "Kotlin"

    ```kotlin
    @ReactMethod
    fun createCalendarEvent(name: String, location: String, promise: Promise) {
        try {
            val eventId = ...
            promise.resolve(eventId)
        } catch (e: Throwable) {
            promise.reject("Create Event Error", e)
        }
    }
    ```

!!!info ""

    Подобно обратным вызовам, метод родного модуля может либо отклонить, либо разрешить обещание (но не оба) и может сделать это не более одного раза. Это означает, что вы можете вызвать либо обратный вызов успеха, либо обратный вызов отказа, но не оба, и каждый обратный вызов может быть вызван не более одного раза. Родной модуль может, однако, сохранить обратный вызов и вызвать его позже.

JavaScript-аналог этого метода возвращает `Promise`. Это означает, что вы можете использовать ключевое слово `await` в асинхронной функции для вызова этого метода и ожидания его результата:

```ts
const onSubmit = async () => {
    try {
        const eventId = await CalendarModule.createCalendarEvent(
            'Party',
            'My House'
        );
        console.log(
            `Created a new event with id ${eventId}`
        );
    } catch (e) {
        console.error(e);
    }
};
```

Метод reject принимает различные комбинации следующих аргументов:

=== "Java"

    ```java
    String code, String message, WritableMap userInfo, Throwable throwable
    ```

=== "Kotlin"

    ```kotlin
    code: String, message: String, userInfo: WritableMap, throwable: Throwable
    ```

Более подробную информацию вы можете найти в интерфейсе `Promise.java` [здесь](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/bridge/Promise.java). Если `userInfo` не предоставлен, ReactNative установит его в null. Для остальных параметров React Native будет использовать значение по умолчанию. Аргумент `message` предоставляет `сообщение об ошибке`, отображаемое в верхней части стека вызовов ошибок. Ниже приведен пример сообщения об ошибке, показанного в JavaScript после следующего вызова reject на Java/Kotlin.

Вызов отказа на Java/Kotlin:

=== "Java"

    ```java
    promise.reject("Create Event error", "Error parsing date", e);
    ```

=== "Kotlin"

    ```kotlin
    promise.reject("Create Event error", "Error parsing date", e)
    ```

Сообщение об ошибке в React Native App при отклонении обещания:

<figure markdown>
![Промисы](native-modules-android-errorscreen.png)
<figcaption>Изображение сообщения об ошибке</figcaption>
</figure>

### Отправка событий на JavaScript

Нативные модули могут сигнализировать JavaScript о событиях, не вызывая их напрямую. Например, вы можете захотеть передать в JavaScript напоминание о том, что скоро произойдет событие календаря из родного календарного приложения Android. Самый простой способ сделать это - использовать `RCTDeviceEventEmitter`, который можно получить из `ReactContext`, как в приведенном ниже фрагменте кода.

=== "Java"

    ```java
    ...
    import com.facebook.react.modules.core.DeviceEventManagerModule;
    import com.facebook.react.bridge.WritableMap;
    import com.facebook.react.bridge.Arguments;
    ...
    private void sendEvent(ReactContext reactContext,
                        String eventName,
                        @Nullable WritableMap params) {
    reactContext
        .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
        .emit(eventName, params);
    }

    private int listenerCount = 0;

    @ReactMethod
    public void addListener(String eventName) {
    if (listenerCount == 0) {
        // Set up any upstream listeners or background tasks as necessary
    }

    listenerCount += 1;
    }

    @ReactMethod
    public void removeListeners(Integer count) {
    listenerCount -= count;
    if (listenerCount == 0) {
        // Remove upstream listeners, stop unnecessary background tasks
    }
    }
    ...
    WritableMap params = Arguments.createMap();
    params.putString("eventProperty", "someValue");
    ...
    sendEvent(reactContext, "EventReminder", params);
    ```

=== "Kotlin"

    ```kotlin
    ...
    import com.facebook.react.bridge.WritableMap
    import com.facebook.react.bridge.Arguments
    import com.facebook.react.modules.core.DeviceEventManagerModule
    ...

    private fun sendEvent(reactContext: ReactContext, eventName: String, params: WritableMap?) {
        reactContext
        .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter::class.java)
        .emit(eventName, params)
    }

    private var listenerCount = 0

    @ReactMethod
    fun addListener(eventName: String) {
    if (listenerCount == 0) {
        // Set up any upstream listeners or background tasks as necessary
    }

    listenerCount += 1
    }

    @ReactMethod
    fun removeListeners(count: Int) {
    listenerCount -= count
    if (listenerCount == 0) {
        // Remove upstream listeners, stop unnecessary background tasks
    }
    }
    ...
    val params = Arguments.createMap().apply {
        putString("eventProperty", "someValue")
    }
    ...
    sendEvent(reactContext, "EventReminder", params)
    ```

Затем модули JavaScript могут зарегистрироваться для получения событий путем `addListener` на классе [NativeEventEmitter](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/EventEmitter/NativeEventEmitter.js).

```ts
import {
    NativeEventEmitter,
    NativeModules,
} from 'react-native';
/// ...
useEffect(() => {
    const eventEmitter = new NativeEventEmitter(
        NativeModules.ToastExample
    );
    let eventListener = eventEmitter.addListener(
        'EventReminder',
        (event) => {
            console.log(event.eventProperty); // "someValue"
        }
    );

    // Removes the listener once unmounted
    return () => {
        eventListener.remove();
    };
}, []);
```

### Получение результатов деятельности от startActivityForResult

Вам нужно прослушать `onActivityResult`, если вы хотите получить результаты деятельности, которую вы начали с помощью `startActivityForResult`. Для этого вы должны расширить `BaseActivityEventListener` или реализовать `ActivityEventListener`. Первый вариант предпочтительнее, так как он более устойчив к изменениям API. Затем необходимо зарегистрировать слушателя в конструкторе модуля следующим образом:

=== "Java"

    ```java
    reactContext.addActivityEventListener(mActivityResultListener);
    ```

=== "Kotlin"

    ```kotlin
    reactContext.addActivityEventListener(mActivityResultListener);
    ```

Теперь вы можете прослушивать `onActivityResult`, реализовав следующий метод:

=== "Java"

    ```java
    @Override
    public void onActivityResult(
        final Activity activity,
        final int requestCode,
        final int resultCode,
        final Intent intent) {
        // Your logic here
    }
    ```

=== "Kotlin"

    ```kotlin
    override fun onActivityResult(
        activity: Activity?,
        requestCode: Int,
        resultCode: Int,
        intent: Intent?
    ) {
        // Your logic here
    }
    ```

Чтобы продемонстрировать это, давайте реализуем базовый подборщик изображений. Программа выбора изображений будет предоставлять JavaScript метод `pickImage`, который при вызове будет возвращать путь к изображению.

=== "Java"

    ```java
    public class ImagePickerModule extends ReactContextBaseJavaModule {

    private static final int IMAGE_PICKER_REQUEST = 1;
    private static final String E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST";
    private static final String E_PICKER_CANCELLED = "E_PICKER_CANCELLED";
    private static final String E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER";
    private static final String E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND";

    private Promise mPickerPromise;

    private final ActivityEventListener mActivityEventListener = new BaseActivityEventListener() {

        @Override
        public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent intent) {
            if (requestCode == IMAGE_PICKER_REQUEST) {
                if (mPickerPromise != null) {
                if (resultCode == Activity.RESULT_CANCELED) {
                    mPickerPromise.reject(E_PICKER_CANCELLED, "Image picker was cancelled");
                } else if (resultCode == Activity.RESULT_OK) {
                    Uri uri = intent.getData();

                    if (uri == null) {
                        mPickerPromise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found");
                    } else {
                        mPickerPromise.resolve(uri.toString());
                    }
                }

                mPickerPromise = null;
                }
            }
        }
    };

    ImagePickerModule(ReactApplicationContext reactContext) {
        super(reactContext);

        // Add the listener for `onActivityResult`
        reactContext.addActivityEventListener(mActivityEventListener);
    }

    @Override
    public String getName() {
        return "ImagePickerModule";
    }

    @ReactMethod
    public void pickImage(final Promise promise) {
        Activity currentActivity = getCurrentActivity();

        if (currentActivity == null) {
        promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist");
        return;
        }

        // Store the promise to resolve/reject when picker returns data
        mPickerPromise = promise;

        try {
        final Intent galleryIntent = new Intent(Intent.ACTION_PICK);

        galleryIntent.setType("image/*");

        final Intent chooserIntent = Intent.createChooser(galleryIntent, "Pick an image");

        currentActivity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST);
        } catch (Exception e) {
        mPickerPromise.reject(E_FAILED_TO_SHOW_PICKER, e);
        mPickerPromise = null;
        }
    }
    }
    ```

=== "Kotlin"

    ```kotlin
    class ImagePickerModule(reactContext: ReactApplicationContext) :
        ReactContextBaseJavaModule(reactContext) {

        private var pickerPromise: Promise? = null

        private val activityEventListener =
            object : BaseActivityEventListener() {
                override fun onActivityResult(
                    activity: Activity?,
                    requestCode: Int,
                    resultCode: Int,
                    intent: Intent?
                ) {
                    if (requestCode == IMAGE_PICKER_REQUEST) {
                        pickerPromise?.let { promise ->
                            when (resultCode) {
                                Activity.RESULT_CANCELED ->
                                    promise.reject(E_PICKER_CANCELLED, "Image picker was cancelled")
                                Activity.RESULT_OK -> {
                                    val uri = intent?.data

                                    uri?.let { promise.resolve(uri.toString())}
                                        ?: promise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found")
                                }
                            }

                            pickerPromise = null
                        }
                    }
                }
            }

        init {
            reactContext.addActivityEventListener(activityEventListener)
        }

        override fun getName() = "ImagePickerModule"

        @ReactMethod
        fun pickImage(promise: Promise) {
            val activity = currentActivity

            if (activity == null) {
                promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist")
                return
            }

            pickerPromise = promise

            try {
                val galleryIntent = Intent(Intent.ACTION_PICK).apply { type = "image\/*" }

                val chooserIntent = Intent.createChooser(galleryIntent, "Pick an image")

                activity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST)
            } catch (t: Throwable) {
                pickerPromise?.reject(E_FAILED_TO_SHOW_PICKER, t)
                pickerPromise = null
            }
        }

        companion object {
            const val IMAGE_PICKER_REQUEST = 1
            const val E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST"
            const val E_PICKER_CANCELLED = "E_PICKER_CANCELLED"
            const val E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER"
            const val E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND"
        }
    }
    ```

### Прослушивание событий жизненного цикла

Прослушивание событий жизненного цикла активности, таких как `onResume`, `onPause` и т.д., очень похоже на то, как был реализован `ActivityEventListener`. Модуль должен реализовать `LifecycleEventListener`. Затем необходимо зарегистрировать слушателя в конструкторе модуля следующим образом:

=== "Java"

    ```java
    reactContext.addLifecycleEventListener(this);
    ```

=== "Kotlin"

    ```kotlin
    reactContext.addLifecycleEventListener(this)
    ```

Теперь вы можете прослушивать события LifeCycle активности, реализуя следующие методы:

=== "Java"

    ```java
    @Override
    public void onHostResume() {
    // Activity `onResume`
    }
    @Override
    public void onHostPause() {
    // Activity `onPause`
    }
    @Override
    public void onHostDestroy() {
    // Activity `onDestroy`
    }
    ```

=== "Kotlin"

    ```kotlin
    override fun onHostResume() {
        // Activity `onResume`
    }

    override fun onHostPause() {
        // Activity `onPause`
    }

    override fun onHostDestroy() {
        // Activity `onDestroy`
    }
    ```

### Threading

На сегодняшний день в Android все асинхронные методы нативных модулей выполняются в одном потоке. Нативные модули не должны иметь никаких предположений о том, в каком потоке они вызываются, поскольку текущее назначение может измениться в будущем. Если требуется блокирующий вызов, то тяжелая работа должна быть передана внутреннему управляемому рабочему потоку, а все обратные вызовы распределяются оттуда.
