---
description: Функция useAnimatedRef позволяет получить ссылку на представление. Используется совместно с функциями measure, scrollTo и useScrollViewOffset
---

# useAnimatedRef

Функция **`useAnimatedRef`** позволяет получить ссылку на представление. Используется совместно с функциями [`measure`](../advanced/measure.md), [`scrollTo`](../scroll/scrollTo.md) и [`useScrollViewOffset`](../scroll/useScrollViewOffset.md).

Объект, определенный с помощью `useAnimatedRef`, должен быть передан в свойство `ref` компонента.

## Описание

```js
import { useAnimatedRef } from 'react-native-reanimated';

function App() {
    const animatedRef = useAnimatedRef();

    return <Animated.View ref={animatedRef} />;
}
```

!!!tip "Типизация"

    ```ts
    function useAnimatedRef<T extends Component>(): AnimatedRef<
    	T
    >;
    ```

### Аргументы

`useAnimatedRef` не принимает никаких аргументов.

### Возвращает

`useAnimatedRef` возвращает объект со свойством `current`, содержащий экземпляр компонента.

## Пример

```js
export default function App() {
    const animatedRef = useAnimatedRef();

    return (
        <View style={styles.container}>
            <Animated.View
                ref={animatedRef}
                style={styles.box}
                onLayout={() => {
                    // Returns a reference to the component
                    const component = animatedRef.current;
                }}
            />
        </View>
    );
}
```

## Замечания

-   С помощью `useAnimatedRef` можно ссылаться не только на анимированные версии компонентов, но и на любой компонент React Native.

-   Значение, хранящееся в свойстве `current`, становится доступным после установки компонента.

    ```js
    function App() {
        const animatedRef = useAnimatedRef();

        console.log(animatedRef.current); // 🚩 Возвращается null

        useEffect(() => {
            console.log(animatedRef.current); // ✅ Возвращает компонент
        }, []);

        return <View ref={animatedRef} />;
    }
    ```

    В качестве альтернативы можно получить значение, хранящееся в `current`, в [обработчиках событий](https://reactdev.ru/learn/responding-to-events/) или в свойстве `onLayout`.

-   Значение, хранящееся в свойстве `current`, недоступно в [UI thread](../fundamentals/glossary.md#ui-thread).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылка

-   [useAnimatedRef](https://docs.swmansion.com/react-native-reanimated/docs/core/useAnimatedRef/)
