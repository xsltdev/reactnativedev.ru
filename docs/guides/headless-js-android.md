# Headless JS

**Headless JS** - это способ выполнения задач на JavaScript, пока ваше приложение находится в фоновом режиме. Его можно использовать, например, для синхронизации свежих данных, обработки push-уведомлений или воспроизведения музыки.

## JS API

Задача - это асинхронная функция, которую вы регистрируете в `AppRegistry`, аналогично регистрации приложений React:

```tsx
import { AppRegistry } from 'react-native';
AppRegistry.registerHeadlessTask('SomeTaskName', () =>
    require('SomeTaskName')
);
```

Затем, в `SomeTaskName.js`:

```tsx
module.exports = async (taskData) => {
    // do stuff
};
```

Вы можете делать в своей задаче все, что угодно, например, сетевые запросы, таймеры и так далее, если это не затрагивает пользовательский интерфейс. Как только ваша задача завершится (т.е. обещание будет выполнено), React Native перейдет в режим "паузы" (если только не запущены другие задачи или нет приложения на переднем плане).

## API платформы

Да, это все еще требует некоторого нативного кода, но он довольно тонкий. Вам нужно расширить `HeadlessJsTaskService` и переопределить `getTaskConfig`, например:

=== "Java"

    ```java
    package com.your_application_name;

    import android.content.Intent;
    import android.os.Bundle;
    import com.facebook.react.HeadlessJsTaskService;
    import com.facebook.react.bridge.Arguments;
    import com.facebook.react.jstasks.HeadlessJsTaskConfig;
    import javax.annotation.Nullable;

    public class MyTaskService extends HeadlessJsTaskService {

    @Override
    protected @Nullable HeadlessJsTaskConfig getTaskConfig(Intent intent) {
    	Bundle extras = intent.getExtras();
    	if (extras != null) {
    	return new HeadlessJsTaskConfig(
    		"SomeTaskName",
    		Arguments.fromBundle(extras),
    		5000, // timeout in milliseconds for the task
    		false // optional: defines whether or not the task is allowed in foreground. Default is false
    		);
    	}
    	return null;
    }
    }
    ```

=== "Kotlin"

    ```kotlin
    package com.your_application_name;

    import android.content.Intent
    import com.facebook.react.HeadlessJsTaskService
    import com.facebook.react.bridge.Arguments
    import com.facebook.react.jstasks.HeadlessJsTaskConfig

    class MyTaskService : HeadlessJsTaskService() {
    	override fun getTaskConfig(intent: Intent): HeadlessJsTaskConfig? {
    		return intent.extras?.let {
    			HeadlessJsTaskConfig(
    				"SomeTaskName",
    				Arguments.fromBundle(it),
    				5000, // timeout for the task
    				false // optional: defines whether or not the task is allowed in foreground.
    				// Default is false
    			)
    		}
    	}
    }

    ```

Затем добавьте службу в файл `AndroidManifest.xml`:

```xml
<service android:name="com.example.MyTaskService" />
```

Теперь, всякий раз, когда вы [запускаете свою службу][0], например, в качестве периодической задачи или в ответ на какое-то системное событие / трансляцию, JS будет раскручиваться, выполнять свою задачу, а затем сворачиваться.

Пример:

=== "Java"

    ```java
    Intent service = new Intent(getApplicationContext(), MyTaskService.class);
    Bundle bundle = new Bundle();

    bundle.putString("foo", "bar");
    service.putExtras(bundle);

    getApplicationContext().startService(service);
    ```

=== "Kotlin"

    ```kotlin
    val service = Intent(applicationContext, MyTaskService::class.java)
    val bundle = Bundle()

    bundle.putString("foo", "bar")

    service.putExtras(bundle)

    applicationContext.startService(service)
    ```

## Повторные попытки

По умолчанию задача headless JS не будет выполнять никаких повторных попыток. Для этого необходимо создать `HeadlessJsRetryPolicy` и бросить определенную `Error`.

`LinearCountingRetryPolicy` - это реализация `HeadlessJsRetryPolicy`, которая позволяет вам указать максимальное количество повторных попыток с фиксированной задержкой между каждой попыткой. Если это не подходит для ваших нужд, вы можете реализовать собственную `HeadlessJsRetryPolicy`. Эти политики могут быть переданы в качестве дополнительного аргумента в конструктор `HeadlessJsTaskConfig`, например.

