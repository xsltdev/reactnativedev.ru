# Нативные UI компоненты Android

!!!info ""

    Native Module и Native Components - это наши стабильные технологии, используемые в унаследованной архитектуре. Они будут устаревшими в будущем, когда новая архитектура станет стабильной. Новая архитектура использует [Turbo Native Module](the-new-architecture-pillars-turbomodules.md) и [Fabric Native Components](the-new-architecture-pillars-fabric-components.md) для достижения аналогичных результатов.

Существует масса нативных виджетов пользовательского интерфейса, готовых к использованию в новейших приложениях - некоторые из них являются частью платформы, другие доступны в виде библиотек сторонних разработчиков, а еще больше могут использоваться в вашем собственном портфолио. В React Native уже обернуто несколько наиболее важных компонентов платформы, таких как `ScrollView` и `TextInput`, но не все из них, и уж точно не те, которые вы могли написать сами для предыдущего приложения. К счастью, мы можем обернуть эти существующие компоненты для бесшовной интеграции с вашим приложением React Native.

Как и руководство по нативным модулям, это более продвинутое руководство, которое предполагает, что вы немного знакомы с программированием Android SDK. Это руководство покажет вам, как создать нативный компонент пользовательского интерфейса, проведя вас через реализацию подмножества существующего компонента `ImageView`, доступного в основной библиотеке React Native.

## Пример ImageView

В этом примере мы рассмотрим требования к реализации, позволяющие использовать ImageViews в JavaScript.

Нативные представления создаются и манипулируются путем расширения `ViewManager` или, чаще всего, `SimpleViewManager`. В данном случае `SimpleViewManager` удобен тем, что он применяет общие свойства, такие как цвет фона, непрозрачность и Flexbox-макет.

Эти подклассы по сути являются синглтонами - мост создает только один экземпляр каждого из них. Они отправляют собственные представления в `NativeViewHierarchyManager`, который делегирует им обратную связь для установки и обновления свойств представлений по мере необходимости. Менеджеры `ViewManagers` также обычно являются делегатами для представлений, посылая события обратно в JavaScript через мост.

Чтобы отправить представление:

1.  Создайте подкласс ViewManager.
2.  Реализуйте метод `createViewInstance`.
3.  Раскройте сеттеры свойств представления, используя аннотацию `@ReactProp` (или `@ReactPropGroup`).
4.  Зарегистрируйте менеджер в `createViewManagers` пакета приложений.
5.  Реализуйте модуль JavaScript

### 1. Создайте подкласс `ViewManager`.

В этом примере мы создаем класс менеджера представлений `ReactImageManager`, который расширяет `SimpleViewManager` типа `ReactImageView`. `ReactImageView` - это тип объекта, управляемого менеджером, это будет пользовательское нативное представление. Имя, возвращаемое функцией `getName`, используется для ссылки на тип нативного представления из JavaScript.

=== "Kotlin"

    ```kotlin
    class ReactImageManager(
    	private val callerContext: ReactApplicationContext
    ) : SimpleViewManager<ReactImageView>() {

    override fun getName() = REACT_CLASS

    companion object {
    	const val REACT_CLASS = "RCTImageView"
    }
    }
    ```

=== "Java"

    ```java
    public class ReactImageManager extends SimpleViewManager<ReactImageView> {

    public static final String REACT_CLASS = "RCTImageView";
    ReactApplicationContext mCallerContext;

    public ReactImageManager(ReactApplicationContext reactContext) {
    	mCallerContext = reactContext;
    }

    @Override
    public String getName() {
    	return REACT_CLASS;
    }
    }
    ```

### 2. Реализуйте метод `createViewInstance`.

Представления создаются в методе `createViewInstance`, представление должно инициализировать себя в состоянии по умолчанию, любые свойства будут установлены через последующий вызов `updateView.`.

=== "Kotlin"

    ```kotlin
    override fun createViewInstance(context: ThemedReactContext) =
    	ReactImageView(context, Fresco.newDraweeControllerBuilder(), null, callerContext)
    ```

