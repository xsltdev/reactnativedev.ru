---
description: Иногда требуется предотвратить уход пользователя с экрана, например, при несохраненных изменениях показать диалог подтверждения
---

# Предотвращение возврата назад

Иногда требуется предотвратить уход пользователя с экрана, например, при несохраненных изменениях показать диалог подтверждения. Для этого можно использовать событие `beforeRemove`.

Слушатель события получает `action`, которое его вызвало. Вы можете повторно отправить это действие после подтверждения или проверить объект действия, чтобы определить, что делать.

Пример:

```js
function EditText({ navigation }) {
    const [text, setText] = React.useState('');
    const hasUnsavedChanges = Boolean(text);

    React.useEffect(
        () =>
            navigation.addListener('beforeRemove', (e) => {
                if (!hasUnsavedChanges) {
                    // If we don't have unsaved changes, then we don't need to do anything
                    return;
                }

                // Prevent default behavior of leaving the screen
                e.preventDefault();

                // Prompt the user before leaving the screen
                Alert.alert(
                    'Discard changes?',
                    'You have unsaved changes. Are you sure to discard them and leave the screen?',
                    [
                        {
                            text: "Don't leave",
                            style: 'cancel',
                            onPress: () => {},
                        },
                        {
                            text: 'Discard',
                            style: 'destructive',
                            // If the user confirmed, then we dispatch the action we blocked earlier
                            // This will continue the action that had triggered the removal of the screen
                            onPress: () =>
                                navigation.dispatch(
                                    e.data.action
                                ),
                        },
                    ]
                );
            }),
        [navigation, hasUnsavedChanges]
    );

    return (
        <TextInput
            value={text}
            placeholder="Type something…"
            onChangeText={setText}
        />
    );
}
```

Обычно это можно было сделать следующим образом:

-   переопределить кнопку "Назад" в заголовке
-   Отключить жест пролистывания назад
-   Переопределить системную кнопку/жест назад в Android.

Однако этот подход имеет много важных отличий, помимо меньшего объема кода:

-   Он не связан с какими-либо конкретными кнопками, переход назад с пользовательских кнопок также будет срабатывать
-   Он не связан с какими-либо конкретными действиями, любое действие, удаляющее маршрут из состояния, будет вызывать его.
-   Работает во вложенных навигаторах, например, если экран удаляется из-за действия в родительском навигаторе.
-   Пользователь все еще может пролистать назад в навигаторе стека, однако пролистывание будет отменено, если событие было предотвращено
-   Можно продолжить то же действие, которое вызвало событие.

## Ограничения

При использовании события `beforeRemove` необходимо учитывать несколько ограничений. Событие **только** срабатывает, когда экран удаляется из-за изменения состояния навигации. Например:

-   Пользователь нажал кнопку "Назад" на экране в стеке.
-   Пользователь выполнил жест пролистывания назад.
-   Было отправлено некоторое действие, такое как `pop` или `reset`, которое удаляет экран из состояния.

Это событие **не** вызывается, когда экран расфокусируется, но не удаляется. Например:

-   Пользователь поместил новый экран поверх экрана со слушателем в стеке.
-   Пользователь перешел с одной вкладки/экрана Drawer на другую вкладку/экран Drawer.

Событие также **не** срабатывает, когда пользователь покидает экран в результате действий, не контролируемых состоянием навигации:

-   Пользователь закрывает приложение (например, нажав кнопку "Назад" на главном экране, закрыв вкладку в браузере, закрыв приложение из переключателя приложений и т.д.). Для обработки некоторых из этих случаев можно дополнительно использовать событие [`hardwareBackPress`](https://reactnative.dev/docs/backhandler) в Android, событие [`beforeunload`](https://developer.mozilla.org/en-US/docs/web/api/window/beforeunload_event) в Web и т.д.
-   Экран размонтируется из-за условного рендеринга или из-за размонтирования родительского компонента.
-   Экран размонтируется из-за использования опции `unmountOnBlur` в [`@react-navigation/bottom-tabs`](bottom-tab-navigator.md), [`@react-navigation/drawer`](drawer-navigator.md) и т.д.

Помимо вышеперечисленных сценариев, данная возможность также не работает с [`@react-navigation/native-stack`](native-stack-navigator.md). Для того чтобы эта функция работала, необходимо:

-   Отключить жест пролистывания для экрана (`gestureEnabled: false`).
-   Переопределить родную кнопку "Назад" в заголовке на пользовательскую кнопку "Назад" (`headerLeft: (props) => <CustomBackButton {...props} />`).

## Ссылки

-   [Preventing going back](https://reactnavigation.org/docs/preventing-going-back)
