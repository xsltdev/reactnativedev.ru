# Профилирование с Hermes

Вы можете визуализировать производительность JavaScript в приложении React Native с помощью [Hermes](https://github.com/facebook/hermes). **Hermes** — это небольшой и легкий движок JavaScript, оптимизированный для запуска React Native на Android (подробнее об использовании его с React Native можно прочитать [здесь](hermes.md). Hermes помогает повысить производительность приложений, а также предоставляет способы анализа производительности выполняемого JavaScript.

В этом разделе вы узнаете, как составить профиль вашего приложения React Native, работающего на Hermes, и как визуализировать профиль с помощью [вкладки Performance в Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference)

!!!warning ""

    Перед началом работы обязательно [включите Hermes в вашем приложении](hermes.md)!

Следуйте приведенным ниже инструкциям, чтобы начать профилирование:

1.  [Запишите профиль выборки Hermes](profile-hermes.md#record-a-hermes-sampling-profile)
2.  [Выполнить команду из CLI](profile-hermes.md#execute-command-from-cli)
3.  [Открыть загруженный профиль в Chrome DevTools](profile-hermes.md#open-the-downloaded-profile-on-chrome-devtools)

## Запись профиля выборки Hermes

Чтобы записать профиль выборки из меню Dev, выполните следующие действия:

1.  Перейдите в терминал запущенного Metro-сервера.
2.  Нажмите `d`, чтобы открыть **Dev Menu.**.
3.  Выберите **Enable Sampling Profiler.**.
4.  Выполните JavaScript в вашем приложении (нажмите кнопки и т.д.).
5.  Откройте **Dev Menu**, нажав `d` еще раз.
6.  Выберите **Disable Sampling Profiler**, чтобы остановить запись и сохранить профилировщик выборки.

В тосте будет показано место сохранения профилировщика выборки, обычно в `/data/user/0/com.appName/cache/*.cpuprofile`.

![Запись профиля выборки Hermes](HermesProfileSaved.png)

## Выполнить команду из CLI

Вы можете использовать [React Native CLI](https://github.com/react-native-community/cli) для преобразования профиля трассировки Hermes в профиль трассировки Chrome, а затем извлечь его на локальную машину с помощью:

```sh
npx react-native profile-hermes [destinationDir]
```

### Включение карты источников

!!!info ""

    Вы можете прочитать о картах исходников на странице [source maps](sourcemaps.md).

### Общие ошибки

#### `adb: no devices/emulators found` или `adb: device offline`.

-   **Почему это происходит** CLI не может получить доступ к устройству или эмулятору (через adb), который вы используете для запуска приложения.
-   **Как исправить** Убедитесь, что ваше устройство/эмулятор Android подключено и работает. Команда работает только тогда, когда она может получить доступ к adb.

#### `Нет файла в каталоге cache/`.

-   **Почему это происходит** CLI не может найти файл **.cpupuprofile** в каталоге **cache/** вашего приложения. Возможно, вы забыли записать профиль с устройства.
-   **Как исправить** Следуйте [инструкциям](profile-hermes.md#record-a-hermes-sampling-profile) для включения/выключения профилировщика с устройства.

#### `Ошибка: your_profile_name.cpupuprofile is an empty file`.

-   **Почему это происходит** Профиль пуст, это может быть связано с неправильной работой Hermes.
-   **Как исправить** Убедитесь, что ваше приложение работает на последней версии Hermes.

## Откройте загруженный профиль в Chrome DevTools

Чтобы открыть профиль в Chrome DevTools, выполните следующие действия:

1.  Откройте Chrome DevTools.
2.  Выберите вкладку **Performance**.
3.  Щелкните правой кнопкой мыши и выберите **Загрузить профиль...**.

![Загрузка профиля производительности в Chrome DevTools](openChromeProfile.png)

## Как работает трансформатор профиля Hermes?

Профиль Hermes Sample Profile имеет формат `JSON object format`, в то время как формат, поддерживаемый Google's DevTools, — `JSON Array Format`. (Более подробную информацию о форматах можно найти в [Trace Event Format Document](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/preview)).

```ts
export interface HermesCPUProfile {
    traceEvents: SharedEventProperties[];
    samples: HermesSample[];
    stackFrames: { [key in string]: HermesStackFrame };
}
```

Профиль Hermes имеет большую часть информации, закодированной в свойствах `samples` и `stackFrames`. Каждый образец — это снимок стека вызовов функций в данный момент времени, поскольку каждый образец имеет свойство `sf`, которое соответствует вызову функции.

```ts
export interface HermesSample {
    cpu: string;
    name: string;
    ts: string;
    pid: number;
    tid: string;
    weight: string;
    /**
     * Will refer to an element in the stackFrames object of the Hermes Profile
     */
    sf: number;
    stackFrameData?: HermesStackFrame;
}
```

Информация о вызове функции может быть найдена в `stackFrames`, который содержит пары ключ-объект, где ключом является номер `f`, а соответствующий объект дает нам всю необходимую информацию о функции, включая номер `f` ее родительской функции. Эта связь между родителями и детьми может быть прослежена вверх, чтобы найти информацию обо всех функциях, запущенных в определенный момент времени.

```ts
export interface HermesStackFrame {
    line: string;
    column: string;
    funcLine: string;
    funcColumn: string;
    name: string;
    category: string;
    /**
     * A parent function may or may not exist
     */
    parent?: number;
}
```

На этом этапе вам следует определить еще несколько терминов, а именно:

1.  Узлы: Объекты, соответствующие номерам `sf` в `stackFrames`.
2.  Активные узлы: Узлы, которые в данный момент выполняются в определенный момент времени. Узел классифицируется как работающий, если его номер `sf` находится в стеке вызовов функции. Этот стек вызовов может быть получен из номера `sf` образца и прослежен вверх, пока не будут доступны родительские `sf`.

Затем `samples` и `stackFrames` в тандеме могут быть использованы для генерации всех событий начала и окончания в соответствующие временные метки, при этом:

1.  Начальные узлы/события: Узлы, отсутствующие в стеке вызовов функций предыдущего образца, но присутствующие в стеке текущего образца.
2.  Конечные узлы/события: Узлы, присутствующие в стеке вызовов функций предыдущего образца, но отсутствующие в текущем образце.

![Термины CallStack с пояснениями](CallStackDemo.jpg)

Теперь вы можете построить `пламенную диаграмму` вызовов функций, поскольку у вас есть вся информация о функции, включая временные метки ее начала и окончания.

Трансформатор `hermes-profile-transformer` может преобразовать любой профиль, созданный с помощью Hermes, в формат, который можно напрямую отобразить в Chrome DevTools. Более подробную информацию об этом можно найти на [`@react-native-community/hermes-profile-transformer`](https://github.com/react-native-community/hermes-profile-transformer).
