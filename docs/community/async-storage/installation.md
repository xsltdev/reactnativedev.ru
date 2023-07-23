---
description: Установка Async Storage в проект React Native
---

# Установка

=== "npm"

    ```bash
    npm install @react-native-async-storage/async-storage
    ```

=== "Yarn"

    ```bash
    yarn add @react-native-async-storage/async-storage
    ```

=== "Expo CLI"

    ```bash
    npx expo install @react-native-async-storage/async-storage
    ```

## Подключение

### Android & iOS

Требуется **React Native 0.60+**.

[CLI autolink feature](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) связывает модуль во время сборки приложения.

На iOS для добавления нативного `RNAsyncStorage` в проект используйте CocoaPods:

```bash
npx pod-install
```

### Windows

Требуется **React Native Windows 0.63+**.

[CLI autolink feature](https://microsoft.github.io/react-native-windows/docs/native-modules-autolinking) связывает модуль во время сборки приложения.

### macOS

Требуется **React Native macOS 0.63+**.

1.  Установите `platform :macos, '10.14'` в `macos/Podfile`.
2.  Установите pods
3.  С этого момента [CLI autolink feature](https://microsoft.github.io/react-native-windows/docs/native-modules-autolinking) будет связывать модуль при сборке приложения.
