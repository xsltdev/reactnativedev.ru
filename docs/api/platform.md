# Platform

## Пример

<div data-snack-id="@bndby/platform-api-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Свойства

### `constants`

```ts
static constants: PlatformConstants;
```

Возвращает объект, содержащий все доступные общие и специфические константы, связанные с платформой.

**Свойства:**

| Name                          | Type    | Optional | Description                                                                                                                     |
| ----------------------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------- |
| isTesting                     | boolean | No       |                                                                                                                                 |
| reactNativeVersion            | object  | No       | Information about React Native version. Keys are `major`, `minor`, `patch` with optional `prerelease` and values are `number`s. |
| Version :simple-android:      | number  | No       | OS version constant specific to Android.                                                                                        |
| Release :simple-android:      | string  | No       |                                                                                                                                 |
| Serial :simple-android:       | string  | No       | Hardware serial number of an Android device.                                                                                    |
| Fingerprint :simple-android:  | string  | No       | A string that uniquely identifies the build.                                                                                    |
| Model :simple-android:        | string  | No       | The end-user-visible name for the Android device.                                                                               |
| Brand :simple-android:        | string  | No       | The consumer-visible brand with which the product/hardware will be associated.                                                  |
| Manufacturer :simple-android: | string  | No       | The manufacturer of the Android device.                                                                                         |

### `isPad` :simple-ios:

```ts
static isPad: boolean;
```

Возвращает булево значение, определяющее, является ли устройство iPad.

| Тип    |
| ------ |
| булево |

### `isTV`

```ts
static isTV: boolean;
```

Возвращает булево значение, определяющее, является ли устройство телевизором.

| Тип    |
| ------ |
| булево |

### `isTesting`

```ts
static isTesting: boolean;
```

Возвращает булево значение, определяющее, запущено ли приложение в режиме разработчика с установленным флагом тестирования.

| Тип    |
| ------ |
| булево |

### `OS`

```ts
static OS: 'android' | 'ios';
```

Возвращает строковое значение, представляющее текущую ОС.

| Тип                        |
| -------------------------- |
| enum(`'android'`, `'ios'`) |

### `Version`

```ts
static Version: 'number' | 'string';
```

Returns the version of the OS.

| Type                                             |
| ------------------------------------------------ |
| number :simple-android:<hr />string :simple-ios: |

## Методы

### `select()`

```ts
static select(config: Record<string, T>): T;
```

Возвращает наиболее подходящее значение для платформы, на которой вы сейчас работаете.

#### Параметры:

| Имя    | Тип    | Требуемые | Описание.                       |
| ------ | ------ | --------- | ------------------------------- |
| config | object | Yes       | См. описание конфигурации ниже. |

Метод Select возвращает наиболее подходящее значение для платформы, на которой вы сейчас работаете. То есть, если вы работаете на телефоне, предпочтение будет отдано ключам `android` и `ios`. Если они не указаны, будет использован ключ `native`, а затем ключ `default`.

Параметр `config` представляет собой объект со следующими ключами:

-   `android` (любой)
-   `ios` (любой)
-   `native` (любой)
-   `default` (любой)

**Пример использования:**

```ts
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
    container: {
        flex: 1,
        ...Platform.select({
            android: {
                backgroundColor: 'green',
            },
            ios: {
                backgroundColor: 'red',
            },
            default: {
                // other platforms, web for example
                backgroundColor: 'blue',
            },
        }),
    },
});
```

В результате контейнер будет иметь `flex: 1` на всех платформах, зеленый цвет фона на Android, красный цвет фона на iOS и синий цвет фона на других платформах.

Поскольку значение соответствующего ключа платформы может быть типа `any`, метод [`select`](platform.md#select) также может быть использован для возврата специфичных для платформы компонентов, как показано ниже:

```ts
const Component = Platform.select({
    ios: () => require('ComponentIOS'),
    android: () => require('ComponentAndroid'),
})();

<Component />;
```

```ts
const Component = Platform.select({
    native: () => require('ComponentForNative'),
    default: () => require('ComponentForWeb'),
})();

<Component />;
```