=== "Java"

    ```java
    HeadlessJsRetryPolicy retryPolicy = new LinearCountingRetryPolicy(
    	3, // Max number of retry attempts
    	1000 // Delay between each retry attempt
    );

    return new HeadlessJsTaskConfig(
    	'SomeTaskName',
    	Arguments.fromBundle(extras),
    	5000,
    	false,
    	retryPolicy
    );
    ```

=== "Kotlin"

    ```kotlin
    val retryPolicy: HeadlessJsTaskRetryPolicy =
    	LinearCountingRetryPolicy(
    		3, // Max number of retry attempts
    		1000 // Delay between each retry attempt
    	)

    return HeadlessJsTaskConfig("SomeTaskName", Arguments.fromBundle(extras), 5000, false, retryPolicy)
    ```

Повторная попытка будет предпринята только в случае возникновения определенной `ошибки`. Внутри безголовой JS-задачи можно импортировать ошибку и бросать ее, когда требуется повторная попытка.

Пример:

```tsx
import {HeadlessJsTaskError} from 'HeadlessJsTask';

module.exports = async taskData => {
  const condition = ...;
  if (!condition) {
    throw new HeadlessJsTaskError();
  }
};
```

Если вы хотите, чтобы все ошибки вызывали повторную попытку, вам нужно перехватить их и выдать вышеуказанную ошибку.

## Предостережения

-   Функция, переданная в `setTimeout`, не всегда ведет себя так, как ожидается. Вместо этого функция вызывается только при повторном запуске приложения. Если вам нужно только подождать, используйте функцию повторного запуска.
-   По умолчанию ваше приложение аварийно завершается, если вы пытаетесь запустить задачу, когда приложение находится на переднем плане. Это сделано для того, чтобы разработчики не прострелили себе ногу, выполняя много работы в задаче и замедляя работу пользовательского интерфейса. Вы можете передать четвертый аргумент `boolean` для управления этим поведением.
-   Если вы запускаете свой сервис из `BroadcastReceiver`, обязательно вызовите `HeadlessJsTaskService.acquireWakeLockNow()` перед возвратом из `onReceive()`.

## Пример использования

Сервис может быть запущен из Java API. Сначала вам нужно решить, когда сервис должен быть запущен, и реализовать ваше решение соответствующим образом. Вот пример, который реагирует на изменение сетевого соединения.

Следующие строки показывают часть файла манифеста Android для регистрации широковещательного приемника.

```xml
<receiver android:name=".NetworkChangeReceiver">
    <intent-filter>
        <action
            android:name="android.net.conn.CONNECTIVITY_CHANGE"
        />
    </intent-filter>
</receiver>
```

Приемник широковещания затем обрабатывает намерение, которое было передано в функции onReceive. Это отличное место, чтобы проверить, находится ли ваше приложение на переднем плане или нет. Если приложение не на переднем плане, мы можем подготовить наше намерение к запуску, без информации или с дополнительной информацией, собранной с помощью `putExtra` (помните, что пакет может обрабатывать только посылочные значения). В итоге сервис запускается и wakelock приобретается.

