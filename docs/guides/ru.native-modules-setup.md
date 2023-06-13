

---
id: native-modules-setup
title: Native Modules NPM Package Setup
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

Нативные модули обычно распространяются как пакеты npm, за исключением того, что поверх обычного JavaScript они будут включать некоторый нативный код для каждой платформы. Чтобы узнать больше о пакетах npm, вам может быть полезно [это руководство](https://docs.npmjs.com/packages-and-modules/contributing-packages-to-the-registry).



Для создания базовой структуры проекта для нативного модуля мы воспользуемся инструментом сообщества под названием [create-react-native-library](https://github.com/callstack/react-native-builder-bob). Вы можете пойти дальше и углубиться в работу этой библиотеки, но для наших нужд мы выполним только базовый сценарий:

```shell
npx create-react-native-library@latest react-native-awesome-module
```

Где `react-native-awesome-module` - имя, которое вы хотите дать новому модулю. После этого перейдите в папку `react-native-awesome-module` и загрузите проект примера, выполнив:

```shell
yarn
```

Когда загрузка будет завершена, вы сможете запустить пример приложения, выполнив одну из следующих команд:

```shell
# Android app
yarn example android
# iOS app
yarn example ios
```

Когда все шаги будут выполнены, вы сможете продолжить работу с руководствами [Android Native Modules](native-modules-android) или [iOS Native Modules](native-modules-ios) для добавления кода.



> Для менее категоричной настройки посмотрите на сторонний инструмент [create-react-native-module](https://github.com/brodybits/create-react-native-module).

