---
description: Это удобный хук, возвращающий ссылку на обработчик события, который может быть использован с прокручиваемыми компонентами React Native
---

# useAnimatedScrollHandler

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

Это удобный хук, возвращающий ссылку на обработчик события, который может быть использован с прокручиваемыми компонентами React Native.

## Аргументы

### `scrollHandlerOrHandlersObject` [объект с рабочими элементами]

Объект, содержащий любой из следующих ключей: `onScroll`, `onBeginDrag`, `onEndDrag`, `onMomentumBegin`, `onMomentumEnd`. Значения в объекте должны быть отдельными заготовками. Каждый из них будет срабатывать при наступлении соответствующего события на подключенном компоненте `Scrollable`.

При вызове каждый из событийных ворклетов будет получать следующие параметры:

-   `event` [object] - объект события, несущий информацию о прокрутке. Полезная нагрузка может отличаться в зависимости от типа события (`onScroll`, `onBeginDrag` и т. д.). Для ознакомления со структурой событий прокрутки обратитесь к [React Native's ScrollView documentation](https://reactnative.dev/docs/scrollview).
-   `context` [object] - обычный JS-объект, который может быть использован для хранения некоторого состояния. Этот объект будет сохраняться между событиями прокрутки, и вы можете читать и записывать в него любые данные. При наличии нескольких обработчиков событий, представленных в виде объекта воркетов, объект `context` будет общим для всех воркетов, что позволит им взаимодействовать друг с другом.

### `dependencies` [Array]

Необязательный массив значений, изменение которых приводит к тому, что данный хук будет получать обновленные значения при рендеринге оборачивающего компонента. Это важно, когда, например, воркет использует значения, зависящие от состояния компонента.

Пример:

```js
const App = () => {
    const [state, setState] = useState(0);
    const sv = useSharedValue(0);

    const handler = useAnimatedScrollHandler(
        {
            onEndDrag: (e) => {
                sv.value = state;
            },
        },
        dependencies
    );
    //...
    return <></>;
};
```

Здесь `dependencies` могут быть:

-   `undefined` (аргумент пропущен) - заготовка будет перестроена при изменении тела любого из callback'ов или значений из их закрытия (переменных из внешней области видимости, используемых в заготовке),
-   пустой массив(`[]`) - заготовка будет перестроена только при изменении тела любого из callback'ов,
-   массив значений(`[val1, val2, ..., valN]`) - заготовка будет перестроена при изменении любого из тел обратных вызовов или любых значений из данного массива.

## Возвращает

Хук возвращает объект обработчика, который может быть подключен к контейнеру с прокруткой. Обратите внимание, что для корректного срабатывания обработчика следует использовать контейнеры, обернутые в `Animated` (например, `Animated.ScrollView`, а не просто `ScrollView`). Обработчик должен быть передан в параметре `onScroll` независимо от того, настроен ли он на получение только событий прокрутки или также событий движения или перетаскивания.

## Пример

В приведенном ниже примере мы определяем обработчик прокрутки, передавая один обработчик worklet. Обработчик запускается для каждого события прокрутки, переданного компоненту `Animated.ScrollView`, к которому мы прикрепили обработчик.

```js
import Animated, {
    useSharedValue,
    useAnimatedStyle,
    useAnimatedScrollHandler,
} from 'react-native-reanimated';

function ScrollExample() {
    const translationY = useSharedValue(0);

    const scrollHandler = useAnimatedScrollHandler(
        (event) => {
            translationY.value = event.contentOffset.y;
        }
    );

    const stylez = useAnimatedStyle(() => {
        return {
            transform: [
                {
                    translateY: translationY.value,
                },
            ],
        };
    });

    return (
        <View style={styles.container}>
            <Animated.View style={[styles.box, stylez]} />
            <Animated.ScrollView
                style={styles.scroll}
                onScroll={scrollHandler}
            >
                <Content />
            </Animated.ScrollView>
        </View>
    );
}
```

Если нас интересует получение событий перетаскивания или импульса, то вместо передачи одного объекта worklet можно передать объект worklet'ов. Ниже для удобства мы покажем только, как должен быть определен `scrollHandler` в таком случае. Место, где мы прикрепляем обработчик к прокручиваемому компоненту, остается неизменным независимо от типов событий, которые мы хотим получать:

```js
const isScrolling = useSharedValue(false);

const scrollHandler = useAnimatedScrollHandler({
    onScroll: (event) => {
        translationY.value = event.contentOffset.y;
    },
    onBeginDrag: (e) => {
        isScrolling.value = true;
    },
    onEndDrag: (e) => {
        isScrolling.value = false;
    },
});
```

## Ссылки

-   [useAnimatedScrollHandler](https://docs.swmansion.com/react-native-reanimated/docs/scroll/useAnimatedScrollHandler)
