---
description: Модуль Platform дает доступ к константам и методам, связанными с платформой
---

# Platform

## Пример {#example}

<div data-snack-id="@bndby/platform-api-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Свойства {#properties}

### constants

```ts
static constants: PlatformConstants;
```

Возвращает объект, содержащий все доступные общие и специфические константы, связанные с платформой.

**Свойства:**

| Имя                             | Тип       | Описание                                                                                                                             |
| ------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `isTesting`                     | `boolean` |                                                                                                                                      |
| `reactNativeVersion`            | `object`  | Информация о версии React Native. Ключами являются `major`, `minor`, `patch` с необязательным `prerelease`, а значениями - `number`. |
| `Version` :simple-android:      | `number`  | Константа версии ОС, характерная для Android.                                                                                        |
| `Release` :simple-android:      | `string`  |                                                                                                                                      |
| `Serial` :simple-android:       | `string`  | Аппаратный серийный номер устройства Android.                                                                                        |
| `Fingerprint` :simple-android:  | `string`  | Строка, уникально идентифицирующая сборку.                                                                                           |
| `Model` :simple-android:        | `string`  | Имя устройства Android, видимое конечным пользователем.                                                                              |
| `Brand` :simple-android:        | `string`  | Видимый потребителю бренд, с которым будет ассоциироваться продукт/оборудование.                                                     |
| `Manufacturer` :simple-android: | `string`  | Производитель устройства Android.                                                                                                    |

### isPad :simple-ios:

```ts
static isPad: boolean;
```

Возвращает булево значение, определяющее, является ли устройство iPad.

| Тип       |
| --------- |
| `boolean` |

### isTV

```ts
static isTV: boolean;
```

Возвращает булево значение, определяющее, является ли устройство телевизором.

| Тип       |
| --------- |
| `boolean` |

### isTesting

```ts
static isTesting: boolean;
```

Возвращает булево значение, определяющее, запущено ли приложение в режиме разработчика с установленным флагом тестирования.

| Тип       |
| --------- |
| `boolean` |

### OS

```ts
static OS: 'android' | 'ios';
```

Возвращает строковое значение, представляющее текущую ОС.

| Тип                      |
| ------------------------ |
| `enum('android', 'ios')` |

### Version

```ts
static Version: 'number' | 'string';
```

Returns the version of the OS.

| Тип                                                  |
| ---------------------------------------------------- |
| `number` :simple-android:<hr />`string` :simple-ios: |

## Методы {#methods}

### select()

```ts
static select(config: Record<string, T>): T;
```

Возвращает наиболее подходящее значение для платформы, на которой вы сейчас работаете.

**Параметры:**

| Имя                    | Тип      | Описание                        |
| ---------------------- | -------- | ------------------------------- |
| `config` (обязательно) | `object` | См. описание конфигурации ниже. |

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
