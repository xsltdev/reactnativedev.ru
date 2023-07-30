---
description: PermissionsAndroid предоставляет доступ к новой модели разрешений Android M
---

# PermissionsAndroid

!!!info "Необходим проект с нативным кодом"

    Следующая глава относится только к проектам с открытым кодом. Если вы используете управляемый рабочий процесс Expo, смотрите руководство по [Разрешениям](https://docs.expo.dev/guides/permissions/) в документации Expo для соответствующей альтернативы.

**`PermissionsAndroid`** предоставляет доступ к новой модели разрешений Android M. Так называемые "нормальные" разрешения предоставляются по умолчанию при установке приложения, если они есть в файле `AndroidManifest.xml`. Однако "опасные" разрешения требуют диалогового запроса. Для таких разрешений следует использовать этот модуль.

На устройствах до версии SDK 23 разрешения предоставляются автоматически, если они есть в манифесте, поэтому `check` всегда должен приводить к `true`, а `request` всегда должен разрешаться в `PermissionsAndroid.RESULTS.GRANTED`.

Если пользователь ранее отключил разрешение, которое вы запрашиваете, ОС посоветует вашему приложению показать обоснование необходимости этого разрешения. Необязательный аргумент `rationale` покажет диалоговое приглашение только в случае необходимости — в противном случае появится обычное приглашение разрешения.

## Пример {#example}

<div data-snack-id="@bndby/permissionsandroid-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### Разрешения, требующие запроса пользователя {#permissions-that-require-prompting-the-user}

Доступны как константы в разделе `PermissionsAndroid.PERMISSIONS`:

-   `READ_CALENDAR`: 'android.permission.READ_CALENDAR'
-   `WRITE_CALENDAR`: 'android.permission.WRITE_CALENDAR'
-   `CAMERA`: 'android.permission.CAMERA'
-   `READ_CONTACTS`: 'android.permission.READ_CONTACTS'
-   `WRITE_CONTACTS`: 'android.permission.WRITE_CONTACTS'
-   `GET_ACCOUNTS`: 'android.permission.GET_ACCOUNTS'
-   `ACCESS_FINE_LOCATION`: 'android.permission.ACCESS_FINE_LOCATION'
-   `ACCESS_COARSE_LOCATION`: 'android.permission.ACCESS_COARSE_LOCATION'
-   `ACCESS_BACKGROUND_LOCATION`: 'android.permission.ACCESS_BACKGROUND_LOCATION'
-   `RECORD_AUDIO`: 'android.permission.RECORD_AUDIO'
-   `READ_PHONE_STATE`: 'android.permission.READ_PHONE_STATE'
-   `CALL_PHONE`: 'android.permission.CALL_PHONE'
-   `READ_CALL_LOG`: 'android.permission.READ_CALL_LOG'
-   `WRITE_CALL_LOG`: 'android.permission.WRITE_CALL_LOG'
-   `WRITE_CALL_LOG`: 'android.permission.WRITE_CALL_LOG'
-   `ADD_VOICEMAIL`: 'com.android.voicemail.permission.ADD_VOICEMAIL'
-   `USE_SIP`: 'android.permission.USE_SIP'
-   `PROCESS_OUTGOING_CALLS`: 'android.permission.PROCESS_OUTGOING_CALLS'
-   `BODY_SENSORS`: 'android.permission.BODY_SENSORS'
-   `SEND_SMS`: 'android.permission.SEND_SMS'
-   `RECEIVE_SMS`: 'android.permission.RECEIVE_SMS'
-   `READ_SMS`: 'android.permission.READ_SMS'
-   `RECEIVE_WAP_PMS`: 'android.permission.RECEIVE_SMS'
-   `RECEIVE_WAP_PUSH`: 'android.permission.RECEIVE_WAP_PUSH'
-   `RECEIVE_MMS`: 'android.permission.RECEIVE_MMS'
-   `READ_EXTERNAL_SMS`: 'android.permission.READ_SMS'
-   `READ_EXTERNAL_STORAGE`: 'android.permission.READ_EXTERNAL_STORAGE'
-   `WRITE_EXTERNAL_STORAGE`: 'android.permission.WRITE_EXTERNAL_STORAGE'
-   `BLUETOOTH_CONNECT`: 'android.permission.BLUETOOTH_CONNECT'
-   `BLUETOOTH_SCAN`: 'android.permission.BLUETOOTH_SCAN'
-   `BLUETOOTH_ADVERTISE`: 'android.permission.BLUETOOTH_ADVERTISE'
-   `ACCESS_MEDIA_LOCATION`: 'android.permission.ACCESS_MEDIA_LOCATION'
-   `ACCEPT_HANDOVER`: 'android.permission.ACCEPT_HANDOVER'
-   `ACTIVITY_RECOGNITION`: 'android.permission.ACTIVITY_RECOGNITION'
-   `ANSWER_PHONE_CALLS`: 'android.permission.ANSWER_PHONE_CALLS'
-   `READ_PHONE_NUMBERS`: 'android.permission.READ_PHONE_NUMBERS'
-   `UWB_RANGING`: 'android.permission.UWB_RANGING'
-   `BODY_SENSORS_BACKGROUND`: 'android.permission.BODY_SENSORS_BACKGROUND'
-   `READ_MEDIA_IMAGES`: 'android.permission.READ_MEDIA_IMAGES'
-   `READ_MEDIA_VIDEO`: 'android.permission.READ_MEDIA_VIDEO'
-   `READ_MEDIA_AUDIO`: 'android.permission.READ_MEDIA_AUDIO'
-   `POST_NOTIFICATIONS`: 'android.permission.POST_NOTIFICATIONS'
-   `NEARBY_WIFI_DEVICES`: 'android.permission.NEARBY_WIFI_DEVICES'
-   `READ_VOICEMAIL`: 'com.android.voicemail.permission.READ_VOICEMAIL'
-   `WRITE_VOICEMAIL`: 'com.android.voicemail.permission.WRITE_VOICEMAIL'

### Строки результатов для запроса разрешений {#result-strings-for-requesting-permissions}

Доступны как константы в разделе `PermissionsAndroid.RESULTS`:

-   `GRANTED`: 'разрешено'
-   `DENIED`: 'отказано'
-   `NEVER_ASK_AGAIN`: 'never_ask_again'

## Методы {#methods}

### check()

```ts
static check(permission: Permission): Promise<boolean>;
```

Возвращает обещание, разрешающее булево значение о том, были ли предоставлены указанные разрешения.

**Параметры:**

| Имя                        | Тип      | Описание                                  |
| -------------------------- | -------- | ----------------------------------------- |
| `permission` (обязательно) | `string` | Разрешение, которое необходимо проверить. |

### request()

```ts
static request(
  permission: Permission,
  rationale?: Rationale,
): Promise<PermissionStatus>;
```

Предлагает пользователю включить разрешение и возвращает обещание, разрешающееся в строковое значение (см. строки результата выше), указывающее, разрешил ли пользователь запрос или отклонил его, или не хочет, чтобы его спрашивали снова.

Если указано `rationale`, эта функция проверяет в ОС, нужно ли показать диалог, объясняющий, [зачем нужно разрешение](https://developer.android.com/training/permissions/requesting.html#explain), а затем показывает системный диалог разрешения.

**Параметры:**

| Имя          | Тип      | Обязательно | Описание              |
| ------------ | -------- | ----------- | --------------------- |
| `permission` | `string` | Да          | Разрешение на запрос. |
| `rationale`  | `object` | Нет         | См. `rationale` ниже. |

**Обоснование:**

| Имя              | Тип      | Обязательно | Описание                  |
| ---------------- | -------- | ----------- | ------------------------- |
| `title`          | `string` | Да          | Заголовок диалога.        |
| `message`        | `string` | Да          | Сообщение диалога.        |
| `buttonPositive` | `string` | Да          | Текст позитивной кнопки.  |
| `buttonNegative` | `string` | Нет         | Текст негативной кнопки.  |
| `buttonNeutral`  | `string` | Нет         | Текст нейтральной кнопки. |

### requestMultiple()

```ts
static requestMultiple(
  permissions: Permission[],
): Promise<{[key in Permission]: PermissionStatus}>;
```

Предлагает пользователю включить несколько разрешений в одном диалоге и возвращает объект с разрешениями в качестве ключей и строками в качестве значений (см. строки результата выше), указывая, разрешил ли пользователь или отклонил запрос, или не хочет, чтобы его спрашивали снова.

**Параметры:**

| Имя           | Тип     | Обязательно | Описание                         |
| ------------- | ------- | ----------- | -------------------------------- |
| `permissions` | `array` | Да          | Массив запрашиваемых разрешений. |
