---
description: Использовать Redux в приложении с React Navigation очень просто
---

# Интеграция с Redux

Использовать Redux в приложении с React Navigation очень просто. Оно практически ничем не отличается от приложения без React Navigation.

```js
import { Provider } from 'react-redux';
import { NavigationContainer } from '@react-navigation/native';

// Render the app container component with the provider around it
export default function App() {
    return (
        <Provider store={store}>
            <NavigationContainer>
                {/* Screen configuration */}
            </NavigationContainer>
        </Provider>
    );
}
```

Обратите внимание, что мы обернули наши компоненты в `Provider`, как это обычно делается с `react-redux`. Та-да! Теперь можно смело использовать `connect` во всем приложении.

## Использование компонента, который `connect` подключен в `options`.

Создайте компонент, `connect` его к хранилищу, а затем используйте этот компонент в `title`.

```js
function Counter({ value }) {
    return <Text>Count: {value}</Text>;
}

const CounterContainer = connect((state) => ({
    value: state.count,
}))(Counter);
```

```js
<Stack.Screen
    name="Test"
    component={TestScreen}
    options={{ title: () => <CounterContainer /> }}
/>
```

### Передайте экрану интересующее вас состояние в качестве параметра

Если не предполагается, что значение будет меняться, можно просто передать его от подключенного компонента другому экрану в качестве параметра.

```js
<Button
    title="Go to static counter screen"
    onPress={() =>
        props.navigation.navigate('StaticCounter', {
            count,
        })
    }
/>
```

```js
function StaticCounter({ route }) {
    return (
        <View style={styles.container}>
            <Text style={styles.paragraph}>
                {route.params.count}
            </Text>
        </View>
    );
}
```

Таким образом, наш компонент будет выглядеть следующим образом:

```js
<RootStack.Screen
    name="StaticCounter"
    component={StaticCounter}
    options={({ route }) => ({ title: route.params.count })}
/>
```

## Можно ли хранить состояние навигации и в Redux?

Это невозможно. Мы не поддерживаем эту возможность, потому что слишком легко выстрелить себе в ногу и замедлить / сломать приложение.

Однако можно использовать [`redux-devtools-extension`](https://github.com/reduxjs/redux-devtools) для просмотра состояния [навигации](navigation-state.md) и действий, а также для отладки путешествий во времени с помощью пакета [`devtools`](devtools.md).

## Ссылки

-   [Redux integration](https://reactnavigation.org/docs/redux-integration/)
