---
description: С помощью хука useAnimatedKeyboard можно создавать анимацию, основанную на текущем положении клавиатуры
---

# useAnimatedKeyboard

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

!!!warning ""

    Реализация `useAnimatedKeyboard` в Android является экспериментальной.

С помощью хука `useAnimatedKeyboard` можно создавать анимацию, основанную на текущем положении клавиатуры.

В Android для параметра `android:windowSoftInputMode` в файле `AndroidManifest.xml` следует установить значение `adjustResize`. Затем с помощью хука `useAnimatedKeyboard` отключается стандартное поведение Android (изменение размера представления под клавиатуру) во всем приложении. Используя значения хука `useAnimatedKeyboard`, вы можете самостоятельно управлять клавиатурой. Размонтирование всех компонентов, использующих хук `useAnimatedKeyboard`, возвращает стандартное поведение Android.

## Аргументы

### `options` `[AnimatedKeyboardOptions]`

Необязательный объект, содержащий дополнительную конфигурацию.

## Возвращает

Хук `useAnimatedKeyboard` возвращает экземпляр `[AnimatedKeyboardInfo]`;

## Types

### `AnimatedKeyboardInfo: [object]`

Свойства:

-   `height`: [[SharedValue](../core/useSharedValue.md)] содержит `[number]` содержит текущую высоту клавиатуры
-   `state`: [[SharedValue](../core/useSharedValue.md)] содержит `[enum]` содержит текущее состояние клавиатуры. Возможные состояния: `{ CLOSED, OPEN, CLOSING, OPENING }`.

### `AnimatedKeyboardOptions: [object]`

Свойства:

-   `isStatusBarTranslucentAndroid` [bool] - если вы хотите использовать полупрозрачную строку состояния на Android, установите этот параметр в `true`. По умолчанию установлено значение `false`. Игнорируется на iOS.

## Пример

```js
function AnimatedKeyboardExample() {
    const keyboard = useAnimatedKeyboard();
    const translateStyle = useAnimatedStyle(() => {
        return {
            transform: [
                { translateY: -keyboard.height.value },
            ],
        };
    });

    return (
        <ScrollView
            contentContainerStyle={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Animated.View style={translateStyle}>
                <TextInput />
            </Animated.View>
        </ScrollView>
    );
}
```

## Ссылки

-   [useAnimatedKeyboard](https://docs.swmansion.com/react-native-reanimated/docs/device/useAnimatedKeyboard)
