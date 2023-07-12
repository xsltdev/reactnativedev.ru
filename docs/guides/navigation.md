# Навигация между экранами

Мобильные приложения редко состоят из одного экрана. Управление представлением и переходом между несколькими экранами обычно осуществляется с помощью так называемого навигатора.

В этом руководстве рассматриваются различные компоненты навигации, доступные в React Native. Если вы только начинаете работать с навигацией, то, скорее всего, захотите использовать [React Navigation](navigation.md#react-navigation). React Navigation предоставляет простое навигационное решение с возможностью представления общих шаблонов стековой навигации и навигации с вкладками как на Android, так и на iOS.

Если вы интегрируете React Native в приложение, которое уже управляет навигацией нативно, или ищете альтернативу React Navigation, следующая библиотека обеспечивает нативную навигацию на обеих платформах: [react-native-navigation](https://github.com/wix/react-native-navigation).

## React Navigation

Решение сообщества для навигации — это отдельная библиотека, которая позволяет разработчикам настраивать экраны приложения с помощью нескольких строк кода.

### Установка и настройка

Сначала необходимо установить их в свой проект:

```shell
npm install @react-navigation/native @react-navigation/native-stack
```

Затем установите необходимые зависимости от сверстников. Вам нужно выполнить разные команды в зависимости от того, является ли ваш проект Expo managed проектом или "голым" проектом React Native.

-   Если у вас есть проект под управлением Expo, установите зависимости с помощью `expo`:

    ```shell
    npx expo install react-native-screens react-native-safe-area-context
    ```

-   Если у вас "голый" проект React Native, установите зависимости с помощью `npm`:

    ```shell
    npm install react-native-screens react-native-safe-area-context
    ```

    Для iOS с голым проектом React Native убедитесь, что у вас установлен [Cocoapods](https://cocoapods.org/). Затем установите капсулы для завершения установки:

    ```shell
    cd ios
    pod install
    cd ..
    ```

!!!note "Примечание"

    После установки вы можете получить предупреждения, связанные с одноранговыми зависимостями. Обычно они вызваны неправильными диапазонами версий, указанными в некоторых пакетах. Вы можете спокойно игнорировать большинство предупреждений, пока ваше приложение собирается.

Теперь вам нужно обернуть все приложение в `NavigationContainer`. Обычно это делается в начальном файле, таком как `index.js` или `App.js`:

```ts
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';

const App = () => {
    return (
        <NavigationContainer>
            {/* Rest of your app code */}
        </NavigationContainer>
    );
};

export default App;
```

Теперь вы готовы создать и запустить свое приложение на устройстве/симуляторе.

### Использование

Теперь вы можете создать приложение с главным экраном и экраном профиля:

```ts
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

const MyStack = () => {
    return (
        <NavigationContainer>
            <Stack.Navigator>
                <Stack.Screen
                    name="Home"
                    component={HomeScreen}
                    options={{ title: 'Welcome' }}
                />
                <Stack.Screen
                    name="Profile"
                    component={ProfileScreen}
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
};
```

В этом примере есть 2 экрана (`Home` и `Profile`), определенные с помощью компонента `Stack.Screen`. Аналогично, вы можете определить столько экранов, сколько захотите.

Вы можете установить такие параметры, как заголовок экрана для каждого экрана, в свойстве `options` файла `Stack.Screen`.

Каждый экран принимает реквизит `component`, который является компонентом React. Эти компоненты получают свойство `navigation`, которое имеет различные методы для связи с другими экранами. Например, вы можете использовать `navigation.navigate` для перехода к экрану `Profile`:

```ts
const HomeScreen = ({ navigation }) => {
    return (
        <Button
            title="Go to Jane's profile"
            onPress={() =>
                navigation.navigate('Profile', {
                    name: 'Jane',
                })
            }
        />
    );
};
const ProfileScreen = ({ navigation, route }) => {
    return (
        <Text>This is {route.params.name}'s profile</Text>
    );
};
```

Этот навигатор `native-stack` использует нативные API: `UINavigationController` на iOS и `Fragment` на Android, так что навигация, созданная с помощью `createNativeStackNavigator`, будет вести себя так же и иметь те же характеристики производительности, что и приложения, созданные на базе этих API.

В React Navigation также есть пакеты для различных видов навигаторов, таких как вкладки и ящики. Вы можете использовать их для реализации различных шаблонов в вашем приложении.

Для полного ознакомления с React Navigation следуйте руководству [React Navigation Getting Started Guide](https://reactnavigation.org/docs/getting-started).
