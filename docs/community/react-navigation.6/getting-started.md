# Начало работы

Далее в разделе _Фундаментальные основы_ данной документации представлен обзор наиболее важных аспектов React Navigation. Этого будет достаточно, чтобы понять, как построить типичное небольшое мобильное приложение, и дать вам основу, необходимую для более глубокого погружения в более сложные части React Navigation.

## Предварительные условия

Если вы уже знакомы с JavaScript, React и React Native, то вы сможете быстро освоить React Navigation! Если же нет, то мы настоятельно рекомендуем сначала получить базовые знания, а затем вернуться сюда, когда все будет готово.

Вот некоторые ресурсы, которые могут вам помочь:

1.  [React Native Express](https://www.reactnative.express) (разделы с 1 по 4)

2.  [Основные концепции React](https://reactdev.ru/learn)

3.  [React Hooks](https://reactdev.ru/reference/hooks/)

4.  [React Context](https://reactdev.ru/learn/passing-data-deeply-with-context) (Дополнительно)

## Минимальные требования

-   `react-native` >= 0.63.0

-   `expo` >= 41 (если вы используете [Expo](https://expo.io))

-   `typescript` >= 4.1.0 (если вы используете [TypeScript](https://www.typescriptlang.org))

## Установка

Установите необходимые пакеты в ваш проект React Native:

```bash
npm install @react-navigation/native
```

React Navigation состоит из некоторых основных утилит, которые затем используются навигаторами для создания навигационной структуры в вашем приложении. Не стоит слишком беспокоиться об этом, скоро все станет ясно! Чтобы облегчить работу по установке, давайте также установим и настроим зависимости, используемые большинством навигаторов, после чего можно будет приступать к написанию кода.

Сейчас мы установим библиотеки [`react-native-screens`](https://github.com/software-mansion/react-native-screens) и [`react-native-safe-area-context`](https://github.com/th3rdwave/react-native-safe-area-context). Если эти библиотеки у вас уже установлены и имеют последнюю версию, то вы закончили! В противном случае читайте дальше.

### Установка зависимостей в проект, управляемый Expo

В каталоге проекта выполните команду:

```bash
npx expo install react-native-screens react-native-safe-area-context
```

В результате будут установлены совместимые версии этих библиотек.

Теперь вы можете перейти к ["Hello React Navigation"](hello-react-navigation.md), чтобы начать писать код.

### Установка зависимостей в пустой проект React Native

В каталоге проекта выполните команду:

```bash
npm install react-native-screens react-native-safe-area-context
```

!!!note ""

    После установки могут появляться предупреждения, связанные с одноранговыми зависимостями. Обычно они вызваны неправильным указанием диапазона версий в некоторых пакетах. Большинство предупреждений можно игнорировать, пока приложение работает.

Начиная с версии React Native 0.60 и выше, [связывание происходит автоматически](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md). Поэтому **не нужно запускать** `react-native link`.

Если вы работаете на Mac и разрабатываете под iOS, то для завершения линковки вам необходимо установить капсулы (через [Cocoapods](https://cocoapods.org/)).

```bash
npx pod-install ios
```

Для корректной работы пакета `react-native-screens` на устройствах под управлением ОС Android требуется выполнить еще один шаг настройки. Отредактируйте файл `MainActivity.java`, расположенный в каталоге `android/app/src/main/java/<имя вашего пакета>/MainActivity.java`.

Добавьте выделенный код в тело класса `MainActivity`:

```java
public class MainActivity extends ReactActivity {
  // ...
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(null);
  }
  // ...
}
```

и обязательно добавьте следующий оператор import в верхней части этого файла под оператором package:

```java
import android.os.Bundle;
```

Это изменение необходимо для предотвращения сбоев, связанных с тем, что состояние представления не сохраняется последовательно при перезапуске Activity.

!!!note ""

    При использовании навигатора (например, stack navigator) необходимо следовать инструкциям по установке дополнительных зависимостей от этого навигатора. Если вы получаете ошибку "Unable to resolve module", то необходимо установить этот модуль в проект.

## Обертывание приложения в `NavigationContainer`

Теперь необходимо обернуть все приложение в `NavigationContainer`. Обычно это делается во входном файле, таком как `index.js` или `App.js`:

```js
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';

export default function App() {
    return (
        <NavigationContainer>
            {/* Rest of your app code */}
        </NavigationContainer>
    );
}
```

!!!note ""

    В типичном приложении React Native `NavigationContainer` должен использоваться только один раз в корне приложения. Не следует создавать гнезда из нескольких `NavigationContainer`, если только у вас нет конкретного случая их использования.

Теперь вы готовы к сборке и запуску приложения на устройстве/симуляторе.

Перейдите к ["Hello React Navigation"](hello-react-navigation.md), чтобы начать писать код.

## Ссылки

-   [https://reactnavigation.org/docs/getting-started](https://reactnavigation.org/docs/getting-started)
