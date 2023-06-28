# Интеграция с фрагментами Android

В руководстве [Интеграция с существующими приложениями](integration-with-existing-apps.md) подробно описано, как интегрировать полноэкранное приложение React Native в существующее приложение Android в качестве Activity. Для использования компонентов React Native в рамках Fragments в существующем приложении требуется дополнительная настройка. Преимущество этого способа заключается в том, что он позволяет интегрировать компоненты React Native в нативное приложение вместе с нативными фрагментами в Activity.

## 1. Добавьте React Native в ваше приложение

Следуйте руководству по [Интеграции с существующими приложениями](integration-with-existing-apps.md) до раздела интеграции кода. Продолжайте следовать Шагу 1. Создайте файл `index.android.js` и Шаг 2. Добавьте ваш код React Native из этого раздела.

## 2. Интеграция вашего приложения с фрагментом React Native

Вы можете отобразить ваш React Native компонент во фрагмент вместо полноэкранного React Native Activity. Компонент может называться "экран" или "фрагмент", и он будет функционировать так же, как и фрагмент Android, вероятно, содержащий дочерние компоненты. Эти компоненты могут быть помещены в папку `/fragments`, а дочерние компоненты, используемые для создания фрагмента, могут быть помещены в папку `/components`.

Вам нужно будет реализовать интерфейс `ReactApplication` в вашем основном классе Application Java/Kotlin. Если вы создали новый проект в Android Studio с активностью по умолчанию, вам нужно будет создать новый класс (например, `MyReactApplication.java` или `MyReactApplication.kt`). Если класс уже существует, вы можете найти этот основной класс в вашем файле `AndroidManifest.xml`. В теге `<application />` вы должны увидеть свойство `android:name`, например, `android:name=".MyReactApplication"`. Это значение является классом, который вы хотите реализовать и предоставить необходимые методы.

Убедитесь, что ваш основной класс Application реализует ReactApplication:

=== "Java"

    ``` java
    public class MyReactApplication extends Application implements ReactApplication {...}
    ```

=== "Kotlin"

    ``` kotlin
    class MyReactApplication: Application(), ReactApplication {...}
    ```

Переопределите необходимые методы `getUseDeveloperSupport`, `getPackages` и `getReactNativeHost`:

=== "Java"

    ```java
    public class MyReactApplication extends Application implements ReactApplication {
    	@Override
    	public void onCreate() {
    		super.onCreate();
    		SoLoader.init(this, false);
    	}

    	private final ReactNativeHost mReactNativeHost = new DefaultReactNativeHost(this) {
    		@Override
    		public boolean getUseDeveloperSupport() {
    			return BuildConfig.DEBUG;
    		}

    		protected List<ReactPackage> getPackages() {
    			List<ReactPackage> packages = new PackageList(this).getPackages();
    			// Packages that cannot be autolinked yet can be added manually here
    			return packages;
    		}
    	};

    	@Override
    	public ReactNativeHost getReactNativeHost() {
    		return mReactNativeHost;
    	}
    }
    ```

=== "Kotlin"

    ```kotlin
    class MyReactApplication : Application(), ReactApplication {
    	override fun onCreate() {
    		super.onCreate()
    		SoLoader.init(this, false)
    	}
    	private val reactNativeHost =
    		object : DefaultReactNativeHost(this) {
    			override fun getUseDeveloperSupport() = BuildConfig.DEBUG
    			override fun getPackages(): List<ReactPackage> {
    				val packages = PackageList(this).getPackages().toMutableList()
    				// Packages that cannot be autolinked yet can be added manually here
    				return packages
    			}
    		}
    	override fun getReactNativeHost(): ReactNativeHost = reactNativeHost
    }
    ```

Если вы используете Android Studio, используйте ++alt+enter++, чтобы добавить все недостающие импорты в ваш класс. В качестве альтернативы вот необходимые импорты, которые нужно включить вручную:

=== "Java"

    ```java
    import android.app.Application;

    import com.facebook.react.PackageList;
    import com.facebook.react.ReactApplication;
    import com.facebook.react.ReactNativeHost;
    import com.facebook.react.ReactPackage;
    import com.facebook.react.defaults.DefaultReactNativeHost;
    import com.facebook.soloader.SoLoader;

    import java.util.List;
    ```

=== "Kotlin"

    ```kotlin
    import android.app.Application

    import com.facebook.react.PackageList
    import com.facebook.react.ReactApplication
    import com.facebook.react.ReactNativeHost
    import com.facebook.react.ReactPackage
    import com.facebook.react.defaults.DefaultReactNativeHost
    import com.facebook.soloader.SoLoader
    ```

Выполните операцию "Синхронизировать файлы проекта с Gradle".

### Шаг 3. Добавьте фрейм-макет для фрагмента React Native

Теперь вы добавите ваш React Native фрагмент в Activity. Для нового проекта этой активностью будет `MainActivity`, но это может быть любая активность, и больше фрагментов могут быть добавлены в дополнительные активности по мере интеграции новых компонентов React Native в ваше приложение.