=== "Java"

    ```java
    @Override
    public ReactImageView createViewInstance(ThemedReactContext context) {
    	return new ReactImageView(context, Fresco.newDraweeControllerBuilder(), null, mCallerContext);
    }
    ```

### 3. Выявление сеттеров свойств представления с помощью аннотации `@ReactProp` (или `@ReactPropGroup`)

Свойства, которые должны быть отражены в JavaScript, должны быть представлены в виде метода setter, аннотированного `@ReactProp` (или `@ReactPropGroup`). Метод setter должен принимать обновляемое представление (текущего типа представления) в качестве первого аргумента и значение свойства в качестве второго аргумента. Setter должен быть публичным и не возвращать значение (т.е. тип возврата должен быть `void` в Java или `Unit` в Kotlin). Тип свойства, передаваемого в JS, определяется автоматически на основе типа значения аргумента сеттера. В настоящее время поддерживаются следующие типы значений (в Java): `boolean`, `int`, `float`, `double`, `String`, `Boolean`, `Integer`, `ReadableArray`, `ReadableMap`. Соответствующие типы в Kotlin: `Boolean`, `Int`, `Float`, `Double`, `String`, `ReadableArray`, `ReadableMap`.

Аннотация `@ReactProp` имеет один обязательный аргумент `name` типа `String`. Имя, присвоенное аннотации `@ReactProp`, связанной с методом сеттера, используется для ссылки на свойство на стороне JS.

Кроме `name`, аннотация `@ReactProp` может принимать следующие необязательные аргументы: `defaultBoolean`, `defaultInt`, `defaultFloat`. Эти аргументы должны быть соответствующего типа (соответственно `boolean`, `int`, `float` в Java и `Boolean`, `Int`, `Float` в Kotlin), а значение будет передано методу сеттера в случае, если свойство, на которое ссылается сеттер, было удалено из компонента. Обратите внимание, что значения "по умолчанию" предоставляются только для примитивных типов, в случае, если сеттер имеет какой-то сложный тип, `null` будет предоставлено в качестве значения по умолчанию в случае, если соответствующее свойство будет удалено.

Требования к декларации сеттера для методов, аннотированных с `@ReactPropGroup`, отличаются от требований для `@ReactProp`, пожалуйста, обратитесь к документации класса аннотации `@ReactPropGroup` для получения дополнительной информации об этом. **ВАЖНО!** В ReactJS обновление значения свойства приведет к вызову метода setter. Обратите внимание, что одним из способов обновления компонента является удаление свойств, которые были установлены ранее. В этом случае также будет вызван метод setter, чтобы уведомить менеджер представления о том, что свойство изменилось. В этом случае будет предоставлено значение "по умолчанию" (для примитивных типов значение "по умолчанию" можно указать с помощью аргументов `defaultBoolean`, `defaultFloat` и т.д. аннотации `@ReactProp`, для сложных типов setter будет вызван со значением `null`).

=== "Kotlin"

    ```kotlin
    @ReactProp(name = "src")
    fun setSrc(view: ReactImageView, sources: ReadableArray?) {
    	view.setSource(sources)
    }

    @ReactProp(name = "borderRadius", defaultFloat = 0f)
    override fun setBorderRadius(view: ReactImageView, borderRadius: Float) {
    	view.setBorderRadius(borderRadius)
    }

    @ReactProp(name = ViewProps.RESIZE_MODE)
    fun setResizeMode(view: ReactImageView, resizeMode: String?) {
    	view.setScaleType(ImageResizeMode.toScaleType(resizeMode))
    }
    ```

=== "Java"

    ```java
    @ReactProp(name = "src")
    public void setSrc(ReactImageView view, @Nullable ReadableArray sources) {
    	view.setSource(sources);
    }

    @ReactProp(name = "borderRadius", defaultFloat = 0f)
    public void setBorderRadius(ReactImageView view, float borderRadius) {
    	view.setBorderRadius(borderRadius);
    }

    @ReactProp(name = ViewProps.RESIZE_MODE)
    public void setResizeMode(ReactImageView view, @Nullable String resizeMode) {
    	view.setScaleType(ImageResizeMode.toScaleType(resizeMode));
    }
    ```

