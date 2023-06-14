# Разрешение в библиотеке Android

!!!warning "Внимание"

    Эта документация все еще является экспериментальной, и детали могут быть изменены по мере итераций. Не стесняйтесь делиться своими отзывами в обсуждении внутри рабочей группы для этой страницы.

    Более того, она содержит несколько ручных шагов. Пожалуйста, обратите внимание, что это не будет представлять окончательный опыт разработчиков, когда Новая архитектура станет стабильной. Мы работаем над инструментами, шаблонами и библиотеками, которые помогут вам быстро начать работу с новой архитектурой без необходимости проходить всю процедуру настройки.

После того, как вы определили спецификации JavaScript для ваших родных модулей в рамках [предварительных условий](new-architecture-library-intro), настроили конфигурацию Codegen и выполнили настройку Android/Gradle, теперь вы готовы перенести вашу библиотеку на новую архитектуру. Вот шаги, которые вы можете выполнить для этого.

## 1. Расширить или реализовать сгенерированные кодом нативные интерфейсы

Спецификация JavaScript для вашего нативного модуля или компонента будет использоваться для генерации кода нативного интерфейса для каждой поддерживаемой платформы (т.е. Android и iOS). Эти файлы нативных интерфейсов будут генерироваться **когда будет создано приложение React Native, зависящее от вашей библиотеки**.

Хотя сгенерированный код нативного интерфейса **не будет поставляться как часть вашей библиотеки**, вам необходимо убедиться, что ваш код на Java/Kotlin соответствует протоколам, предоставляемым этими файлами нативного интерфейса.

Вы можете вызвать задачу `generateCodegenArtifactsFromSchema` Gradle для генерации кода нативного интерфейса вашей библиотеки, чтобы использовать их **в качестве ссылки:**.

```bash
./gradlew generateCodegenArtifactsFromSchema
```

Файлы, которые выводятся, можно найти внутри `build/generated/source/codegen` и **не должны быть зафиксированы**, но вам нужно будет обратиться к ним, чтобы определить, какие изменения нужно внести в ваши родные модули, чтобы они обеспечивали реализацию каждого сгенерированного интерфейса.

Вывод Codegen для модуля с именем `NativeAwesomeManager` будет выглядеть следующим образом:

```
app/build/generated/source/codegen
├── java
│   └── com
│       └── example
│           └── samplelibrary
│               └── NativeAwesomeManagerSpec.java
├── jni
│   ├── Android.mk
│   ├── CMakeLists.txt
│   ├── react
│   │   └── renderer
│   │       └── components
│   │           └── samplelibrary
│   │               ├── ComponentDescriptors.h
│   │               ├── EventEmitters.cpp
│   │               ├── EventEmitters.h
│   │               ├── Props.cpp
│   │               ├── Props.h
│   │               ├── ShadowNodes.cpp
│   │               └── ShadowNodes.h
│   ├── samplelibrary-generated.cpp
│   └── samplelibrary.h
└── schema.json
```

### Расширяет абстрактный класс, предоставленный кодогеном

Обновите ваш нативный модуль или компонент, чтобы убедиться, что он **расширяет абстрактный класс**, который был сгенерирован из ваших спецификаций JavaScript (т.е. файл `NativeAwesomeManagerSpec.java` из предыдущего примера).

Следуя примеру, приведенному в предыдущем разделе, ваша библиотека может импортировать `NativeAwesomeManagerSpec`, реализовать соответствующий "родной" интерфейс и необходимые методы для него:

=== "Java"

    ```java
    import androidx.annotation.NonNull;

    import com.example.samplelibrary.NativeAwesomeManagerSpec;
    import com.facebook.react.bridge.Promise;
    import com.facebook.react.bridge.ReactApplicationContext;

    public class NativeAwesomeManager extends NativeAwesomeManagerSpec {

    	public static final String NAME = "NativeAwesomeManager";

    	public NativeAwesomeManager(ReactApplicationContext reactContext) {
    		super(reactContext);
    	}

    	@Override
    	public void getString(String id, Promise promise) {
    		// Implement this method
    	}

    	@NonNull
    	@Override
    	public String getName() {
    		return NAME;
    	}
    }
    ```

=== "Kotlin"

    ```kotlin
    import com.example.samplelibrary.NativeAwesomeManagerSpec
    import com.facebook.react.bridge.Promise
    import com.facebook.react.bridge.ReactApplicationContext

    class NativeAwesomeManager(reactContext: ReactApplicationContext) :
    	NativeAwesomeManagerSpec(reactContext) {
    	override fun getString(id: String, promise: Promise) {
    		// Implement this method
    	}

    	override fun getName() = NAME

    	companion object {
    		val NAME = "NativeAwesomeManager"
    	}
    }
    ```

Обратите внимание, что **генерируемый абстрактный класс**, который вы сейчас расширяете (`MyAwesomeSpec` в данном примере), сам расширяет `ReactContextBaseJavaModule`. Поэтому вы не должны использовать доступ ни к одному из методов/полей, которые вы использовали ранее (например, `ReactApplicationContext` и так далее). Более того, созданный класс теперь также будет реализовывать интерфейс `TurboModule` для вас.
