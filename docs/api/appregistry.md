# AppRegistry

!!!note "Требуется проект с нативным кодом"

    Если вы используете управляемый рабочий процесс Expo, то только один входной компонент, зарегистрированный в `AppRegistry`, и он обрабатывается автоматически (или через [registerRootComponent](https://docs.expo.dev/versions/latest/sdk/register-root-component/)). Вам не нужно использовать этот API.

`AppRegistry` — это точка входа JS для запуска всех приложений React Native. Корневые компоненты приложения должны зарегистрировать себя с помощью `AppRegistry.registerComponent`, после чего нативная система может загрузить пакет для приложения и затем фактически запустить приложение, когда оно будет готово, вызвав `AppRegistry.runApplication`.

```tsx
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

## Методы

### `getAppKeys()`

```tsx
static getAppKeys(): string[];
```

Возвращает массив строк.

### `getRegistry()`

```tsx
static getRegistry(): {sections: string[]; runnables: Runnable[]};
```

Возвращает объект [Registry](appregistry.md#registry).

### `getRunnable()`

```tsx
static getRunnable(appKey: string): : Runnable | undefined;
```

Возвращает объект [Runnable](appregistry.md#runnable).

**Параметры:**

| Name                 | Type   |
| -------------------- | ------ |
| appKey (обязательно) | string |

### `getSectionKeys()`

```tsx
static getSectionKeys(): string[];
```

Возвращает массив строк.

### `getSections()`

```tsx
static getSections(): Record<string, Runnable>;
```

Возвращает объект [Runnables](appregistry.md#runnables).

### `registerCancellableHeadlessTask()`

```tsx
static registerCancellableHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
  taskCancelProvider: TaskCancelProvider,
);
```

Зарегистрируйте безголовую задачу, которую можно отменить. Безголовая задача — это часть кода, которая выполняется без пользовательского интерфейса.

**Параметры:**

| Name                                 | Type                                                    | Description                                                                                                                                                                                                                                                                     |
| ------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey<br/>(обязательно)            | string                                                  | Собственный идентификатор для данного экземпляра задачи, который использовался при вызове `startHeadlessTask`.                                                                                                                                                                  |
| taskProvider<br/>(обязательно)       | [`TaskProvider`](appregistry.md#taskprovider)           | Функция возврата обещания, принимающая в качестве единственного аргумента некоторые данные, переданные с нативной стороны. Когда обещание разрешается или отклоняется, родная сторона получает уведомление об этом событии и может принять решение об уничтожении JS-контекста. |
| taskCancelProvider<br/>(обязательно) | [TaskCancelProvider](appregistry.md#taskcancelprovider) | возвращающая пустоту функция, не принимающая аргументов; когда запрашивается отмена, функция, выполняемая taskProvider, должна сворачиваться и возвращаться как можно скорее.                                                                                                   |

### `registerComponent()`

```tsx
static registerComponent(
  appKey: string,
  getComponentFunc: ComponentProvider,
  section?: boolean,
): string;
```

**Параметры:**

| Name                            | Type              |
| ------------------------------- | ----------------- |
| appKey (обязательно)            | string            |
| componentProvider (обязательно) | ComponentProvider |
| section                         | boolean           |

### `registerConfig()`

```tsx
static registerConfig(config: AppConfig[]);
```

**Параметры:**

| Name                 | Type                                    |
| -------------------- | --------------------------------------- |
| config (обязательно) | [AppConfig](appregistry.md#appconfig)[] |

### `registerHeadlessTask()`

```tsx
static registerHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
);
```

Зарегистрируйте безголовое задание. Безголовая задача — это часть кода, которая выполняется без пользовательского интерфейса.

Это способ запуска задач на JavaScript, пока ваше приложение находится в фоновом режиме. Его можно использовать, например, для синхронизации свежих данных, обработки push-уведомлений или воспроизведения музыки.

**Параметры:**

| Name                       | Type                                        | Description                                                                                                                                                                                                                                                                     |
| -------------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey (обязательно)      | string                                      | Собственный идентификатор для данного экземпляра задачи, который использовался при вызове startHeadlessTask.                                                                                                                                                                    |
| taskProvider (обязательно) | [TaskProvider](appregistry.md#taskprovider) | Функция возврата обещания, принимающая в качестве единственного аргумента некоторые данные, переданные с нативной стороны. Когда обещание разрешается или отклоняется, родная сторона получает уведомление об этом событии и может принять решение об уничтожении JS-контекста. |

### `registerRunnable()`

```tsx
static registerRunnable(appKey: string, func: Runnable): string;
```

**Параметры:**

| Name                 | Type     |
| -------------------- | -------- |
| appKey (обязательно) | string   |
| run (обязательно)    | function |

### `registerSection()`

```tsx
static registerSection(
  appKey: string,
  component: ComponentProvider,
);
```

**Параметры:**

| Name                    | Type              |
| ----------------------- | ----------------- |
| appKey (обязательно)    | string            |
| component (обязательно) | ComponentProvider |

### `runApplication()`

```tsx
static runApplication(appKey: string, appParameters: any): void;
```

Загружает пакет JavaScript и запускает приложение.

**Параметры:**

| Name                        | Type   |
| --------------------------- | ------ |
| appKey (обязательно)        | string |
| appParameters (обязательно) | any    |

### `setComponentProviderInstrumentationHook()`

```tsx
static setComponentProviderInstrumentationHook(
  hook: ComponentProviderInstrumentationHook,
);
```

**Параметры:**

| Name               | Type     |
| ------------------ | -------- |
| hook (обязательно) | function |

Правильная функция `hook` принимает в качестве аргументов следующее:

| Name                                  | Type               |
| ------------------------------------- | ------------------ |
| component (обязательно)               | ComponentProvider  |
| scopedPerformanceLogger (обязательно) | IPerformanceLogger |

Функция также должна возвращать React Component.

### `setWrapperComponentProvider()`

```tsx
static setWrapperComponentProvider(
  provider: WrapperComponentProvider,
);
```

**Параметры:**

| Name                   | Type              |
| ---------------------- | ----------------- |
| provider (обязательно) | ComponentProvider |

### `startHeadlessTask()`

```tsx
static startHeadlessTask(
  taskId: number,
  taskKey: string,
  data: any,
);
```

Вызывается только из нативного кода. Запускает задачу без головы.

**Параметры:**

| Name                  | Type   | Description                                                          |
| --------------------- | ------ | -------------------------------------------------------------------- |
| taskId (обязательно)  | number | The native id for this task instance to keep track of its execution. |
| taskKey (обязательно) | string | The key for the task to start.                                       |
| data (обязательно)    | any    | The data to pass to the task.                                        |

### `unmountApplicationComponentAtRootTag()`

```tsx
static unmountApplicationComponentAtRootTag(rootTag: number);
```

Останавливает приложение, когда представление должно быть уничтожено.

**Параметры:**

| Name                  | Type   |
| --------------------- | ------ |
| rootTag (обязательно) | number |

## Определения типов

### AppConfig

Конфигурация приложения для метода `registerConfig`.

| Type   |
| ------ |
| object |

**Параметры:**

| Name                 | Type              |
| -------------------- | ----------------- |
| appKey (обязательно) | string            |
| component            | ComponentProvider |
| run                  | function          |
| section              | boolean           |

!!!note ""

    Ожидается, что в каждом конфиге будет установлена либо функция `component`, либо `run`.

### Registry

| Type   |
| ------ |
| object |

**Параметры:**

| Name      | Type                                          |
| --------- | --------------------------------------------- |
| runnables | array of [Runnables](appregistry.md#runnable) |
| sections  | массив строк                                  |

### Runnable

| Тип    |
| ------ |
| объект |

**Свойства:**

| Имя       | Тип               |
| --------- | ----------------- |
| компонент | ComponentProvider |
| run       | function          |

### Runnables

Объект с ключом `appKey` и значением типа [`Runnable`](appregistry.md#runnable).

| Тип    |
| ------ |
| объект |

### Task

`Task` — это функция, которая принимает любые данные в качестве аргумента и возвращает `Promise`, который разрешается в `undefined`.

| Тип     |
| ------- |
| функция |

### TaskCanceller

Функция `TaskCanceller` — это функция, которая не принимает никаких аргументов и возвращает `void`.

| Type    |
| ------- |
| функция |

### TaskCancelProvider

Правильный `TaskCancelProvider` — это функция, которая возвращает [`TaskCanceller`](appregistry.md#taskcanceller).

| Тип     |
| ------- |
| функция |

### TaskProvider

Правильный `TaskProvider` — это функция, которая возвращает [`Task`](appregistry.md#task).

| Type    |
| ------- |
| функция |
