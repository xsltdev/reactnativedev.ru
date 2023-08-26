---
description: Документ объясняет, как можно определить сложную анимацию, используя простую и популярную схему определения анимации - ключевые кадры
---

# Анимация по ключевым кадрам

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Документ объясняет, как можно определить сложную анимацию, используя простую и популярную схему определения анимации - ключевые кадры.

## Как определить анимацию с ключевыми кадрами?

### 1. Импортировать ключевой кадр

```js
import { Keyframe } from 'react-native-reanimated';
```

### 2. Создайте объект Keyframe, определите начальное и конечное состояние

В конструктор Keyframe передайте объект с определениями вашей анимации. Ключи объекта должны находиться в диапазоне `0-100` и соответствовать ходу анимации, поэтому в `0` присвойте объекту стиль, который вы хотите видеть в начале анимации, а в `100` - стиль, который вы хотите видеть в конце анимации.

```js
import { Keyframe } from 'react-native-reanimated';

const keyframe = new Keyframe({
    0: {
        transform: [{ rotate: '0deg' }],
    },
    100: {
        transform: [{ rotate: '45deg' }],
    },
});
```

Вместо того чтобы использовать `0` и `100`, можно задать точки границы с помощью ключевых слов `from` и `to`. Результат будет таким же.

```js
import { Keyframe } from 'react-native-reanimated';

const keyframe = new Keyframe({
    from: {
        transform: [{ rotate: '0deg' }],
    },
    to: {
        transform: [{ rotate: '45deg' }],
    },
});
```

Указание ключевого кадра `0` или `from` является обязательным, поскольку он содержит начальное состояние объекта, который необходимо анимировать. Убедитесь, что вы указали начальное значение для всех свойств стиля, которые вы хотите анимировать в других ключевых кадрах. Помните, что не следует указывать одновременно ключевые кадры `0` и `from`, или `100` и `to`, так как это приведет к конфликту парсинга.

### 3. Добавление средних точек

Между крайними точками можно определить средние точки, в которых объект должен иметь определенные стилевые свойства. Помните, что задавать стиль можно только для тех свойств, для которых задано начальное значение в ключевом кадре `0` или `from`. Если вы хотите анимировать стиль трансформации, убедитесь, что все свойства в массиве трансформации находятся в одном и том же порядке во всех ключевых кадрах.

```js
import { Keyframe } from 'react-native-reanimated';

const keyframe = new Keyframe({
    0: {
        transform: [{ rotate: '0deg' }],
    },
    45: {
        transform: [{ rotate: '100deg' }],
    },
    100: {
        transform: [{ rotate: '45deg' }],
    },
});
```

### 4. Настройка переходов с помощью функции смягчения

Если свойство easing не указано, то по умолчанию используется функция линейного смягчения.

```js
import { Keyframe, Easing } from 'react-native-reanimated';

const keyframe = new Keyframe({
    0: {
        transform: [{ rotate: '0deg' }],
    },
    45: {
        transform: [{ rotate: '100deg' }],
        easing: Easing.exp,
    },
    100: {
        transform: [{ rotate: '45deg' }],
    },
});
```

## Как использовать анимацию с ключевыми кадрами?

В настоящее время анимацию с использованием ключевых кадров можно задать только для анимации входа и выхода.

### 1. Выберите компонент Animated Component, вход или выход которого вы хотите анимировать.

```js
// AnimatedComponent - component created by createAnimatedComponent or imported from Reanimated
// keyframe - Keyframe object
<AnimatedComponent exiting={keyframe} />
```

### 2. Настройка анимации

```js
<AnimatedComponent
    exiting={keyframe.duration(3000).delay(200)}
/>
```

## Имеющиеся модификаторы

Порядок модификаторов не имеет значения.

### продолжительность

по умолчанию: 500
Сколько времени должна длиться анимация.

### задержка

по умолчанию: 0
Позволяет запускать анимацию с заданной задержкой.

### withCallback

Позволяет выполнить код при завершении анимации ключевого кадра.

## Пример

```js
export function KeyframeAnimation(): React.ReactElement {
    const [show, setShow] = useState(false);

    const enteringAnimation = new Keyframe({
        0: {
            originX: 50,
            transform: [{ rotate: '45deg' }],
        },
        30: {
            originX: 10,
            transform: [{ rotate: '-90deg' }],
        },
        100: {
            originX: 0,
            transform: [{ rotate: '0deg' }],
            easing: Easing.quad,
        },
    }).duration(2000);

    const exitingAnimation = new Keyframe({
        0: {
            opacity: 1,
            transform: [{ skewX: '0deg' }],
        },
        30: {
            opacity: 0.5,
            transform: [{ skewX: '40deg' }],
            easing: Easing.exp,
        },
        100: {
            opacity: 0,
            transform: [{ skewX: '-10deg' }],
        },
    }).duration(2000);

    return (
        <View style={{ flexDirection: 'column-reverse' }}>
            <Button
                title="animate"
                onPress={() => {
                    setShow((last) => !last);
                }}
            />
            <View
                style={{
                    height: 400,
                    alignItems: 'center',
                    justifyContent: 'center',
                }}
            >
                {show && (
                    <Animated.View
                        entering={enteringAnimation}
                        exiting={exitingAnimation}
                        style={{
                            height: 100,
                            width: 200,
                            backgroundColor: 'blue',
                            alignItems: 'center',
                            justifyContent: 'center',
                        }}
                    />
                )}
            </View>
        </View>
    );
}
```

<video
  src="https://user-images.githubusercontent.com/48885911/125463255-04502655-3147-4d15-ae5b-f327666eadff.mov"
  controls="controls"
  muted="muted"></video>

## Ссылки

-   [Keyframe animations](https://docs.swmansion.com/react-native-reanimated/docs/layout-animations/keyframe-animations)
