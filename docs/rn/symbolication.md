# Символизация трассировки стека

Если приложение React Native выбрасывает необработанное исключение в сборке релиза, вывод может быть обфусцирован и трудночитаем:

```shell
07-15 10:58:25.820 18979 18998 E AndroidRuntime: FATAL EXCEPTION: mqt_native_modules
07-15 10:58:25.820 18979 18998 E AndroidRuntime: Process: com.awesomeproject, PID: 18979 07-15 10:58:25.820 18979 18998 E AndroidRuntime: com.facebook.react.common.JavascriptException: Failed, js engine: hermes, stack:
07-15 10:58:25.820 18979 18998 E AndroidRuntime: p@1:132161
07-15 10:58:25.820 18979 18998 E AndroidRuntime: p@1:132084
07-15 10:58:25.820 18979 18998 E AndroidRuntime: f@1:131854
07-15 10:58:25.820 18979 18998 E AndroidRuntime: anonymous@1:131119
```

Секции типа `p@1:132161` — это минифицированные имена функций и смещения байткода. Чтобы отладить проблему, вам нужно перевести ее в имя файла, строки и функции: `AwesomeProject/App.js:54:initializeMap`. Это известно как **символизация.** Вы можете символизировать минифицированные имена функций и байткод, как описано выше, передав `metro-symbolicate` сгенерированную карту исходного кода и трассировку стека.

!!!note ""

Пакет `metro-symbolicate` устанавливается по умолчанию в проект шаблона React Native из [настройки среды разработки](getting-started.md).

Из файла, содержащего трассировку стека:

```shell
npx metro-symbolicate android/app/build/generated/sourcemaps/react/release/index.android.bundle.map < stacktrace.txt
```

Непосредственно из `adb logcat`:

```shell
adb logcat -d | npx metro-symbolicate android/app/build/generated/sourcemaps/react/release/index.android.bundle.map
```

Это превратит каждое минифицированное имя функции и смещение типа `p@1:132161` в реальное имя файла и функции `AwesomeProject/App.js:54:initializeMap`.

## Примечания по картам исходных текстов

-   В процессе сборки может быть создано несколько карт исходных текстов. Убедитесь, что используете ту, которая находится в месте, указанном в примерах.
-   Убедитесь, что используемая вами карта исходного кода соответствует точному коммиту сбойного приложения. Небольшие изменения в исходном коде могут вызвать большие различия в смещениях.
-   Если `metro-symbolicate` немедленно завершается с успехом, убедитесь, что входные данные поступают из трубы или перенаправления, а не из терминала.
