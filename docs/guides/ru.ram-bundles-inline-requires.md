

---
id: ram-bundles-inline-requires
title: RAM Bundles and Inline Requires
---

Если у вас большое приложение, возможно, вам стоит рассмотреть формат пакета модулей случайного доступа (RAM) и использование встроенных требований. Это полезно для приложений с большим количеством экранов, которые, возможно, никогда не будут открыты во время обычного использования приложения. В целом это полезно для приложений, содержащих большое количество кода, который не нужен в течение некоторого времени после запуска. Например, приложение включает сложные экраны профиля или менее используемые функции, но в большинстве сеансов используется только главный экран приложения для получения обновлений. Мы можем оптимизировать загрузку пакета, используя формат RAM и требуя эти функции и экраны в режиме inline (когда они действительно используются).



## Загрузка JavaScript




Прежде чем react-native сможет выполнить JS-код, этот код должен быть загружен в память и разобран. При использовании стандартного пакета, если вы загружаете пакет размером 50мб, все 50мб должны быть загружены и разобраны, прежде чем любой из них может быть выполнен. Оптимизация RAM-пакетов заключается в том, что вы можете загрузить только ту часть из 50 мб, которая вам действительно нужна при запуске, и постепенно загружать все большую часть пакета по мере необходимости этих разделов.




## Inline Requires




Inline require откладывает требование модуля или файла до тех пор, пока этот файл действительно не понадобится. Базовый пример выглядит следующим образом:

```tsx title="VeryExpensive.tsx"
import React, {Component} from 'react';
import {Text} from 'react-native';
// ... import some very expensive modules

// You may want to log at the file level to verify when this is happening
console.log('VeryExpensive component loaded');

export default class VeryExpensive extends Component {
  // lots and lots of code
  render() {
    return <Text>Very Expensive Component</Text>;
  }
}
```

```tsx title="Optimized.tsx"
import React, {Component} from 'react';
import {TouchableOpacity, View, Text} from 'react-native';

let VeryExpensive = null;

export default class Optimized extends Component {
  state = {needsExpensive: false};

  didPress = () => {
    if (VeryExpensive == null) {
      VeryExpensive = require('./VeryExpensive').default;
    }

    this.setState(() => ({
      needsExpensive: true,
    }));
  };

  render() {
    return (
      <View style={{marginTop: 20}}>
        <TouchableOpacity onPress={this.didPress}>
          <Text>Load</Text>
        </TouchableOpacity>
        {this.state.needsExpensive ? <VeryExpensive /> : null}
      </View>
    );
  }
}
```

Даже без формата RAM встроенные require могут привести к улучшению времени запуска, поскольку код в VeryExpensive.js будет выполняться только после того, как он впервые потребуется.



## Включить формат RAM




На iOS использование формата RAM создаст единый индексированный файл, который react native будет загружать по одному модулю за раз. На Android по умолчанию будет создаваться набор файлов для каждого модуля. Вы можете заставить Android создавать один файл, как и iOS, но использование нескольких файлов может быть более производительным и требует меньше памяти.




Включите формат RAM в Xcode, отредактировав этап сборки "Bundle React Native code and images". Перед `../node_modules/react-native/scripts/react-native-xcode.sh` добавьте `export BUNDLE_COMMAND="ram-bundle"`:

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

На Android включите формат RAM, отредактировав файл `android/app/build.gradle`. Перед строкой `apply from: "../../node_modules/react-native/react.gradle"` добавьте или измените блок `project.ext.react`:

```
project.ext.react = [
  bundleCommand: "ram-bundle",
]
```

Используйте следующие строки на Android, если вы хотите использовать один индексированный файл:

```
project.ext.react = [
  bundleCommand: "ram-bundle",
  extraPackagerArgs: ["--indexed-ram-bundle"]
]
```

