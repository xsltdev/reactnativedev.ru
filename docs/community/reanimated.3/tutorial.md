---
description: Этот урок поможет вам сделать экраны и компоненты более плавными за счет добавления анимации. Мы будем использовать новую возможность библиотеки Reanimated - Layout Animations
---

# Как легко анимировать экраны и компоненты

Этот урок поможет вам сделать экраны и компоненты более плавными за счет добавления анимации. Мы будем использовать новую возможность библиотеки Reanimated - Layout Animations, которая позволяет нам анимировать:

-   появление компонентов
-   исчезновение компонентов
-   изменения макета

используя предопределенные анимации. Существует множество предопределенных анимаций, которые можно использовать, однако, если вам необходимо создать более индивидуальную анимацию, вы можете легко реализовать свою собственную.

Чтобы лучше понять, как использовать эти анимации, мы приукрасим уже существующий экран - список участников, добавив к его компонентам анимацию расположения. Здесь вы можете увидеть эффект от изменений, которые мы сделаем в процессе работы над этим уроком.

До:

![До](tutorial-before.gif){:style="max-height: 500px"}

После:

![После](tutorial-after.gif){:style="max-height: 500px"}

## Представление экрана

Здесь мы имеем уже существующий компонент списка `Participant` (полный код для самостоятельного редактирования можно найти [здесь](https://gist.github.com/jmysliv/87b15453aab173a63a4d22fcc5b1d603) ). Давайте сосредоточимся на части, которую мы будем анимировать, в данном случае - на компоненте `Participant`.

```ts
// ...
function Participant({
    name,
    onRemove,
}: {
    name: string;
    onRemove: () => void;
}): React.ReactElement {
    return (
        <View style={[styles.participantView]}>
            <Text>{name}</Text>
            <Button
                title="Remove"
                color="red"
                onPress={onRemove}
            />
        </View>
    );
}

export default function AnimatedListExample(): React.ReactElement {
    const [inputValue, setInputValue] = useState('');
    const [participantList, setParticipantList] = useState<
        EventParticipant[]
    >([]);

    const addParticipant = () => {
        setParticipantList(
            [
                {
                    name: inputValue,
                    id: Date.now().toString(),
                },
            ].concat(participantList)
        );
        setInputValue('');
    };

    const removeParticipant = (id: string) => {
        setParticipantList(
            participantList.filter(
                (participant) => participant.id !== id
            )
        );
    };

    return (
        <View style={[styles.listView]}>
            <ScrollView style={[{ width: '100%' }]}>
                {participantList.map((participant) => (
                    <Participant
                        key={participant.id}
                        name={participant.name}
                        onRemove={() =>
                            removeParticipant(
                                participant.id
                            )
                        }
                    />
                ))}
            </ScrollView>

            <View style={[styles.bottomRow]}>
                <View style={[styles.textInput]}>
                    <Text>Add participant: </Text>
                    <TextInput
                        placeholder="Name"
                        value={inputValue}
                        onChangeText={setInputValue}
                    />
                </View>

                <Button
                    title="Add"
                    disabled={inputValue === ''}
                    onPress={addParticipant}
                />
            </View>
        </View>
    );
}
```

## Компонент `Participant`

Компонент `Participant` отвечает за отображение элемента списка, который мы будем анимировать.

```js
// ...
<ScrollView style={[{ width: '100%' }]}>
    {participantList.map((participant) => (
        <Participant
            key={participant.id}
            name={participant.name}
            onRemove={() =>
                removeParticipant(participant.id)
            }
        />
    ))}
</ScrollView>
// ...
```

## Внутреннее устройство компонента `Participant`

Как видно, компонент `Participant` обернут в один компонент `View`, и именно в него мы будем добавлять анимацию. Мы можем анимировать только те компоненты, которые предоставляются Reanimated (например, `Animated.View`), или компоненты, которые мы изменили с помощью `createAnimatedComponent`.

```js
// ...
function Participant({
    name,
    onRemove,
}: {
    name: string,
    onRemove: () => void,
}): React.ReactElement {
    return (
        <View style={[styles.participantView]}>
            <Text>{name}</Text>
            <Button
                title="Remove"
                color="red"
                onPress={onRemove}
            />
        </View>
    );
}
// ...
```

## Шаг 1

Импортируем `Animated` из Reanimated и заменим `View` на `Animated.View`, чтобы мы могли добавить анимацию в наш компонент.

```js
import Animated from 'react-native-reanimated';
// ...
function Participant({
    name,
    onRemove,
}: {
    name: string,
    onRemove: () => void,
}): React.ReactElement {
    return (
        <Animated.View style={[styles.participantView]}>
            <Text>{name}</Text>
            <Button
                title="Remove"
                color="red"
                onPress={onRemove}
            />
        </Animated.View>
    );
}
// ...
```

## Шаг 2

Теперь добавим анимацию входа. Существует множество доступных [предопределенных анимаций](./api/LayoutAnimations/EntryAnimations.md), из которых мы можем выбирать. Мы остановимся на эффекте `LightSpeedInLeft`.

```js
import Animated, {
    LightSpeedInLeft,
} from 'react-native-reanimated';
// ...
function Participant({
    name,
    onRemove,
}: {
    name: string,
    onRemove: () => void,
}): React.ReactElement {
    return (
        <Animated.View
            entering={LightSpeedInLeft}
            style={[styles.participantView]}
        >
            <Text>{name}</Text>
            <Button
                title="Remove"
                color="red"
                onPress={onRemove}
            />
        </Animated.View>
    );
}
// ...
```

## Промежуточный результат 1

Здесь вы можете увидеть эффект, которого мы добились, добавив анимацию входа.

![Текущее состояние](tutorial-entering.gif){:style="max-height: 500px"}

## Шаг 3

Теперь добавим переход к макету.

```js
import Animated, {
    LightSpeedInLeft,
    Layout,
} from 'react-native-reanimated';
// ...
function Participant({
    name,
    onRemove,
}: {
    name: string,
    onRemove: () => void,
}): React.ReactElement {
    return (
        <Animated.View
            entering={LightSpeedInLeft}
            layout={Layout.springify()}
            style={[styles.participantView]}
        >
            <Text>{name}</Text>
            <Button
                title="Remove"
                color="red"
                onPress={onRemove}
            />
        </Animated.View>
    );
}
// ...
```

## Промежуточный результат 2

Здесь показан эффект, которого мы добились, добавив переход макета.

![Current result 2](tutorial-layout.gif){:style="max-height: 500px"}

## Шаг 4

Теперь добавим анимацию выхода. Опять же существует множество [предопределенных анимаций](./api/LayoutAnimations/ExitAnimations.md), из которых мы можем выбирать. Мы остановимся на эффекте `LightSpeedOutRight`.

```js
import Animated, {
    LightSpeedInLeft,
    LightSpeedOutRight,
    Layout,
} from 'react-native-reanimated';
// ...
function Participant({
    name,
    onRemove,
}: {
    name: string,
    onRemove: () => void,
}): React.ReactElement {
    return (
        <Animated.View
            entering={LightSpeedInLeft}
            exiting={LightSpeedOutRight}
            layout={Layout.springify()}
            style={[styles.participantView]}
        >
            <Text>{name}</Text>
            <Button
                title="Remove"
                color="red"
                onPress={onRemove}
            />
        </Animated.View>
    );
}
// ...
```

## Итог

И это все! Как видите, добавить анимацию в список оказалось очень просто, а удобство использования стало намного выше. Полный код анимированного списка можно найти [здесь](https://gist.github.com/jmysliv/0056d6cd3021883ea0f2c7f4af0526b2).

![После](tutorial-after.gif){:style="max-height: 500px"}

## Ссылки

-   [How to easily animate your screens and components](https://docs.swmansion.com/react-native-reanimated/docs/tutorials/LayoutAnimations/layoutAnimations)