=== "Java"

    ```java
    import android.app.ActivityManager;
    import android.content.BroadcastReceiver;
    import android.content.Context;
    import android.content.Intent;
    import android.net.ConnectivityManager;
    import android.net.Network;
    import android.net.NetworkCapabilities;
    import android.net.NetworkInfo;
    import android.os.Build;

    import com.facebook.react.HeadlessJsTaskService;

    public class NetworkChangeReceiver extends BroadcastReceiver {

    	@Override
    	public void onReceive(final Context context, final Intent intent) {
    		/**
    		 This part will be called every time network connection is changed
    		e.g. Connected -> Not Connected
    		**/
    		if (!isAppOnForeground((context))) {
    			/**
    			 We will start our service and send extra info about
    			network connections
    			**/
    			boolean hasInternet = isNetworkAvailable(context);
    			Intent serviceIntent = new Intent(context, MyTaskService.class);
    			serviceIntent.putExtra("hasInternet", hasInternet);
    			context.startService(serviceIntent);
    			HeadlessJsTaskService.acquireWakeLockNow(context);
    		}
    	}

    	private boolean isAppOnForeground(Context context) {
    		/**
    		 We need to check if app is in foreground otherwise the app will crash.
    		http://stackoverflow.com/questions/8489993/check-android-application-is-in-foreground-or-not
    		**/
    		ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
    		List<ActivityManager.RunningAppProcessInfo> appProcesses =
    				activityManager.getRunningAppProcesses();
    		if (appProcesses == null) {
    			return false;
    		}
    		final String packageName = context.getPackageName();
    		for (ActivityManager.RunningAppProcessInfo appProcess : appProcesses) {
    			if (appProcess.importance ==
    					ActivityManager.RunningAppProcessInfo.IMPORTANCE_FOREGROUND &&
    					appProcess.processName.equals(packageName)) {
    				return true;
    			}
    		}
    		return false;
    	}

    	public static boolean isNetworkAvailable(Context context) {
    		ConnectivityManager cm = (ConnectivityManager)
    				context.getSystemService(Context.CONNECTIVITY_SERVICE);

    		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    			Network networkCapabilities = cm.getActiveNetwork();

    			if(networkCapabilities == null) {
    				return false;
    			}

    			NetworkCapabilities actNw = cm.getNetworkCapabilities(networkCapabilities);

    			if(actNw == null) {
    				return false;
    			}

    			if(actNw.hasTransport(NetworkCapabilities.TRANSPORT_WIFI) || actNw.hasTransport(NetworkCapabilities.TRANSPORT_CELLULAR) || actNw.hasTransport(NetworkCapabilities.TRANSPORT_ETHERNET)) {
    				return true;
    			}

    			return false;
    		}

    		// deprecated in API level 29
    		NetworkInfo netInfo = cm.getActiveNetworkInfo();
    		return (netInfo != null && netInfo.isConnected());
    	}
    }
    ```

=== "Kotlin"

    ```kotlin
    import android.app.ActivityManager
    import android.app.ActivityManager.RunningAppProcessInfo
    import android.content.BroadcastReceiver
    import android.content.Context
    import android.content.Intent
    import android.net.ConnectivityManager
    import android.net.NetworkCapabilities
    import android.os.Build
    import com.facebook.react.HeadlessJsTaskService

    class NetworkChangeReceiver : BroadcastReceiver() {
    	override fun onReceive(context: Context, intent: Intent?) {
    		/**
    		* This part will be called every time network connection is changed e.g. Connected -> Not
    		* Connected
    		*/
    		if (!isAppOnForeground(context)) {
    			/** We will start our service and send extra info about network connections */
    			val hasInternet = isNetworkAvailable(context)
    			val serviceIntent = Intent(context, MyTaskService::class.java)
    			serviceIntent.putExtra("hasInternet", hasInternet)
    			context.startService(serviceIntent)
    			HeadlessJsTaskService.acquireWakeLockNow(context)
    		}
    	}

    	private fun isAppOnForeground(context: Context): Boolean {
    		/**
    		* We need to check if app is in foreground otherwise the app will crash.
    		* http://stackoverflow.com/questions/8489993/check-android-application-is-in-foreground-or-not
    		*/
    		val activityManager = context.getSystemService(Context.ACTIVITY_SERVICE) as ActivityManager
    		val appProcesses = activityManager.runningAppProcesses ?: return false
    		val packageName: String = context.getPackageName()
    		for (appProcess in appProcesses) {
    			if (appProcess.importance == RunningAppProcessInfo.IMPORTANCE_FOREGROUND &&
    					appProcess.processName == packageName
    			) {
    				return true
    			}
    		}
    		return false
    	}

    	companion object {
    		fun isNetworkAvailable(context: Context): Boolean {
    			val cm = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
    			var result = false

    			if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    				val networkCapabilities = cm.activeNetwork ?: return false

    				val actNw = cm.getNetworkCapabilities(networkCapabilities) ?: return false

    				result =
    					when {
    						actNw.hasTransport(NetworkCapabilities.TRANSPORT_WIFI) -> true
    						actNw.hasTransport(NetworkCapabilities.TRANSPORT_CELLULAR) -> true
    						actNw.hasTransport(NetworkCapabilities.TRANSPORT_ETHERNET) -> true
    						else -> false
    					}

    				return result
    			} else {
    				cm.run {
    					// deprecated in API level 29
    					cm.activeNetworkInfo?.run {
    						result =
    							when (type) {
    								ConnectivityManager.TYPE_WIFI -> true
    								ConnectivityManager.TYPE_MOBILE -> true
    								ConnectivityManager.TYPE_ETHERNET -> true
    								else -> false
    							}
    					}
    				}
    			}
    			return result
    		}
    	}
    }

    ```

[0]: https://developer.android.com/reference/android/content/Context.html#startService(android.content.Intent)