:::info
Если вы используете [Hermes JS Engine](https://github.com/facebook/hermes), вам **не следует** включать функцию RAM bundles. В Hermes, при загрузке байткода, `mmap` гарантирует, что весь файл не будет загружен. Использование Hermes с RAM bundles может привести к проблемам, поскольку эти механизмы несовместимы друг с другом.

:::




## Настройка предварительной загрузки и встроенных требований




Теперь, когда у нас есть RAM-бандл, вызов `require` становится накладным. Теперь `require` должен посылать сообщение через мост, когда он встречает модуль, который еще не загружен. Это больше всего повлияет на запуск, потому что именно в этот момент происходит наибольшее количество вызовов require, пока приложение загружает начальный модуль. К счастью, мы можем настроить часть модулей на предварительную загрузку. Чтобы сделать это, вам нужно будет реализовать некоторую форму inline require.




## Исследование загруженных модулей




В корневой файл (index.(ios|android).js) после начального импорта можно добавить следующее:

```js
const modules = require.getModules();
const moduleIds = Object.keys(modules);
const loadedModuleNames = moduleIds
  .filter(moduleId => modules[moduleId].isInitialized)
  .map(moduleId => modules[moduleId].verboseName);
const waitingModuleNames = moduleIds
  .filter(moduleId => !modules[moduleId].isInitialized)
  .map(moduleId => modules[moduleId].verboseName);

// make sure that the modules you expect to be waiting are actually waiting
console.log(
  'loaded:',
  loadedModuleNames.length,
  'waiting:',
  waitingModuleNames.length,
);

// grab this text blob, and put it in a file named packager/modulePaths.js
console.log(
  `module.exports = ${JSON.stringify(
    loadedModuleNames.sort(),
    null,
    2,
  )};`,
);
```

Когда вы запустите свое приложение, вы можете посмотреть в консоли, сколько модулей было загружено, а сколько ожидает загрузки. Возможно, вы захотите прочитать имена модулей и посмотреть, нет ли сюрпризов. Обратите внимание, что встроенные require вызываются при первом обращении к импорту. Вам может потребоваться исследование и рефакторинг, чтобы гарантировать, что при запуске будут загружаться только нужные вам модули. Обратите внимание, что вы можете изменить объект Systrace на require, чтобы помочь отладить проблемные require.

```js
require.Systrace.beginEvent = message => {
  if (message.includes(problematicModule)) {
    throw new Error();
  }
};
```

Все приложения разные, но, возможно, имеет смысл загружать только те модули, которые нужны для самого первого экрана. Когда вы будете удовлетворены, поместите вывод загруженных имен модулей в файл с именем `packager/modulePaths.js`.



## Обновление metro.config.js




Теперь нам нужно обновить `metro.config.js` в корне проекта, чтобы использовать наш только что созданный файл `modulePaths.js`:

```js
const modulePaths = require('./packager/modulePaths');
const resolve = require('path').resolve;
const fs = require('fs');

// Update the following line if the root folder of your app is somewhere else.
const ROOT_FOLDER = resolve(__dirname, '..');

const config = {
  transformer: {
    getTransformOptions: () => {
      const moduleMap = {};
      modulePaths.forEach(path => {
        if (fs.existsSync(path)) {
          moduleMap[resolve(path)] = true;
        }
      });
      return {
        preloadedModules: moduleMap,
        transform: {inlineRequires: {blockList: moduleMap}},
      };
    },
  },
  projectRoot: ROOT_FOLDER,
};

module.exports = config;
```

Запись `preloadedModules` в конфигурации указывает, какие модули должны быть помечены как предварительно загруженные при сборке пакета RAM. Когда пакет загружается, эти модули загружаются немедленно, еще до выполнения каких-либо требований. Запись `blockList` указывает на то, что эти модули не должны требоваться в строке. Поскольку они загружены заранее, использование встроенного require не дает никакого преимущества в производительности. Фактически, сгенерированный JavaScript тратит дополнительное время на разрешение встроенного require каждый раз, когда ссылается на импорт.



## Тестирование и измерение улучшений




Теперь вы должны быть готовы к созданию приложения с использованием формата RAM и встроенных требований. Убедитесь, что вы измерили время до и после запуска.