### 4. Регистрация `ViewManager`

Последним шагом является регистрация ViewManager в приложении, это происходит аналогично [Native Modules](native-modules-android.md), через функцию-член пакета applications `createViewManagers`.

=== "Kotlin"

    ```kotlin
    override fun createViewManagers(
    	reactContext: ReactApplicationContext
    ) = listOf(ReactImageManager(reactContext))
    ```

=== "Java"

    ```java
    @Override
    public List<ViewManager> createViewManagers(
    							ReactApplicationContext reactContext) {
    	return Arrays.<ViewManager>asList(
    	new ReactImageManager(reactContext)
    	);
    }
    ```

### 5. Реализация модуля JavaScript

Самый последний шаг - создание модуля JavaScript, который определяет интерфейсный слой между Java/Kotlin и JavaScript для пользователей вашего нового представления. Рекомендуется документировать интерфейс компонента в этом модуле (например, используя TypeScript, Flow или обычные комментарии).

```tsx title="ImageView.tsx"
import { requireNativeComponent } from 'react-native';

/**
 * Composes `View`.
 *
 * - src: string
 * - borderRadius: number
 * - resizeMode: 'cover' | 'contain' | 'stretch'
 */
module.exports = requireNativeComponent('RCTImageView');
```

Функция `requireNativeComponent` принимает имя нативного представления. Обратите внимание, что если ваш компонент должен делать что-то более сложное (например, пользовательскую обработку событий), вам следует обернуть родной компонент в другой компонент React. Это показано в примере `MyCustomView` ниже.

## События

Итак, теперь мы знаем, как открывать нативные компоненты представления, которыми мы можем свободно управлять из JS, но как нам быть с событиями от пользователя, такими как пинч-зум или панорамирование? При возникновении нативного события нативный код должен выдать событие JavaScript-представлению представления, и два представления будут связаны значением, возвращаемым методом `getId()`.

=== "Kotlin"

    ```kotlin
    class MyCustomView(context: Context) : View(context) {
    ...
    fun onReceiveNativeEvent() {
    	val event = Arguments.createMap().apply {
    	putString("message", "MyMessage")
    	}
    	val reactContext = context as ReactContext
    	reactContext
    		.getJSModule(RCTEventEmitter::class.java)
    		.receiveEvent(id, "topChange", event)
    }
    }
    ```

=== "Java"

    ```java
    class MyCustomView extends View {
    ...
    public void onReceiveNativeEvent() {
    	WritableMap event = Arguments.createMap();
    	event.putString("message", "MyMessage");
    	ReactContext reactContext = (ReactContext)getContext();
    	reactContext
    		.getJSModule(RCTEventEmitter.class)
    		.receiveEvent(getId(), "topChange", event);
    	}
    }
    ```

Чтобы сопоставить имя события `topChange` с реквизитом обратного вызова `onChange` в JavaScript, зарегистрируйте его, переопределив метод `getExportedCustomBubblingEventTypeConstants` в вашем `ViewManager`:

=== "Kotlin"

    ```kotlin
    class ReactImageManager : SimpleViewManager<MyCustomView>() {
    ...
    override fun getExportedCustomBubblingEventTypeConstants(): Map<String, Any> {
    	return mapOf(
    	"topChange" to mapOf(
    		"phasedRegistrationNames" to mapOf(
    		"bubbled" to "onChange"
    		)
    	)
    	)
    }
    }
    ```

=== "Java"

    ```java
    public class ReactImageManager extends SimpleViewManager<MyCustomView> {
    	...
    	public Map getExportedCustomBubblingEventTypeConstants() {
    		return MapBuilder.builder().put(
    			"topChange",
    			MapBuilder.of(
    				"phasedRegistrationNames",
    				MapBuilder.of("bubbled", "onChange")
    			)
    		).build();
    	}
    }
    ```

