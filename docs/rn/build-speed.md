---
description: Чтобы смягчить этот удар по производительности, на этой странице представлены некоторые предложения о том, как улучшить время сборки
---

# Ускорение фазы сборки

Создание вашего приложения React Native может быть **дорогим** и занимать несколько минут времени разработчиков.

Это может быть проблематично по мере роста вашего проекта и, как правило, в больших организациях с несколькими разработчиками React Native.

Чтобы смягчить этот удар по производительности, на этой странице представлены некоторые предложения о том, как **улучшить время сборки**.

!!!info ""

    Если вы заметили замедление времени сборки с **новой архитектурой на Android**, мы рекомендуем обновить React Native до версии 0.71.

## Собирайте только один ABI во время разработки (только для Android)

При локальной сборке приложения для android, по умолчанию вы собираете все 4 [Application Binary Interfaces (ABIs)](https://developer.android.com/ndk/guides/abis): `armeabi-v7a`, `arm64-v8a`, `x86` и `x86_64`.

Однако, вероятно, вам не нужно собирать их все, если вы собираете локально и тестируете в эмуляторе или на физическом устройстве.

Это должно сократить время **нативной сборки** на ~75%.

Если вы используете React Native CLI, вы можете добавить флаг `--active-arch-only` к команде `run-android`. Этот флаг будет гарантировать, что правильный ABI будет получен либо от запущенного эмулятора, либо от подключенного телефона. Чтобы убедиться, что этот подход работает нормально, вы увидите на консоли сообщение типа `info Detected architectures arm64-v8a`.

```
$ yarn react-native run-android --active-arch-only

[ ... ]
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 1037 file(s) to forward-jetify. Using 32 workers...
info JS server already running.
info Detected architectures arm64-v8a
info Installing the app...
```

Этот механизм опирается на свойство Gradle `reactNativeArchitectures`.

Поэтому, если вы собираете непосредственно с помощью Gradle из командной строки и без CLI, вы можете указать ABI, который вы хотите собрать, следующим образом:

```shell
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

Это может быть полезно, если вы хотите собрать свое Android-приложение на CI и использовать матрицу для распараллеливания сборки на разных архитектурах.

При желании вы можете переопределить это значение локально, используя файл `gradle.properties`, который находится в [папке верхнего уровня](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33) вашего проекта:

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

Как только вы соберете **релизную версию** вашего приложения, не забудьте убрать эти флаги, поскольку вы хотите собрать apk/app bundle, работающий для всех ABI, а не только для того, который вы используете в своем ежедневном рабочем процессе разработки.

## Используйте кэш компилятора

Если вы часто используете нативные сборки (C++ или Objective-C), вам может быть полезно использовать **кэш компилятора**.

В частности, вы можете использовать два типа кэшей: локальные кэши компиляторов и распределенные кэши компиляторов.

### Локальные кэши

!!!info ""

    Следующие инструкции будут работать **как для Android, так и для iOS**.

    Если вы создаете только приложения для Android, вы должны быть готовы к работе.

    Если вы создаете также приложения для iOS, следуйте инструкциям в разделе XCode Specific Setup ниже.

Мы предлагаем использовать [**ccache**](https://ccache.dev/) для кэширования компиляции ваших нативных сборок.

Ccache работает, оборачивая компиляторы C++, сохраняя результаты компиляции и пропуская компиляцию.

если первоначально был сохранен промежуточный результат компиляции.

Чтобы установить его, вы можете следовать [официальной инструкции по установке](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md).

На macOS мы можем установить ccache с помощью `brew install ccache`.

После установки вы можете настроить его следующим образом для кэширования результатов компиляции NDK:

```
ln -s $(which ccache) /usr/local/bin/gcc
ln -s $(which ccache) /usr/local/bin/g++
ln -s $(which ccache) /usr/local/bin/cc
ln -s $(which ccache) /usr/local/bin/c++
ln -s $(which ccache) /usr/local/bin/clang
ln -s $(which ccache) /usr/local/bin/clang++
```

Это создаст символические ссылки на `ccache` внутри `/usr/local/bin/`, которые будут называться `gcc`, `g++` и так далее.

Это работает до тех пор, пока `/usr/local/bin/` стоит на первом месте, чем `/usr/bin/` в вашей переменной `$PATH`, которая используется по умолчанию.

Вы можете проверить, что это работает, используя команду `which`:

```
$ which gcc
/usr/local/bin/gcc
```

Если результатом является `/usr/local/bin/gcc`, то вы эффективно вызываете `ccache`, который обернет вызовы `gcc`.

!!!warning ""

    Обратите внимание, что такая настройка `ccache` повлияет на все компиляции, запущенные на вашей машине, а не только на те, которые связаны с React Native. Используйте ее на свой страх и риск. Если у вас не получается установить/компилировать другое программное обеспечение, причина может быть в этом. В этом случае вы можете удалить симлинк, созданный с помощью:

    ```
    unlink /usr/local/bin/gcc
    unlink /usr/local/bin/g++
    unlink /usr/local/bin/cc
    unlink /usr/local/bin/c++
    unlink /usr/local/bin/clang
    unlink /usr/local/bin/clang++
    ```

    чтобы вернуть машину в исходное состояние и использовать компиляторы по умолчанию.

Затем вы можете выполнить две чистые сборки (например, на Android вы можете сначала запустить `yarn react-native run-android`, удалить папку `android/app/build` и выполнить первую команду еще раз). Вы заметите, что вторая сборка была намного быстрее первой (она должна занимать секунды, а не минуты).

Во время сборки вы можете убедиться, что `ccache` работает правильно, и проверить количество обращений к кэшу `ccache -s`.

```
$ ccache -s
Summary:
  Hits:             196 /  3068 (6.39 %)
    Direct:           0 /  3068 (0.00 %)
    Preprocessed:   196 /  3068 (6.39 %)
  Misses:          2872
    Direct:        3068
    Preprocessed:  2872
  Uncacheable:        1
Primary storage:
  Hits:             196 /  6136 (3.19 %)
  Misses:          5940
  Cache size (GB): 0.60 / 20.00 (3.00 %)
```

Обратите внимание, что `ccache` агрегирует статистику по всем сборкам. Вы можете использовать `ccache --zero-stats`, чтобы сбросить их перед сборкой для проверки соотношения хитов кэша.

Если вам нужно очистить кэш, вы можете сделать это с помощью `ccache --clear`.

#### Специфическая настройка XCode

Чтобы убедиться, что `ccache` корректно работает с iOS и XCode, необходимо выполнить пару дополнительных шагов:

**1.** Вы должны изменить способ вызова команды компилятора в Xcode и `xcodebuild`. По умолчанию они используют _полностью указанные пути_ к двоичным файлам компилятора, поэтому символические ссылки, установленные в `/usr/local/bin`, не будут использоваться. Вы можете настроить Xcode на использование _относительных_ имен для компиляторов, используя любую из этих двух опций:

-   переменные окружения с префиксом в командной строке, если вы используете прямую командную строку: `CLANG=clang CLANGPLUSPLUS=clang++ LD=clang LDPLUSPLUS=clang++ xcodebuild <остальная часть командной строки xcodebuild>`.

-   Раздел `post_install` в вашем `ios/Podfile`, который изменяет компилятор в рабочем пространстве Xcode во время шага `pod install`:

```ruby
  post_install do |installer|
    react_native_post_install(installer)

    # ...possibly other post_install items here

    installer.pods_project.targets.each do |target|
      target.build_configurations.each do |config|
        # Using the un-qualified names means you can swap in different implementations, for example ccache
        config.build_settings["CC"] = "clang"
        config.build_settings["LD"] = "clang"
        config.build_settings["CXX"] = "clang++"
        config.build_settings["LDPLUSPLUS"] = "clang++"
      end
    end

    __apply_Xcode_12_5_M1_post_install_workaround(installer)
  end
```

**2.** Вам нужна конфигурация ccache, которая допускает определенный уровень небрежности и поведения кэша, чтобы ccache регистрировал попадания в кэш во время компиляции Xcode. Переменные конфигурации ccache, которые отличаются от стандартных, следующие, если конфигурация задается переменной окружения:

```bash
export CCACHE_SLOPPINESS=clang_index_store,file_stat_matches,include_file_ctime,include_file_mtime,ivfsoverlay,pch_defines,modules,system_headers,time_macros
export CCACHE_FILECLONE=true
export CCACHE_DEPEND=true
export CCACHE_INODECACHE=true
```

То же самое можно настроить в файле `ccache.conf` или в любом другом механизме, который предоставляет ccache. Подробнее об этом можно прочитать в [официальном руководстве по ccache](https://ccache.dev/manual/4.3.html).

#### Использование этого подхода в CI

Ccache использует папку `/Users/$USER/Library/Caches/ccache` на macOS для хранения кэша.

Поэтому вы можете сохранять и восстанавливать соответствующую папку и на CI для ускорения сборки.

Однако есть несколько моментов, о которых следует знать:

**1.** На CI мы рекомендуем делать полностью чистую сборку, чтобы избежать проблем с отравленным кэшем. Если вы будете следовать подходу, упомянутому в предыдущем абзаце, вы сможете распараллелить нативную сборку на 4 различных ABI и, скорее всего, вам не понадобится `ccache` на CI.

**2.** `ccache` полагается на временные метки для вычисления попадания в кэш. Это не очень хорошо работает на CI, так как файлы загружаются заново при каждом запуске CI. Чтобы решить эту проблему, вам нужно использовать опцию `compiler_check content`, которая вместо этого полагается на [хэширование содержимого файла](https://ccache.dev/manual/4.3.html).

### Распределенные кэши

Подобно локальным кэшам, вы можете рассмотреть возможность использования распределенного кэша для ваших собственных сборок.

Это может быть особенно полезно в больших организациях, которые часто делают сборки native.

Мы рекомендуем использовать [sccache](https://github.com/mozilla/sccache) для достижения этой цели.

Мы рекомендуем обратиться к инструкции по настройке и использованию этого инструмента в sccache [distributed compilation quickstart](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md).
