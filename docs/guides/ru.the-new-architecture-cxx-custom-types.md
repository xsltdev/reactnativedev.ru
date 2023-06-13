

---
id: cxx-custom-types
title: Supporting Custom C++ Types
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

import constants from '@site/core/TabsConstants';

import NewArchitectureWarning from '../\_markdown-new-architecture-warning.mdx';

<NewArchitectureWarning/>

По умолчанию C++ Turbo Native Modules поддерживает [bridging functionality](https://github.com/facebook/react-native/tree/main/packages/react-native/ReactCommon/react/bridging) для большинства `std::` стандартных типов.



Если вы хотите добавить поддержку новых / пользовательских типов в ваше приложение / библиотеку, вам нужно только предоставить необходимый заголовочный файл `bridging`.




Это руководство продолжает предыдущий раздел [C++ Turbo Native Modules](./cxx-cxxturbomodules).




## Пример: Int64




C++ Turbo Native Modules пока не поддерживает числа `int64_t` - потому что JavaScript не поддерживает числа больше `2^53`.




Мы не можем представлять числа > `2^53` как JavaScript `числа` - но мы можем представлять их как JavaScript `строки` и автоматически преобразовывать (aka `bridge`) их в C++ `int64_t`, создав пользовательский заголовочный файл Bridging под названием `Int64.h` в папке `tm`:

```cpp Int64.h
#pragma once

#include <react/bridging/Bridging.h>

namespace facebook::react {

template <>
struct Bridging<int64_t> {
  static int64_t fromJs(jsi::Runtime &rt, const jsi::String &value) {
    try {
      size_t pos;
      auto str = value.utf8(rt);
      auto num = std::stoll(str, &pos);
      if (pos != str.size()) {
        throw std::invalid_argument("Invalid number"); // don't support alphanumeric strings
      }
      return num;
    } catch (const std::logic_error &e) {
      throw jsi::JSError(rt, e.what());
    }
  }

  static jsi::String toJs(jsi::Runtime &rt, int64_t value) {
    return bridging::toJs(rt, std::to_string(value));
  }
};

} // namespace facebook::react
```

Ключевыми компонентами для вашего пользовательского заголовка `bridging` являются:



- Явная специализация структуры `Bridging` для вашего пользовательского типа, в данном случае `int64_t`.

- Функция `fromJs` для преобразования типов `jsi::` в ваш пользовательский тип

- Функция `toJS` для преобразования из вашего пользовательского типа в типы `jsi:`.




Если опустить `fromJS` или `toJS`, то вы сделаете `бридинг` заголовка либо _readonly_, либо _writeonly_.




Теперь вы можете добавить следующую функцию в вашу спецификацию JavaScript:

<Tabs groupId="turbomodule-specs" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

```typescript title="NativeSampleModule.ts"
   // ...
   readonly cubicRoot: (input: string) => number;
   // ..
```

</TabItem>
<TabItem value="flow">

```js title="NativeSampleModule.js"
   // ...
   +cubicRoot: (input: string) => number;
   // ..
```

</TabItem>
</Tabs>

Объявите его в файле `NativeSampleModule.h` и включите заголовочный файл `Int64.h`:

```cpp
//...
#include "Int64.h"
//...
int32_t cubicRoot(jsi::Runtime& rt, int64_t input);
```

И реализуйте его в `NativeSampleModule.cpp`:

```cpp
//...
#include <cmath>
//...
int32_t NativeSampleModule::cubicRoot(jsi::Runtime& rt, int64_t input) {
    return std::cbrt(input);
}
```

В своем приложении вы можете вызвать эту новую нативную функцию через:

```js
<Section title="Cxx TurboModule">
  NativeSampleModule.cubicRoot(...) ={' '}
  {JSON.stringify(
    NativeSampleModule.cubicRoot('9223372036854775807'),
  )}
</Section>
```

который должен вернуть `2097152`.



## Любой пользовательский тип




Аналогично приведенному выше примеру, теперь вы можете написать пользовательскую функциональность `bridging` для любого пользовательского типа C++, который вы хотите передать в react-native. Например, вы можете добавить поддержку `folly::StringPiece`, `QString`, `boost::filesystem::path`, `absl::optional` или любого другого типа, который вам нужно поддерживать в ваших C++ Turbo Native модулях.

```cpp title="Path.h"
#pragma once

#include <react/bridging/Bridging.h>
#include <boost/filesystem.hpp>

namespace facebook::react {

template<>
struct Bridging<boost::filesystem::path> {
  static boost::filesystem::path fromJs(jsi::Runtime& rt, const std::string& value) { // auto-bridge from jsi::String to std::string
    return boost::filesystem::path(value);
  }

  static jsi::String toJs(jsi::Runtime& rt, boost::filesystem::path value) {
    return bridging::toJs(rt, value.string());
  }
};

} // namespace facebook::react
```

## Пользовательские структуры



Вы можете использовать тот же подход для пользовательских типов в JavaScript, как, например, этот:

```js
export type CustomType = {
  key: string,
  enabled: boolean,
  time?: number,
};
```

который может быть открыт для вашего нативного модуля C++ Turbo через

<Tabs groupId="turbomodule-specs" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

```typescript title="NativeSampleModule.ts"
   // ...
   readonly passCustomType: (input: CustomType) => CustomType;
   // ..
```

</TabItem>
<TabItem value="flow">

```js title="NativeSampleModule.js"
   // ...
   +passCustomType: (input: CustomType) => CustomType;
   // ..
```

</TabItem>
</Tabs>

### Ручная типизация



Чтобы использовать этот пользовательский тип в C++, вам нужно определить вашу пользовательскую Struct и `bridging` функцию, например, непосредственно в `NativeSampleModule.h`:

```cpp
struct CustomType {
  std::string key;
  bool enabled;
  std::optional<int32_t> time;
};

template <>
struct Bridging<CustomType> {
  static CustomType fromJs(
      jsi::Runtime &rt,
      const jsi::Object &value,
      const std::shared_ptr<CallInvoker> &jsInvoker) {
    return CustomType{
        bridging::fromJs<std::string>(
            rt, value.getProperty(rt, "key"), jsInvoker),
        bridging::fromJs<bool>(
            rt, value.getProperty(rt, "enabled"), jsInvoker),
        bridging::fromJs<std::optional<int32_t>>(
            rt, value.getProperty(rt, "time"), jsInvoker)};
  }

  static jsi::Object toJs(jsi::Runtime &rt, const CustomType &value) {
    auto result = facebook::jsi::Object(rt);
    result.setProperty(rt, "key", bridging::toJs(rt, value.key));
    result.setProperty(rt, "enabled", bridging::toJs(rt, value.enabled));
    if (value.time) {
      result.setProperty(rt, "time", bridging::toJs(rt, value.time.value()));
    }
    return result;
  }
};
```

Объявите его в вашем файле `NativeSampleModule.h`:

```cpp
  CustomType passCustomType(jsi::Runtime& rt, CustomType input);
```

Реализуйте его в `NativeSampleModule.cpp`:

```cpp
CustomType NativeSampleModule::passCustomType(jsi::Runtime& rt, CustomType input) {
  input.key = "1909";
  input.enabled = !input.enabled;
  input.time = 42;
  return input;
}
```

В своем приложении вы можете вызвать эту новую нативную функцию через:

```js
<Section title="Cxx TurboModule">
  NativeSampleModule.passCustomType(...) ={' '}
  {JSON.stringify(
    NativeSampleModule.passCustomType({
      key: '123',
      enabled: true,
      time: undefined,
    }),
  )}
</Section>
```

который должен вернуть `{"key": "1909", "enabled":false", "time":42}`.



Это работает, но довольно сложно.




### Struct generator




[**Codegen**](pillars-codegen.md) для нативных модулей C++ Turbo поддерживает генераторы структур, поэтому вы можете упростить приведенный выше код в `NativeSampleModule.h` до:

```cpp
using CustomType = NativeSampleModuleBaseCustomType<std::string, bool, std::optional<int32_t>>;
template <>
struct Bridging<CustomType>
    : NativeSampleModuleBaseCustomTypeBridging<std::string, bool, std::optional<int32_t>> {};
```

С помощью `использования CustomType` вы объявляете имя для своей конкретной структуры.



#### Типы членов




С помощью `std::string, bool, std::optional<int32_t>` вы определяете типы свойств членов структуры в том порядке, в котором они были определены в вашей спецификации JavaScript. Порядок имеет значение**. Аргумент _1-го_ шаблона относится к _1-му_ типу данных структуры, и так далее.




Без каких-либо пользовательских функций преобразования:




- вы можете только `bridge` JS string в [std::string](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactCommon/react/bridging/AString.h) и JS boolean в [bool](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactCommon/react/bridging/Bool.h).

- Но вы можете выбирать различные JS `числа` [представления в C++](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactCommon/react/bridging/Number.h).




#### Базовый класс




`NativeSampleModuleBaseCustomType` - это автогенерируемый шаблон в вашем `AppSpecsJSI.h`, имя которого генерируется:




- `NativeSampleModule` (имя нативного модуля C++ Turbo в спецификации JavaScript) +

- `Base` (константа) +

- `CustomType` (имя типа в спецификации JavaScript).




Та же схема именования применяется к необходимой структуре `Bridging`, которая определяется через `struct Bridging<CustomType>`.