Этот обратный вызов вызывается с необработанным событием, которое мы обычно обрабатываем в компоненте-обертке, чтобы упростить API:

```tsx title="MyCustomView.tsx"
class MyCustomView extends React.Component {
  constructor(props) {
    super(props);
    this._onChange = this._onChange.bind(this);
  }
  _onChange(event) {
    if (!this.props.onChangeMessage) {
      return;
    }
    this.props.onChangeMessage(event.nativeEvent.message);
  }
  render() {
    return <RCTMyCustomView {...this.props} onChange={this._onChange} />;
  }
}
MyCustomView.propTypes = {
  /**
   * Callback that is called continuously when the user is dragging the map.
   */
  onChangeMessage: PropTypes.func,
  ...
};

const RCTMyCustomView = requireNativeComponent(`RCTMyCustomView`);
```

## Интеграция на примере Android Fragment

Для того чтобы интегрировать существующие нативные элементы пользовательского интерфейса в ваше приложение React Native, вам может понадобиться использовать Android Fragments, чтобы дать вам более детальный контроль над вашим нативным компонентом, чем возвращение `View` из вашего `ViewManager`. Это понадобится вам, если вы хотите добавить пользовательскую логику, привязанную к вашему представлению с помощью [методов жизненного цикла](https://developer.android.com/guide/fragments/lifecycle), таких как `onViewCreated`, `onPause`, `onResume`. Следующие шаги покажут вам, как это сделать:

### 1. Создание примера пользовательского представления

Сначала создадим класс `CustomView`, который расширяет `FrameLayout` (содержимым этого представления может быть любое представление, которое вы хотите отобразить)

=== "Kotlin"

    ```kotlin title="CustomView.kt"
    // replace with your package
    package com.mypackage

    import android.content.Context
    import android.graphics.Color
    import android.widget.FrameLayout
    import android.widget.TextView

    class CustomView(context: Context) : FrameLayout(context) {
    init {
    	// set padding and background color
    	setPadding(16,16,16,16)
    	setBackgroundColor(Color.parseColor("#5FD3F3"))

    	// add default text view
    	addView(TextView(context).apply {
    	text = "Welcome to Android Fragments with React Native."
    	})
    }
    }
    ```

=== "Java"

    ```java title="CustomView.java"
    // replace with your package
    package com.mypackage;

    import android.content.Context;
    import android.graphics.Color;
    import android.widget.FrameLayout;
    import android.widget.ImageView;
    import android.widget.TextView;

    import androidx.annotation.NonNull;

    public class CustomView extends FrameLayout {
    public CustomView(@NonNull Context context) {
    	super(context);
    	// set padding and background color
    	this.setPadding(16,16,16,16);
    	this.setBackgroundColor(Color.parseColor("#5FD3F3"));

    	// add default text view
    	TextView text = new TextView(context);
    	text.setText("Welcome to Android Fragments with React Native.");
    	this.addView(text);
    }
    }
    ```

### 2. Создайте `фрагмент`

=== "Kotlin"

    ```kotlin title="MyFragment.kt"
    // replace with your package
    package com.mypackage

    import android.os.Bundle
    import android.view.LayoutInflater
    import android.view.View
    import android.view.ViewGroup
    import androidx.fragment.app.Fragment

    // replace with your view's import
    import com.mypackage.CustomView

    class MyFragment : Fragment() {
    private lateinit var customView: CustomView

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
    	super.onCreateView(inflater, container, savedInstanceState)
    	customView = CustomView(requireNotNull(context))
    	return customView // this CustomView could be any view that you want to render
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    	super.onViewCreated(view, savedInstanceState)
    	// do any logic that should happen in an `onCreate` method, e.g:
    	// customView.onCreate(savedInstanceState);
    }

    override fun onPause() {
    	super.onPause()
    	// do any logic that should happen in an `onPause` method
    	// e.g.: customView.onPause();
    }

    override fun onResume() {
    	super.onResume()
    	// do any logic that should happen in an `onResume` method
    	// e.g.: customView.onResume();
    }

    override fun onDestroy() {
    	super.onDestroy()
    	// do any logic that should happen in an `onDestroy` method
    	// e.g.: customView.onDestroy();
    }
    }
    ```

=== "Java"

    ```java title="MyFragment.java"
    // replace with your package
    package com.mypackage;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.fragment.app.Fragment;

    // replace with your view's import
    import com.mypackage.CustomView;

    public class MyFragment extends Fragment {
    	CustomView customView;

    	@Override
    	public View onCreateView(LayoutInflater inflater, ViewGroup parent, Bundle savedInstanceState) {
    		super.onCreateView(inflater, parent, savedInstanceState);
    		customView = new CustomView(this.getContext());
    		return customView; // this CustomView could be any view that you want to render
    	}

    	@Override
    	public void onViewCreated(View view, Bundle savedInstanceState) {
    		super.onViewCreated(view, savedInstanceState);
    		// do any logic that should happen in an `onCreate` method, e.g:
    		// customView.onCreate(savedInstanceState);
    	}

    	@Override
    	public void onPause() {
    		super.onPause();
    		// do any logic that should happen in an `onPause` method
    		// e.g.: customView.onPause();
    	}

    	@Override
    	public void onResume() {
    		super.onResume();
    	// do any logic that should happen in an `onResume` method
    	// e.g.: customView.onResume();
    	}

    	@Override
    	public void onDestroy() {
    		super.onDestroy();
    		// do any logic that should happen in an `onDestroy` method
    		// e.g.: customView.onDestroy();
    	}
    }
    ```

### 3. Создайте подкласс `ViewManager`.

=== "Kotlin"

    ```kotlin title="MyViewManager.kt"
    // replace with your package
    package com.mypackage

    import android.view.Choreographer
    import android.view.View
    import android.view.ViewGroup
    import android.widget.FrameLayout
    import androidx.fragment.app.FragmentActivity
    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.bridge.ReadableArray
    import com.facebook.react.uimanager.ThemedReactContext
    import com.facebook.react.uimanager.ViewGroupManager
    import com.facebook.react.uimanager.annotations.ReactPropGroup

    class MyViewManager(
    	private val reactContext: ReactApplicationContext
    ) : ViewGroupManager<FrameLayout>() {
    private var propWidth: Int? = null
    private var propHeight: Int? = null

    override fun getName() = REACT_CLASS

    /**
    * Return a FrameLayout which will later hold the Fragment
    */
    override fun createViewInstance(reactContext: ThemedReactContext) =
    	FrameLayout(reactContext)

    /**
    * Map the "create" command to an integer
    */
    override fun getCommandsMap() = mapOf("create" to COMMAND_CREATE)

    /**
    * Handle "create" command (called from JS) and call createFragment method
    */
    override fun receiveCommand(
    	root: FrameLayout,
    	commandId: String,
    	args: ReadableArray?
    ) {
    	super.receiveCommand(root, commandId, args)
    	val reactNativeViewId = requireNotNull(args).getInt(0)

    	when (commandId.toInt()) {
    	COMMAND_CREATE -> createFragment(root, reactNativeViewId)
    	}
    }

    @ReactPropGroup(names = ["width", "height"], customType = "Style")
    fun setStyle(view: FrameLayout, index: Int, value: Int) {
    	if (index == 0) propWidth = value
    	if (index == 1) propHeight = value
    }

    /**
    * Replace your React Native view with a custom fragment
    */
    fun createFragment(root: FrameLayout, reactNativeViewId: Int) {
    	val parentView = root.findViewById<ViewGroup>(reactNativeViewId)
    	setupLayout(parentView)

    	val myFragment = MyFragment()
    	val activity = reactContext.currentActivity as FragmentActivity
    	activity.supportFragmentManager
    		.beginTransaction()
    		.replace(reactNativeViewId, myFragment, reactNativeViewId.toString())
    		.commit()
    }

    fun setupLayout(view: View) {
    	Choreographer.getInstance().postFrameCallback(object: Choreographer.FrameCallback {
    	override fun doFrame(frameTimeNanos: Long) {
    		manuallyLayoutChildren(view)
    		view.viewTreeObserver.dispatchOnGlobalLayout()
    		Choreographer.getInstance().postFrameCallback(this)
    	}
    	})
    }

    /**
    * Layout all children properly
    */
    private fun manuallyLayoutChildren(view: View) {
    	// propWidth and propHeight coming from react-native props
    	val width = requireNotNull(propWidth)
    	val height = requireNotNull(propHeight)

    	view.measure(
    		View.MeasureSpec.makeMeasureSpec(width, View.MeasureSpec.EXACTLY),
    		View.MeasureSpec.makeMeasureSpec(height, View.MeasureSpec.EXACTLY))

    	view.layout(0, 0, width, height)
    }

    companion object {
    	private const val REACT_CLASS = "MyViewManager"
    	private const val COMMAND_CREATE = 1
    }
    }
    ```

=== "Java"

    ```java title="MyViewManager.java"
    // replace with your package
    package com.mypackage;

    import android.view.Choreographer;
    import android.view.View;
    import android.widget.FrameLayout;

    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.FragmentActivity;

    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.bridge.ReadableArray;
    import com.facebook.react.common.MapBuilder;
    import com.facebook.react.uimanager.annotations.ReactProp;
    import com.facebook.react.uimanager.annotations.ReactPropGroup;
    import com.facebook.react.uimanager.ViewGroupManager;
    import com.facebook.react.uimanager.ThemedReactContext;

    import java.util.Map;

    public class MyViewManager extends ViewGroupManager<FrameLayout> {

    public static final String REACT_CLASS = "MyViewManager";
    public final int COMMAND_CREATE = 1;
    private int propWidth;
    private int propHeight;

    ReactApplicationContext reactContext;

    public MyViewManager(ReactApplicationContext reactContext) {
    	this.reactContext = reactContext;
    }

    @Override
    public String getName() {
    	return REACT_CLASS;
    }

    /**
     * Return a FrameLayout which will later hold the Fragment
     */
    @Override
    public FrameLayout createViewInstance(ThemedReactContext reactContext) {
    	return new FrameLayout(reactContext);
    }

    /**
     * Map the "create" command to an integer
     */
    @Nullable
    @Override
    public Map<String, Integer> getCommandsMap() {
    	return MapBuilder.of("create", COMMAND_CREATE);
    }

    /**
     * Handle "create" command (called from JS) and call createFragment method
     */
    @Override
    public void receiveCommand(
    	@NonNull FrameLayout root,
    	String commandId,
    	@Nullable ReadableArray args
    ) {
    	super.receiveCommand(root, commandId, args);
    	int reactNativeViewId = args.getInt(0);
    	int commandIdInt = Integer.parseInt(commandId);

    	switch (commandIdInt) {
    	case COMMAND_CREATE:
    		createFragment(root, reactNativeViewId);
    		break;
    	default: {}
    	}
    }

    @ReactPropGroup(names = {"width", "height"}, customType = "Style")
    public void setStyle(FrameLayout view, int index, Integer value) {
    	if (index == 0) {
    	propWidth = value;
    	}

    	if (index == 1) {
    	propHeight = value;
    	}
    }

    /**
     * Replace your React Native view with a custom fragment
     */
    public void createFragment(FrameLayout root, int reactNativeViewId) {
    	ViewGroup parentView = (ViewGroup) root.findViewById(reactNativeViewId);
    	setupLayout(parentView);

    	final MyFragment myFragment = new MyFragment();
    	FragmentActivity activity = (FragmentActivity) reactContext.getCurrentActivity();
    	activity.getSupportFragmentManager()
    			.beginTransaction()
    			.replace(reactNativeViewId, myFragment, String.valueOf(reactNativeViewId))
    			.commit();
    }

    public void setupLayout(View view) {
    	Choreographer.getInstance().postFrameCallback(new Choreographer.FrameCallback() {
    	@Override
    	public void doFrame(long frameTimeNanos) {
    		manuallyLayoutChildren(view);
    		view.getViewTreeObserver().dispatchOnGlobalLayout();
    		Choreographer.getInstance().postFrameCallback(this);
    	}
    	});
    }

    /**
     * Layout all children properly
     */
    public void manuallyLayoutChildren(View view) {
    	// propWidth and propHeight coming from react-native props
    	int width = propWidth;
    	int height = propHeight;

    	view.measure(
    			View.MeasureSpec.makeMeasureSpec(width, View.MeasureSpec.EXACTLY),
    			View.MeasureSpec.makeMeasureSpec(height, View.MeasureSpec.EXACTLY));

    	view.layout(0, 0, width, height);
    }
    }
    ```

### 4. Зарегистрируйте `ViewManager`

=== "Kotlin"

    ```kotlin title="MyPackage.kt"
    // replace with your package
    package com.mypackage

    import com.facebook.react.ReactPackage
    import com.facebook.react.bridge.ReactApplicationContext
    import com.facebook.react.uimanager.ViewManager

    class MyPackage : ReactPackage {
    ...
    override fun createViewManagers(
    	reactContext: ReactApplicationContext
    ) = listOf(MyViewManager(reactContext))
    }
    ```

=== "Java"

    ```java title="MyPackage.java"
    // replace with your package
    package com.mypackage;

    import com.facebook.react.ReactPackage;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.uimanager.ViewManager;

    import java.util.Arrays;
    import java.util.List;

    public class MyPackage implements ReactPackage {

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    	return Arrays.<ViewManager>asList(
    			new MyViewManager(reactContext)
    	);
    }

    }
    ```

### 5. Зарегистрируйте `Package`

=== "Kotlin"

    ```kotlin title="MainApplication.kt"
    	override fun getPackages() = PackageList(this).packages.apply {
    	add(MyPackage())
    	}
    ```

=== "Java"

    ```java title="MainApplication.java"
    	@Override
    	protected List<ReactPackage> getPackages() {
    	List<ReactPackage> packages = new PackageList(this).getPackages();
    	...
    	packages.add(new MyPackage());
    	return packages;
    	}
    ```

### 6. Внедрение модуля JavaScript

I. Начните с пользовательского менеджера представлений:

```tsx title="MyViewManager.tsx"
import { requireNativeComponent } from 'react-native';

export const MyViewManager = requireNativeComponent(
    'MyViewManager'
);
```

II. Затем реализуйте пользовательский View, вызывая метод `create`:

```tsx title="MyView.tsx"
import React, { useEffect, useRef } from 'react';
import {
    PixelRatio,
    UIManager,
    findNodeHandle,
} from 'react-native';

import { MyViewManager } from './my-view-manager';

const createFragment = (viewId) =>
    UIManager.dispatchViewManagerCommand(
        viewId,
        // we are calling the 'create' command
        UIManager.MyViewManager.Commands.create.toString(),
        [viewId]
    );

export const MyView = () => {
    const ref = useRef(null);

    useEffect(() => {
        const viewId = findNodeHandle(ref.current);
        createFragment(viewId);
    }, []);

    return (
        <MyViewManager
            style={{
                // converts dpi to px, provide desired height
                height: PixelRatio.getPixelSizeForLayoutSize(
                    200
                ),
                // converts dpi to px, provide desired width
                width: PixelRatio.getPixelSizeForLayoutSize(
                    200
                ),
            }}
            ref={ref}
        />
    );
};
```

Если вы хотите раскрыть сеттеры свойств, используя аннотацию `@ReactProp` (или `@ReactPropGroup`), смотрите пример `ImageView` выше.
