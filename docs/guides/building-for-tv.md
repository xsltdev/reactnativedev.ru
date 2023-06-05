# Сборка для ТВ устройств

Поддержка телевизионных устройств была реализована с целью заставить существующие приложения React Native работать на Apple TV и Android TV, при этом в JavaScript-коде приложений не потребуется практически никаких изменений.

=== "AndroidTV"

    ## Изменения в сборке

    -   _Нативный слой_: Чтобы запустить проект React Native на Android TV, внесите следующие изменения в `AndroidManifest.xml`.

    ```xml
    <!-- Add custom banner image to display as Android TV launcher icon -->
    <application android:banner="@drawable/tv_banner">
    	<!-- ... -->
    	<intent-filter>
    		<!-- ... -->
    		<!-- Needed to properly create a launch intent when running on Android TV -->
    		<category
    			android:name="android.intent.category.LEANBACK_LAUNCHER"
    		/>
    	</intent-filter>
    	<!-- ... -->
    </application>
    ```

    <!-- 0002.part.md -->

    -   _Слой JavaScript_: Поддержка Android TV была добавлена в `Platform.android.js`. Вы можете проверить, выполняется ли код на Android TV, выполнив следующие действия

    <!-- end list -->

    <!-- 0003.part.md -->

    ```js
    var Platform = require('Platform');
    var running_on_android_tv = Platform.isTV;
    ```

    <!-- 0004.part.md -->

    ## Изменения кода

    -   _Доступ к сенсорным элементам управления_: При работе на Android TV фреймворк Android будет автоматически применять схему направленной навигации, основанную на относительном положении фокусируемых элементов в ваших представлениях. В миксин `Touchable` добавлен код для обнаружения изменения фокуса и использования существующих методов для стилизации компонентов и инициирования соответствующих действий при выборе представления с помощью пульта телевизора, поэтому `TouchableWithoutFeedback`, `TouchableHighlight`, `TouchableOpacity` и `TouchableNativeFeedback` будут работать как ожидалось. В частности:

    	-   `onFocus` будет выполняться, когда касаемое представление перейдет в фокус
    	-   `onBlur` будет выполняться, когда сенсорное представление выходит из фокуса
    	-   `onPress` будет выполняться, когда сенсорное представление действительно выбрано нажатием кнопки "select" на телевизионном пульте.

    -   _Телевизионный пульт/клавиатурный ввод_: Новый собственный класс `ReactAndroidTVRootViewHelper` устанавливает обработчики ключевых событий для событий пульта дистанционного управления телевизором. Когда происходят удаленные события ТВ, этот класс запускает событие JS. Это событие будет подхвачено экземплярами объекта JavaScript `TVEventHandler`. Код приложения, которому необходимо реализовать пользовательскую обработку удаленных ТВ событий, может создать экземпляр `TVEventHandler` и прослушивать эти события, как показано в следующем коде:

    <!-- конец списка -->

    <!-- 0005.part.md -->

    ```tsx
    var TVEventHandler = require('TVEventHandler');

    class Game2048 extends React.Component {
    	_tvEventHandler: any;

    	_enableTVEventHandler() {
    		this._tvEventHandler = new TVEventHandler();
    		this._tvEventHandler.enable(this, function (
    			cmp,
    			evt
    		) {
    			if (evt && evt.eventType === 'right') {
    				cmp.setState({
    					board: cmp.state.board.move(2),
    				});
    			} else if (evt && evt.eventType === 'up') {
    				cmp.setState({
    					board: cmp.state.board.move(1),
    				});
    			} else if (evt && evt.eventType === 'left') {
    				cmp.setState({
    					board: cmp.state.board.move(0),
    				});
    			} else if (evt && evt.eventType === 'down') {
    				cmp.setState({
    					board: cmp.state.board.move(3),
    				});
    			} else if (
    				evt &&
    				evt.eventType === 'playPause'
    			) {
    				cmp.restartGame();
    			}
    		});
    	}

    	_disableTVEventHandler() {
    		if (this._tvEventHandler) {
    			this._tvEventHandler.disable();
    			delete this._tvEventHandler;
    		}
    	}

    	componentDidMount() {
    		this._enableTVEventHandler();
    	}

    	componentWillUnmount() {
    		this._disableTVEventHandler();
    	}
    }
    ```

    <!-- 0006.part.md -->

    -   Поддержка _Dev Menu_: На эмуляторе cmd-M вызовет меню Dev Menu, аналогичное Android. Чтобы вызвать его на реальном устройстве Android TV, нажмите кнопку меню или долгое нажатие кнопки перемотки вперед на пульте дистанционного управления. (Пожалуйста, не трясите устройство Android TV, это не сработает)

    -   _Известные проблемы_:

    	-   Компоненты `TextInput` пока не работают (т.е. они не могут получать фокус автоматически, см. [этот комментарий](https://github.com/facebook/react-native/pull/16500#issuecomment-629285638)).
    		-   Однако можно использовать ссылку, чтобы вручную вызвать `inputRef.current.focus()`.
    		-   Вы можете обернуть ваш ввод внутри компонента `TouchableWithoutFeedback` и вызвать фокус в событии `onFocus` этого touchable. Это позволяет открывать клавиатуру с помощью клавиш со стрелками.
    		-   Клавиатура может сбрасывать свое состояние после каждого нажатия клавиш (это может происходить только в эмуляторе Android TV).
    	-   Содержимое компонентов `Modal` не может получить фокус, подробности смотрите в [этом выпуске](https://github.com/facebook/react-native/issues/24448).

=== "tvOS"

    !!!danger "Устарело"

    	Используйте [react-native-tvos](https://github.com/react-native-community/react-native-tvos) вместо этого. Подробности смотрите в блоге [0.62 release blog post](https://reactnative.dev/blog/#moving-apple-tv-to-react-native-tvos).

    ## Изменения в сборке

    -   _Нативный слой_: Все проекты React Native Xcode теперь имеют цели сборки для Apple TV, имена которых заканчиваются строкой '-tvOS'.

    -   _react-native init_: В новых проектах React Native, созданных с помощью `react-native init`, в проектах XCode будет автоматически создана цель Apple TV.

    -   _Слой JavaScript_: Поддержка Apple TV добавлена в `Platform.ios.js`. Вы можете проверить, запущен ли код на AppleTV, выполнив следующие действия

    <!-- end list -->

    <!-- 0007.part.md -->

    ```tsx
    var Platform = require('Platform');
    var running_on_tv = Platform.isTV;

    // If you want to be more specific and only detect devices running tvOS
    // (but no Android TV devices) you can use:
    var running_on_apple_tv = Platform.isTVOS;
    ```

    <!-- 0008.part.md -->

    ## Изменения в коде

    -   _Общая поддержка tvOS_: Все изменения в родном коде, специфичные для Apple TV, обернуты определением TARGET_OS_TV. Они включают изменения для подавления API, которые не поддерживаются на tvOS (например, веб-просмотры, слайдеры, переключатели, строка состояния и т.д.), а также изменения для поддержки пользовательского ввода с телевизионного пульта или клавиатуры.

    -   _Общая кодовая база_: Поскольку tvOS и iOS имеют большую часть общего кода на Objective-C и JavaScript, большинство документации для iOS в равной степени применимо и к tvOS.

    -   _Доступ к сенсорным элементам управления_: При работе на Apple TV родным классом представления является `RCTTVView`, который имеет дополнительные методы для использования механизма фокусировки tvOS. В миксин `Touchable` добавлен код для обнаружения изменений фокуса и использования существующих методов для стилизации компонентов и инициирования соответствующих действий при выборе представления с помощью телевизионного пульта, поэтому `TouchableWithoutFeedback`, `TouchableHighlight` и `TouchableOpacity` будут работать как ожидалось. В частности:

    	-   `onFocus` будет выполняться, когда касаемое представление перейдет в фокус
    	-   `onBlur` будет выполняться, когда сенсорное представление выходит из фокуса
    	-   `onPress` будет выполняться, когда сенсорное представление действительно выбрано нажатием кнопки "select" на телевизионном пульте.

    -   _Телевизионный пульт/клавиатурный ввод_: Новый собственный класс, `RCTTVRemoteHandler`, устанавливает распознаватели жестов для событий пульта дистанционного управления телевизором. Когда происходят события, связанные с ТВ-пультом, этот класс выпускает уведомления, которые подхватываются `RCTTVNavigationEventEmitter` (подкласс `RCTEventEmitter`), который выпускает JS-событие. Это событие будет подхвачено экземплярами объекта JavaScript `TVEventHandler`. Код приложения, которому необходимо реализовать пользовательскую обработку удаленных событий ТВ, может создать экземпляр `TVEventHandler` и прослушивать эти события, как в следующем коде:

    <!-- конец списка -->

    <!-- 0009.part.md -->

    ```tsx
    var TVEventHandler = require('TVEventHandler');

    class Game2048 extends React.Component {
    	_tvEventHandler: any;

    	_enableTVEventHandler() {
    		this._tvEventHandler = new TVEventHandler();
    		this._tvEventHandler.enable(this, function (
    			cmp,
    			evt
    		) {
    			if (evt && evt.eventType === 'right') {
    				cmp.setState({
    					board: cmp.state.board.move(2),
    				});
    			} else if (evt && evt.eventType === 'up') {
    				cmp.setState({
    					board: cmp.state.board.move(1),
    				});
    			} else if (evt && evt.eventType === 'left') {
    				cmp.setState({
    					board: cmp.state.board.move(0),
    				});
    			} else if (evt && evt.eventType === 'down') {
    				cmp.setState({
    					board: cmp.state.board.move(3),
    				});
    			} else if (
    				evt &&
    				evt.eventType === 'playPause'
    			) {
    				cmp.restartGame();
    			}
    		});
    	}

    	_disableTVEventHandler() {
    		if (this._tvEventHandler) {
    			this._tvEventHandler.disable();
    			delete this._tvEventHandler;
    		}
    	}

    	componentDidMount() {
    		this._enableTVEventHandler();
    	}

    	componentWillUnmount() {
    		this._disableTVEventHandler();
    	}
    }
    ```

    <!-- 0010.part.md -->

    -   Поддержка _Dev Menu_: На симуляторе cmd-D вызывает Dev Menu, аналогично iOS. Чтобы вызвать его на реальном устройстве Apple TV, долго нажмите на кнопку воспроизведения/паузы на пульте ДУ. (Пожалуйста, не трясите устройство Apple TV, это не сработает :))

    -   _Анимация пульта дистанционного управления телевизором_: Собственный код `RCTTVView` реализует рекомендованную Apple параллакс-анимацию, которая помогает направлять взгляд при навигации пользователя по просмотрам. Анимация может быть отключена или настроена с помощью новых опциональных свойств представления.

    -   _Навигация назад с помощью кнопки меню пульта дистанционного управления телевизором_: Компонент `BackHandler`, изначально написанный для поддержки кнопки "назад" на Android, теперь также поддерживает навигацию назад на Apple TV с помощью кнопки меню на пульте дистанционного управления телевизором.

    -   Поведение _TabBarIOS_: Компонент `TabBarIOS` оборачивает родной API `UITabBar`, который работает по-другому на Apple TV. Чтобы избежать дрожащего повторного рендеринга панели вкладок в tvOS (см. [этот выпуск](https://github.com/facebook/react-native/issues/15081)), выбранный элемент панели вкладок может быть установлен из JavaScript только при первоначальном рендеринге, и после этого управляется пользователем через нативный код.

    -   _Известные проблемы_:

    	-   [Прокрутка ListView](https://github.com/facebook/react-native/issues/12793). Проблему можно обойти, установив `removeClippedSubviews` в false в ListView и подобных компонентах. Для более подробного обсуждения этой проблемы смотрите [этот PR](https://github.com/facebook/react-native/pull/12944).
