---
description: AppRegistry — это точка входа JS для запуска всех приложений React Native
---

# AppRegistry

!!!note "Требуется проект с нативным кодом"

    Если вы используете управляемый рабочий процесс Expo, то только один входной компонент, зарегистрированный в `AppRegistry`, и он обрабатывается автоматически (или через [registerRootComponent](https://docs.expo.dev/versions/latest/sdk/register-root-component/)). Вам не нужно использовать этот API.

**`AppRegistry`** — это точка входа JS для запуска всех приложений React Native. Корневые компоненты приложения должны зарегистрировать себя с помощью `AppRegistry.registerComponent`, после чего нативная система может загрузить пакет для приложения и затем фактически запустить приложение, когда оно будет готово, вызвав `AppRegistry.runApplication`.

```ts
import { Text, AppRegistry } from 'react-native';

const App = () => (
    <View>
        <Text>App1</Text>
    </View>
);

AppRegistry.registerComponent('Appname', () => App);
```

Чтобы "остановить" приложение, когда представление должно быть уничтожено, вызовите `AppRegistry.unmountApplicationComponentAtRootTag` с тегом, который был передан в `runApplication`. Их всегда следует использовать в паре.

`AppRegistry` следует требовать в самом начале последовательности `require`, чтобы убедиться, что среда выполнения JS настроена до того, как потребуются другие модули.

## Методы {#methods}

### getAppKeys()

```ts
static getAppKeys(): string[];
```

Возвращает массив строк.

### getRegistry()

```ts
static getRegistry(): {sections: string[]; runnables: Runnable[]};
```

Возвращает объект [Registry](appregistry.md#registry).

### getRunnable()

```ts
static getRunnable(appKey: string): : Runnable | undefined;
```

Возвращает объект [`Runnable`](appregistry.md#runnable).

**Параметры:**

| Имя                    | Тип      |
| ---------------------- | -------- |
| `appKey` (обязательно) | `string` |

### getSectionKeys()

```ts
static getSectionKeys(): string[];
```

Возвращает массив строк.

### getSections()

```ts
static getSections(): Record<string, Runnable>;
```

Возвращает объект [`Runnables`](appregistry.md#runnables).

### registerCancellableHeadlessTask()

```ts
static registerCancellableHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
  taskCancelProvider: TaskCancelProvider,
);
```

Зарегистрируйте безголовую задачу, которую можно отменить. Безголовая задача — это часть кода, которая выполняется без пользовательского интерфейса.

**Параметры:**

| Имя                                    | Тип                                                     | Описание                                                                                                                                                                                                                                                                        |
| -------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `taskKey`<br/>(обязательно)            | `string`                                                | Собственный идентификатор для данного экземпляра задачи, который использовался при вызове `startHeadlessTask`.                                                                                                                                                                  |
| `taskProvider`<br/>(обязательно)       | [`TaskProvider`](appregistry.md#taskprovider)           | Функция возврата обещания, принимающая в качестве единственного аргумента некоторые данные, переданные с нативной стороны. Когда обещание разрешается или отклоняется, родная сторона получает уведомление об этом событии и может принять решение об уничтожении JS-контекста. |
| `taskCancelProvider`<br/>(обязательно) | [TaskCancelProvider](appregistry.md#taskcancelprovider) | возвращающая пустоту функция, не принимающая аргументов; когда запрашивается отмена, функция, выполняемая `taskProvider`, должна сворачиваться и возвращаться как можно скорее.                                                                                                 |

### registerComponent()

```ts
static registerComponent(
  appKey: string,
  getComponentFunc: ComponentProvider,
  section?: boolean,
): string;
```

**Параметры:**

| Имя                               | Тип                 |
| --------------------------------- | ------------------- |
| `appKey` (обязательно)            | `string`            |
| `componentProvider` (обязательно) | `ComponentProvider` |
| `section`                         | `boolean`           |

### registerConfig()

```ts
static registerConfig(config: AppConfig[]);
```

**Параметры:**

| Имя                    | Тип                                       |
| ---------------------- | ----------------------------------------- |
| `config` (обязательно) | [`AppConfig`](appregistry.md#appconfig)[] |

### registerHeadlessTask()

```ts
static registerHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
);
```

Зарегистрируйте безголовое задание. _Безголовая задача_ — это часть кода, которая выполняется без пользовательского интерфейса.

Это способ запуска задач на JavaScript, пока ваше приложение находится в фоновом режиме. Его можно использовать, например, для синхронизации свежих данных, обработки push-уведомлений или воспроизведения музыки.

**Параметры:**

| Имя                          | Тип                                           | Описание                                                                                                                                                                                                                                                                        |
| ---------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `taskKey` (обязательно)      | `string`                                      | Собственный идентификатор для данного экземпляра задачи, который использовался при вызове `startHeadlessTask`.                                                                                                                                                                  |
| `taskProvider` (обязательно) | [`TaskProvider`](appregistry.md#taskprovider) | Функция возврата обещания, принимающая в качестве единственного аргумента некоторые данные, переданные с нативной стороны. Когда обещание разрешается или отклоняется, родная сторона получает уведомление об этом событии и может принять решение об уничтожении JS-контекста. |

### registerRunnable()

```ts
static registerRunnable(appKey: string, func: Runnable): string;
```

**Параметры:**

| Имя                    | Тип        |
| ---------------------- | ---------- |
| `appKey` (обязательно) | `string`   |
| `run` (обязательно)    | `function` |

### registerSection()

```ts
static registerSection(
  appKey: string,
  component: ComponentProvider,
);
```

**Параметры:**

| Имя                       | Тип                 |
| ------------------------- | ------------------- |
| `appKey` (обязательно)    | `string`            |
| `component` (обязательно) | `ComponentProvider` |

### runApplication()

```ts
static runApplication(appKey: string, appParameters: any): void;
```

Загружает пакет JavaScript и запускает приложение.

**Параметры:**

| Имя                           | Тип      |
| ----------------------------- | -------- |
| `appKey` (обязательно)        | `string` |
| `appParameters` (обязательно) | `any`    |

### setComponentProviderInstrumentationHook()

```ts
static setComponentProviderInstrumentationHook(
  hook: ComponentProviderInstrumentationHook,
);
```

**Параметры:**

| Имя                  | Тип        |
| -------------------- | ---------- |
| `hook` (обязательно) | `function` |

Правильная функция `hook` принимает в качестве аргументов следующее:

| Имя                                     | Тип                 |
| --------------------------------------- | ------------------- |
| `component` (обязательно)               | `ComponentProvider` |
| `scopedPerformanceLogger` (обязательно) | IPerfo`rmanceLogger |

Функция также должна возвращать React Component.

### setWrapperComponentProvider()

```ts
static setWrapperComponentProvider(
  provider: WrapperComponentProvider,
);
```

**Параметры:**

| Имя                      | Тип                 |
| ------------------------ | ------------------- |
| `provider` (обязательно) | `ComponentProvider` |

### startHeadlessTask()

```ts
static startHeadlessTask(
  taskId: number,
  taskKey: string,
  data: any,
);
```

Вызывается только из нативного кода. Запускает задачу без головы.

**Параметры:**

| Имя                     | Тип      | Описание                                                                          |
| ----------------------- | -------- | --------------------------------------------------------------------------------- |
| `taskId` (обязательно)  | `number` | Нативный идентификатор данного экземпляра задачи для отслеживания его выполнения. |
| `taskKey` (обязательно) | `string` | Ключ для запуска задачи.                                                          |
| `data` (обязательно)    | `any`    | Данные для передачи в задачу.                                                     |

### unmountApplicationComponentAtRootTag()

```ts
static unmountApplicationComponentAtRootTag(rootTag: number);
```

Останавливает приложение, когда представление должно быть уничтожено.

**Параметры:**

| Имя                     | Тип      |
| ----------------------- | -------- |
| `rootTag` (обязательно) | `number` |

## Определения типов

### AppConfig

Конфигурация приложения для метода `registerConfig`.

| Тип      |
| -------- |
| `object` |

**Параметры:**

| Имя                    | Тип                 |
| ---------------------- | ------------------- |
| `appKey` (обязательно) | `string`            |
| `component`            | `ComponentProvider` |
| `run`                  | `function`          |
| `section`              | `boolean`           |

!!!note ""

    Ожидается, что в каждом конфиге будет установлена либо функция `component`, либо `run`.

### Registry

| Тип      |
| -------- |
| `object` |

**Параметры:**

| Имя         | Тип                                           |
| ----------- | --------------------------------------------- |
| `runnables` | массив [`Runnables`](appregistry.md#runnable) |
| `sections`  | массив строк                                  |

### Runnable

| Тип      |
| -------- |
| `object` |

**Свойства:**

| Имя         | Тип                 |
| ----------- | ------------------- |
| `component` | `ComponentProvider` |
| `run`       | `function`          |

### Runnables

Объект с ключом `appKey` и значением типа [`Runnable`](appregistry.md#runnable).

| Тип      |
| -------- |
| `object` |

### Task

`Task` — это функция, которая принимает любые данные в качестве аргумента и возвращает `Promise`, который разрешается в `undefined`.

| Тип        |
| ---------- |
| `function` |

### TaskCanceller

Функция `TaskCanceller` — это функция, которая не принимает никаких аргументов и возвращает `void`.

| Тип        |
| ---------- |
| `function` |

### TaskCancelProvider

Правильный `TaskCancelProvider` — это функция, которая возвращает [`TaskCanceller`](appregistry.md#taskcanceller).

| Тип        |
| ---------- |
| `function` |

### TaskProvider

Правильный `TaskProvider` — это функция, которая возвращает [`Task`](appregistry.md#task).

| Тип        |
| ---------- |
| `function` |
