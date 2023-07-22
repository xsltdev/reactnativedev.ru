---
description: В большинстве приложений для получения доступа к данным, связанным с пользователем, или другому приватному контенту требуется аутентификация пользователя
---

# Потоки аутентификации

В большинстве приложений для получения доступа к данным, связанным с пользователем, или другому приватному контенту требуется аутентификация пользователя. Как правило, процесс выглядит следующим образом:

-   Пользователь открывает приложение.
-   Приложение загружает некоторое состояние аутентификации из зашифрованного постоянного хранилища (например, [`SecureStore`](https://docs.expo.io/versions/latest/sdk/securestore/)).
-   После загрузки состояния пользователю открываются либо экраны аутентификации, либо основное приложение, в зависимости от того, было ли загружено действительное состояние аутентификации.
-   Когда пользователь выходит из системы, мы очищаем состояние аутентификации и возвращаем его к экранам аутентификации.

!!!note "Примечание"

    Мы говорим "экраны аутентификации", потому что обычно их несколько. У вас может быть основной экран с полем для ввода имени пользователя и пароля, другой экран для "забыл пароль" и еще один экран для регистрации.

## Что нам нужно

Мы хотим, чтобы поток аутентификации вел себя следующим образом: при входе пользователя в систему мы должны отбрасывать состояние потока аутентификации и размонтировать все экраны, связанные с аутентификацией, а при нажатии аппаратной кнопки "назад" мы не должны иметь возможности вернуться к потоку аутентификации.

## Как это будет работать

Мы можем определить различные экраны на основе некоторого условия. Например, если пользователь вошел в систему, мы можем определить `Home`, `Profile`, `Settings` и т.д. Если пользователь не вошел в систему, мы можем определить экраны `SignIn` и `SignUp`.

Например:

```js
isSignedIn ? (
    <>
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen
            name="Profile"
            component={ProfileScreen}
        />
        <Stack.Screen
            name="Settings"
            component={SettingsScreen}
        />
    </>
) : (
    <>
        <Stack.Screen
            name="SignIn"
            component={SignInScreen}
        />
        <Stack.Screen
            name="SignUp"
            component={SignUpScreen}
        />
    </>
);
```

При таком определении экранов, когда `isSignedIn` имеет значение `true`, React Navigation будет видеть только экраны `Home`, `Profile` и `Settings`, а когда `false`, React Navigation будет видеть экраны `SignIn` и `SignUp`. Это делает невозможным переход к экранам `Home`, `Profile` и `Settings`, когда пользователь не вошел в систему, и к экранам `SignIn` и `SignUp`, когда пользователь вошел в систему.

Этот паттерн уже давно используется в других библиотеках маршрутизации, таких как React Router, и известен как "защищенные маршруты". В данном случае экраны, требующие входа пользователя в систему, "защищены" и не могут быть открыты другими способами, если пользователь не вошел в систему.

Волшебство происходит при изменении значения переменной `isSignedIn`. Допустим, изначально `isSignedIn` имеет значение `false`. Это означает, что на экране отображается либо `SignIn`, либо `SignUp`. После того как пользователь зарегистрируется, значение `isSignedIn` изменится на `true`. React Navigation увидит, что экраны `SignIn` и `SignUp` больше не определены, и удалит их. Затем он автоматически покажет экран `Home`, поскольку это первый экран, определенный, когда `isSignedIn` имеет значение `true`.

В примере показан стековый навигатор, но вы можете использовать тот же подход с любым навигатором.

Условно определяя различные экраны на основе переменной, мы можем реализовать поток авторизации простым способом, не требующим дополнительной логики для обеспечения правильного отображения экрана.

## При условной визуализации экранов не нужно осуществлять ручную навигацию

Важно отметить, что при такой настройке **не нужно вручную переходить** к экрану `Home`, вызывая `navigation.navigate('Home')` или любой другой метод. **React Navigation будет автоматически переходить на нужный экран** при изменении `isSignedIn` - на экран `Home`, когда `isSignedIn` становится `true`, и на экран `SignIn`, когда `isSignedIn` становится `false`. При попытке навигации вручную вы получите ошибку.

## Определяем экраны

В нашем навигаторе мы можем условно определить соответствующие экраны. В нашем случае, допустим, у нас есть 3 экрана:

-   `SplashScreen` - будет показывать заставку или экран загрузки, когда мы восстанавливаем токен.
-   `SignInScreen` - этот экран мы показываем, если пользователь еще не вошел в систему (мы не смогли найти токен).
-   `HomeScreen` - это экран, который мы показываем, если пользователь уже вошел в систему.

Таким образом, наш навигатор будет выглядеть следующим образом:

```js
if (state.isLoading) {
    // We haven't finished checking for the token yet
    return <SplashScreen />;
}

return (
    <Stack.Navigator>
        {state.userToken == null ? (
            // No token found, user isn't signed in
            <Stack.Screen
                name="SignIn"
                component={SignInScreen}
                options={{
                    title: 'Sign in',
                    // When logging out, a pop animation feels intuitive
                    // You can remove this if you want
                    // the default 'push' animation
                    animationTypeForReplace: state.isSignout
                        ? 'pop'
                        : 'push',
                }}
            />
        ) : (
            // User is signed in
            <Stack.Screen
                name="Home"
                component={HomeScreen}
            />
        )}
    </Stack.Navigator>
);
```

В приведенном выше фрагменте `isLoading` означает, что мы все еще проверяем, есть ли у нас токен. Обычно это можно сделать, проверив наличие токена в `SecureStore` и подтвердив его. После того как мы получили токен и убедились, что он действителен, нам нужно установить `userToken`. У нас также есть еще одно состояние `isSignout`, чтобы иметь другую анимацию при выходе из системы.

Главное, что следует отметить, - это то, что мы условно определяем экраны на основе этих переменных состояния:

-   экран `SignIn` определяется только в том случае, если `userToken` равен `null` (пользователь не вошел в систему)
-   Экран `Home` определяется только в том случае, если `userToken` не равен `null` (пользователь вошел в систему).

Здесь мы условно определяем один экран для каждого случая. Но можно определить и несколько экранов. Например, вы, вероятно, захотите определить экраны сброса пароля, регистрации и т.д., когда пользователь не вошел в систему. Аналогично, для экранов, доступных после входа в систему, вероятно, потребуется несколько экранов. Мы можем использовать `React.Fragment` для определения нескольких экранов:

```js
state.userToken == null ? (
    <>
        <Stack.Screen
            name="SignIn"
            component={SignInScreen}
        />
        <Stack.Screen
            name="SignUp"
            component={SignUpScreen}
        />
        <Stack.Screen
            name="ResetPassword"
            component={ResetPassword}
        />
    </>
) : (
    <>
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen
            name="Profile"
            component={ProfileScreen}
        />
    </>
);
```

!!!note ""

    Если экраны, связанные с входом в систему, и остальные экраны находятся в двух разных навигаторах Stack, мы рекомендуем использовать один навигатор Stack и поместить в него условие, а не использовать два разных навигатора. Это позволит получить корректную анимацию перехода при входе/выходе.

## Реализуем логику восстановления токена

!!!note "Примечание"

    Ниже приведен пример того, как можно реализовать логику аутентификации в вашем приложении. Не обязательно следовать ему в точности.

Из предыдущего фрагмента видно, что нам нужны 3 переменные состояния:

-   `isLoading` - мы устанавливаем это значение в `true`, когда пытаемся проверить, сохранен ли уже токен в `SecureStore`.
-   `isSignout` - Мы устанавливаем значение `true`, когда пользователь выходит из системы, в противном случае устанавливаем значение `false`.
-   `userToken` - Токен пользователя. Если он не равен `null`, мы считаем, что пользователь вошел в систему, в противном случае - нет.

Таким образом, нам необходимо:

-   Добавить логику для восстановления токена, входа и выхода из системы
-   Предоставить методы для входа и выхода другим компонентам.

В данном руководстве мы будем использовать `React.useReducer` и `React.useContext`. Но если вы используете библиотеку управления состоянием, такую как Redux или Mobx, вы можете использовать их для этой функциональности. На самом деле, в больших приложениях для хранения токенов аутентификации больше подходит глобальная библиотека управления состоянием. Вы можете применить тот же подход к своей библиотеке управления состояниями.

Сначала нам нужно будет создать контекст для `auth`, в котором мы сможем открыть необходимые методы:

```js
import * as React from 'react';

const AuthContext = React.createContext();
```

Таким образом, наш компонент будет выглядеть следующим образом:

```js
import * as React from 'react';
import * as SecureStore from 'expo-secure-store';

export default function App({ navigation }) {
    const [state, dispatch] = React.useReducer(
        (prevState, action) => {
            switch (action.type) {
                case 'RESTORE_TOKEN':
                    return {
                        ...prevState,
                        userToken: action.token,
                        isLoading: false,
                    };
                case 'SIGN_IN':
                    return {
                        ...prevState,
                        isSignout: false,
                        userToken: action.token,
                    };
                case 'SIGN_OUT':
                    return {
                        ...prevState,
                        isSignout: true,
                        userToken: null,
                    };
            }
        },
        {
            isLoading: true,
            isSignout: false,
            userToken: null,
        }
    );

    React.useEffect(() => {
        // Fetch the token from storage then navigate
        // to our appropriate place
        const bootstrapAsync = async () => {
            let userToken;

            try {
                userToken = await SecureStore.getItemAsync(
                    'userToken'
                );
            } catch (e) {
                // Restoring token failed
            }

            // After restoring token, we may need to validate it
            // in production apps

            // This will switch to the App screen or Auth screen
            // and this loading
            // screen will be unmounted and thrown away.
            dispatch({
                type: 'RESTORE_TOKEN',
                token: userToken,
            });
        };

        bootstrapAsync();
    }, []);

    const authContext = React.useMemo(
        () => ({
            signIn: async (data) => {
                // In a production app, we need to send some data
                // (usually username, password) to server
                // and get a token
                // We will also need to handle errors if sign in failed
                // After getting token, we need to persist the token
                // using `SecureStore`
                // In the example, we'll use a dummy token

                dispatch({
                    type: 'SIGN_IN',
                    token: 'dummy-auth-token',
                });
            },
            signOut: () => dispatch({ type: 'SIGN_OUT' }),
            signUp: async (data) => {
                // In a production app, we need to send user data
                //  to server and get a token
                // We will also need to handle errors if sign up failed
                // After getting token, we need to persist
                // the token using `SecureStore`
                // In the example, we'll use a dummy token

                dispatch({
                    type: 'SIGN_IN',
                    token: 'dummy-auth-token',
                });
            },
        }),
        []
    );

    return (
        <AuthContext.Provider value={authContext}>
            <Stack.Navigator>
                {state.userToken == null ? (
                    <Stack.Screen
                        name="SignIn"
                        component={SignInScreen}
                    />
                ) : (
                    <Stack.Screen
                        name="Home"
                        component={HomeScreen}
                    />
                )}
            </Stack.Navigator>
        </AuthContext.Provider>
    );
}
```

## Заполнение других компонентов

Мы не будем говорить о том, как реализовать текстовые вводы и кнопки для экрана аутентификации, это выходит за рамки навигации. Мы просто заполним их содержимым.

```js
function SignInScreen() {
    const [username, setUsername] = React.useState('');
    const [password, setPassword] = React.useState('');

    const { signIn } = React.useContext(AuthContext);

    return (
        <View>
            <TextInput
                placeholder="Username"
                value={username}
                onChangeText={setUsername}
            />
            <TextInput
                placeholder="Password"
                value={password}
                onChangeText={setPassword}
                secureTextEntry
            />
            <Button
                title="Sign in"
                onPress={() =>
                    signIn({ username, password })
                }
            />
        </View>
    );
}
```

## Удаление общих экранов при изменении состояния авторизации

Рассмотрим следующий пример:

```js
isSignedIn ? (
    <>
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen
            name="Profile"
            component={ProfileScreen}
        />
        <Stack.Screen name="Help" component={HelpScreen} />
    </>
) : (
    <>
        <Stack.Screen
            name="SignIn"
            component={SignInScreen}
        />
        <Stack.Screen
            name="SignUp"
            component={SignUpScreen}
        />
        <Stack.Screen name="Help" component={HelpScreen} />
    </>
);
```

Здесь у нас есть специальные экраны, такие как `SignIn`, `Home` и т.д., которые показываются только в зависимости от состояния входа в систему. Но у нас также есть экран `Help`, который может быть показан в обоих случаях. Это также означает, что если состояние входа изменится, когда пользователь находится на экране `Help`, то он останется на экране `Help`.

Это может быть проблемой, поскольку мы, вероятно, хотим, чтобы пользователь переходил на экран `SignIn` или `Home`, а не оставался на экране `Help`. Для того чтобы это работало, мы можем использовать свойство [`navigationKey`](screen.md#navigationkey). Когда `navigationKey` изменится, React Navigation удалит все экраны.

Таким образом, наш обновленный код будет выглядеть следующим образом:

```js
<>
    {isSignedIn ? (
        <>
            <Stack.Screen
                name="Home"
                component={HomeScreen}
            />
            <Stack.Screen
                name="Profile"
                component={ProfileScreen}
            />
        </>
    ) : (
        <>
            <Stack.Screen
                name="SignIn"
                component={SignInScreen}
            />
            <Stack.Screen
                name="SignUp"
                component={SignUpScreen}
            />
        </>
    )}
    <Stack.Screen
        navigationKey={isSignedIn ? 'user' : 'guest'}
        name="Help"
        component={HelpScreen}
    />
</>
```

Если у вас есть несколько общих экранов, вы также можете использовать [`navigationKey` с `Group`](group.md#navigationkey) для удаления всех экранов в группе. Например:

```js
<>
    {isSignedIn ? (
        <>
            <Stack.Screen
                name="Home"
                component={HomeScreen}
            />
            <Stack.Screen
                name="Profile"
                component={ProfileScreen}
            />
        </>
    ) : (
        <>
            <Stack.Screen
                name="SignIn"
                component={SignInScreen}
            />
            <Stack.Screen
                name="SignUp"
                component={SignUpScreen}
            />
        </>
    )}
    <Stack.Group
        navigationKey={isSignedIn ? 'user' : 'guest'}
    >
        <Stack.Screen name="Help" component={HelpScreen} />
        <Stack.Screen
            name="About"
            component={AboutScreen}
        />
    </Stack.Group>
</>
```

## Ссылки

-   [Authentication flows](https://reactnavigation.org/docs/auth-flow)