Сначала добавьте фрагмент React Native в макет вашей активности. Например, `main_activity.xml` в папке `res/layouts`.

Добавьте `<FrameLayout>` с id, шириной и высотой. Это макет, в котором вы найдете и отобразите ваш фрагмент React Native.

<!-- 0014.part.md -->

```xml
<FrameLayout
    android:id="@+id/reactNativeFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
/>
```

<!-- 0015.part.md -->

### Шаг 4. Добавление фрагмента React Native в макет FrameLayout

Чтобы добавить ваш React Native фрагмент в макет, вам нужно иметь Activity. Как уже говорилось, в новом проекте это будет `MainActivity`. В этой активности добавьте кнопку и слушатель событий. При нажатии на кнопку будет отображаться ваш React Native Fragment.

Измените макет вашей Activity, чтобы добавить кнопку:

<!-- 0016.part.md -->

```xml
<Button
    android:layout_margin="10dp"
    android:id="@+id/button"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Show react fragment"
/>
```

<!-- 0017.part.md -->

Теперь в классе Activity (например, `MainActivity.java` или `MainActivity.kt`) вам нужно добавить `OnClickListener` для кнопки, инстанцировать ваш `ReactFragment` и добавить его в макет фрейма.

Добавьте поле кнопки в верхнюю часть вашей Activity:

=== "Java"

    ```java
    private Button mButton;
    ```

=== "Kotlin"

    ```kotlin
    private lateinit var button: Button
    ```

Обновите метод `onCreate` вашей активности следующим образом:

=== "Java"

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
    	super.onCreate(savedInstanceState);
    	setContentView(R.layout.main_activity);

    	mButton = findViewById(R.id.button);
    	mButton.setOnClickListener(new View.OnClickListener() {
    		public void onClick(View v) {
    			Fragment reactNativeFragment = new ReactFragment.Builder()
    					.setComponentName("HelloWorld")
    					.setLaunchOptions(getLaunchOptions("test message"))
    					.build();

    			getSupportFragmentManager()
    					.beginTransaction()
    					.add(R.id.reactNativeFragment, reactNativeFragment)
    					.commit();

    		}
    	});
    }
    ```

=== "Kotlin"

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle) {
    	super.onCreate(savedInstanceState)
    	setContentView(R.layout.main_activity)
    	button = findViewById<Button>(R.id.button)
    	button.setOnClickListener {
    		val reactNativeFragment = ReactFragment.Builder()
    				.setComponentName("HelloWorld")
    				.setLaunchOptions(getLaunchOptions("test message"))
    				.build()
    		getSupportFragmentManager()
    				.beginTransaction()
    				.add(R.id.reactNativeFragment, reactNativeFragment)
    				.commit()
    	}
    }
    ```

В приведенном выше коде `Fragment reactNativeFragment = new ReactFragment.Builder()` создает ReactFragment, а `getSupportFragmentManager().beginTransaction().add()` добавляет фрагмент к макету фрейма.

Если вы используете стартовый набор для React Native, замените строку "HelloWorld" на ту, что находится в вашем файле `index.js` или `index.android.js` (это первый аргумент метода `AppRegistry.registerComponent()`).

Добавьте метод `getLaunchOptions`, который позволит вам передавать реквизиты компоненту. Это необязательно, и вы можете удалить `setLaunchOptions`, если вам не нужно передавать реквизиты.

=== "Java"

    ```java
    private Bundle getLaunchOptions(String message) {
    	Bundle initialProperties = new Bundle();
    	initialProperties.putString("message", message);
    	return initialProperties;
    }
    ```

=== "Kotlin"

    ```kotlin
    private fun getLaunchOptions(message: String) = Bundle().apply {
    	putString("message", message)
    }
    ```

Добавьте все недостающие импорты в ваш класс Activity. Будьте внимательны, используйте BuildConfig вашего пакета, а не тот, который был взят из пакета facebook! В качестве альтернативы, вот необходимые импорты, которые нужно включить вручную:

=== "Java"

```java
import android.app.Application;

import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.shell.MainReactPackage;
import com.facebook.soloader.SoLoader;
```

=== "Kotlin"

```kotlin
import android.app.Application

import com.facebook.react.ReactApplication
import com.facebook.react.ReactNativeHost
import com.facebook.react.ReactPackage
import com.facebook.react.shell.MainReactPackage
import com.facebook.soloader.SoLoader
```

Выполните операцию "Синхронизировать файлы проекта с Gradle".

### Шаг 5. Протестируйте вашу интеграцию

Запустите `yarn` для установки зависимостей react-native и `yarn native` для запуска metro bundler. Запустите ваше android-приложение в Android Studio, и оно должно загрузить JavaScript-код с сервера разработки и отобразить его в вашем React Native-фрагменте в Activity.

### Шаг 6. Дополнительная настройка — нативные модули

Вам может понадобиться обратиться к существующему коду Java/Kotlin из вашего компонента react. Нативные модули позволяют вам обращаться к нативному коду и запускать методы в вашем нативном приложении. Следуйте настройке здесь [native-modules-android](native-modules-android.md)

<!-- 0034.part.md -->
