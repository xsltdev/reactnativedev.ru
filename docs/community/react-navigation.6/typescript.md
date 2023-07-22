---
description: React Navigation написан на TypeScript и экспортирует определения типов для TypeScript-проектов
---

# Проверка типов с помощью TypeScript

React Navigation написан на TypeScript и экспортирует определения типов для TypeScript-проектов.

## Проверка типа навигатора

Для проверки типа имени и параметров маршрута необходимо создать объектный тип с отображениями имени маршрута на параметры маршрута. Например, в нашем корневом навигаторе есть маршрут `Profile`, который должен иметь параметр `userId`:

```ts
type RootStackParamList = {
    Profile: { userId: string };
};
```

Аналогичным образом необходимо поступить с каждым маршрутом:

```ts
type RootStackParamList = {
    Home: undefined;
    Profile: { userId: string };
    Feed: { sort: 'latest' | 'top' } | undefined;
};
```

Указание `undefined` означает, что маршрут не имеет параметров. Тип объединения с `undefined` (например, `SomeType | undefined`) означает, что параметры являются необязательными.

После того как мы определили отображение, необходимо указать навигатору, что его нужно использовать. Для этого мы можем передать его в качестве generic функции `createXNavigator`:

```ts
import { createStackNavigator } from '@react-navigation/stack';

const RootStack = createStackNavigator<
    RootStackParamList
>();
```

И тогда мы сможем его использовать:

```ts
<RootStack.Navigator initialRouteName="Home">
    <RootStack.Screen name="Home" component={Home} />
    <RootStack.Screen
        name="Profile"
        component={Profile}
        initialParams={{ userId: user.id }}
    />
    <RootStack.Screen name="Feed" component={Feed} />
</RootStack.Navigator>
```

Это обеспечит проверку типов и intelliSense для реквизитов компонентов `Navigator` и `Screen`.

!!!warning "Замечание"

    Тип, содержащий сопоставления, должен быть псевдонимом типа (например, `type RootStackParamList = { ... }`). Он не может быть интерфейсом (например, `interface RootStackParamList { ... }`). Он также не должен расширять `ParamListBase` (например, `interface RootStackParamList extends ParamListBase { ... }`). Это приведет к некорректной проверке типов, которая позволит передавать некорректные имена маршрутов.

### Проверка типов экранов

Для проверки типов экранов нам необходимо аннотировать реквизит `navigation` и реквизит `route`, получаемые экраном. Пакеты навигаторов в React Navigation экспортируют общие типы для определения типов реквизитов `navigation` и `route` из соответствующего навигатора.

Например, для нативного стекового навигатора можно использовать `NativeStackScreenProps`.

```ts
import type { NativeStackScreenProps } from '@react-navigation/native-stack';

type RootStackParamList = {
    Home: undefined;
    Profile: { userId: string };
    Feed: { sort: 'latest' | 'top' } | undefined;
};

type Props = NativeStackScreenProps<
    RootStackParamList,
    'Profile'
>;
```

Тип принимает 3 дженерика:

-   Объект списка param, который мы определили ранее
-   Имя маршрута, к которому относится экран
-   ID навигатора (необязательно).

Если у вас есть реквизит `id` для навигатора, то можно сделать так:

```ts
type Props = NativeStackScreenProps<
    RootStackParamList,
    'Profile',
    'MyStack'
>;
```

Это позволяет нам проверять имена и параметры маршрутов, по которым осуществляется навигация с помощью `navigate`, `push` и т.д. Имя текущего маршрута необходимо для проверки параметров в `route.params` и при вызове `setParams`.

Аналогично можно импортировать `StackScreenProps` для `@react-navigation/stack`, `DrawerScreenProps` из `@react-navigation/drawer`, `BottomTabScreenProps` из `@react-navigation/bottom-tabs` и так далее.

Затем вы можете использовать тип `Props`, определенный выше, для аннотирования вашего компонента.

Для функциональных компонентов:

```ts
function ProfileScreen({ route, navigation }: Props) {
    // ...
}
```

Для классовых компонентов:

```ts
class ProfileScreen extends React.Component<Props> {
    render() {
        // ...
    }
}
```

Типы `navigation` и `route` можно получить из типа `Props` следующим образом:

```ts
type ProfileScreenNavigationProp = Props['navigation'];

type ProfileScreenRouteProp = Props['route'];
```

В качестве альтернативы можно аннотировать реквизиты `navigation` и `route` по отдельности.

Чтобы получить тип для реквизита `navigation`, необходимо импортировать соответствующий тип из навигатора. Например, `NativeStackNavigationProp` для `@react-navigation/native-stack`:

```ts
import type { NativeStackNavigationProp } from '@react-navigation/native-stack';

type ProfileScreenNavigationProp = NativeStackNavigationProp<
    RootStackParamList,
    'Profile'
>;
```

Аналогично можно импортировать `StackNavigationProp` из `@react-navigation/stack`, `DrawerNavigationProp` из `@react-navigation/drawer`, `BottomTabNavigationProp` из `@react-navigation/bottom-tabs` и т.д.

Чтобы получить тип для реквизита `route`, необходимо использовать тип `RouteProp` из `@react-navigation/native`:

```ts
import type { RouteProp } from '@react-navigation/native';

type ProfileScreenRouteProp = RouteProp<
    RootStackParamList,
    'Profile'
>;
```

Мы рекомендуем создать отдельный файл `types.tsx`, в котором хранить типы и импортировать их в файлы компонентов вместо того, чтобы повторять их в каждом файле.

### Вложенные навигаторы

#### Проверка типов экранов и параметров во вложенном навигаторе

Вы можете [переходить к экрану во вложенном навигаторе](nesting-navigators.md#navigating-to-a-screen-in-a-nested-navigator), передавая свойства `screen` и `params` для вложенного экрана:

```ts
navigation.navigate('Home', {
    screen: 'Feed',
    params: { sort: 'latest' },
});
```

Чтобы проверить это, необходимо извлечь параметры из экрана, содержащего вложенный навигатор. Это можно сделать с помощью утилиты `NavigatorScreenParams`:

```ts
import { NavigatorScreenParams } from '@react-navigation/native';

type TabParamList = {
    Home: NavigatorScreenParams<StackParamList>;
    Profile: { userId: string };
};
```

#### Комбинирование навигационных реквизитов

При вложении навигаторов навигационный реквизит экрана представляет собой комбинацию нескольких навигационных реквизитов. Например, если у нас есть вкладка внутри стопки, то реквизит `navigation` будет содержать как `jumpTo` (из навигатора вкладок), так и `push` (из навигатора стопки). Чтобы упростить объединение типов из нескольких навигаторов, можно использовать тип `CompositeScreenProps`:

```ts
import type { CompositeScreenProps } from '@react-navigation/native';
import type { BottomTabScreenProps } from '@react-navigation/bottom-tabs';
import type { StackScreenProps } from '@react-navigation/stack';

type ProfileScreenProps = CompositeScreenProps<
    BottomTabScreenProps<TabParamList, 'Profile'>,
    StackScreenProps<StackParamList>
>;
```

Тип `CompositeScreenProps` принимает 2 параметра, первый - тип реквизита для первичной навигации (тип для навигатора, которому принадлежит данный экран, в нашем случае - навигатор вкладок, содержащий экран `Profile`) и второй - тип реквизита для вторичной навигации (тип для родительского навигатора). Первичный тип всегда должен иметь в качестве второго параметра имя маршрута экрана.

Для нескольких родительских навигаторов этот вторичный тип должен быть вложенным:

```ts
type ProfileScreenProps = CompositeScreenProps<
    BottomTabScreenProps<TabParamList, 'Profile'>,
    CompositeScreenProps<
        StackScreenProps<StackParamList>,
        DrawerScreenProps<DrawerParamList>
    >
>;
```

Если аннотировать свойство `navigation` отдельно, то вместо него можно использовать `CompositeNavigationProp`. Использование аналогично `CompositeScreenProps`:

```ts
import type { CompositeNavigationProp } from '@react-navigation/native';
import type { BottomTabNavigationProp } from '@react-navigation/bottom-tabs';
import type { StackNavigationProp } from '@react-navigation/stack';

type ProfileScreenNavigationProp = CompositeNavigationProp<
    BottomTabNavigationProp<TabParamList, 'Profile'>,
    StackNavigationProp<StackParamList>
>;
```

### Аннотирование `useNavigation`

Для аннотирования свойства `navigation`, которое мы получаем от `useNavigation`, мы можем использовать параметр `type`:

```ts
const navigation = useNavigation<
    ProfileScreenNavigationProp
>();
```

Важно отметить, что это не совсем безопасно для типов, поскольку используемый параметр типа может быть некорректным, а мы не можем проверить его статически.

### Аннотирование `useRoute`

Для аннотирования свойства `route`, которое мы получаем от `useRoute`, мы можем использовать параметр типа:

```ts
const route = useRoute<ProfileScreenRouteProp>();
```

Важно отметить, что это не полностью безопасно для типов, подобно `useNavigation`.

### Аннотирование `options` и `screenOptions`

Когда вы передаете компоненту `Navigator` свойство `options` для `Screen` или `screenOptions`, они уже проверены на соответствие типу, и вам не нужно делать ничего особенного. Однако иногда может потребоваться извлечь опции в отдельный объект и снабдить его аннотацией.

Для аннотирования опций необходимо импортировать соответствующий тип из навигатора. Например, `StackNavigationOptions` для `@react-navigation/stack`:

```ts
import type { StackNavigationOptions } from '@react-navigation/stack';

const options: StackNavigationOptions = {
    headerShown: false,
};
```

Аналогично можно импортировать `DrawerNavigationOptions` из `@react-navigation/drawer`, `BottomTabNavigationOptions` из `@react-navigation/bottom-tabs` и т.д.

При использовании функциональной формы `options` и `screenOptions` можно аннотировать аргументы тем же типом, который использовался для аннотирования реквизитов `navigation` и `route`.

### Аннотирование `ref` на `NavigationContainer`

Если вы используете метод `createNavigationContainerRef()` для создания ссылки, то можете аннотировать ее для проверки типов навигационных действий:

```ts
import { createNavigationContainerRef } from '@react-navigation/native';

// ...

const navigationRef = createNavigationContainerRef<
    RootStackParamList
>();
```

Аналогично для `useNavigationContainerRef()`:

```ts
import { useNavigationContainerRef } from '@react-navigation/native';

// ...

const navigationRef = useNavigationContainerRef<
    RootStackParamList
>();
```

Если вы используете обычный объект `ref`, то в тип `NavigationContainerRef` можно передать generic.

Пример при использовании хука `React.useRef`:

```ts
import type { NavigationContainerRef } from '@react-navigation/native';

// ...

const navigationRef = React.useRef<
    NavigationContainerRef<RootStackParamList>
>(null);
```

Пример использования `React.createRef`:

```ts
import type { NavigationContainerRef } from '@react-navigation/native';

// ...

const navigationRef = React.createRef<
    NavigationContainerRef<RootStackParamList>
>();
```

### Указание типов по умолчанию для `useNavigation`, `Link`, `ref` и т.д.

Вместо того чтобы вручную аннотировать эти API, можно указать глобальный тип для корневого навигатора, который будет использоваться в качестве типа по умолчанию.

Для этого можно добавить этот фрагмент в кодовую базу:

```js
declare global {
  namespace ReactNavigation {
    interface RootParamList extends RootStackParamList {}
  }
}
```

Интерфейс `RootParamList` позволяет React Navigation узнать о параметрах, принимаемых вашим корневым навигатором. Здесь мы расширяем тип `RootStackParamList`, так как это тип параметров для нашего стекового навигатора в корне. Название этого типа не имеет значения.

Указание этого типа важно, если вы часто используете в своем приложении `useNavigation`, `Link` и т.д., поскольку это обеспечит безопасность типов. Это также обеспечит правильную вложенность реквизита `linking`.

### Организация типов

При написании типов для React Navigation есть несколько вещей, которые мы рекомендуем использовать для упорядочивания.

1.  Хорошо создать отдельный файл (например, `navigation/types.tsx`), который будет содержать типы, относящиеся к React Navigation.
2.  Вместо того чтобы использовать `CompositeNavigationProp` непосредственно в компонентах, лучше создать вспомогательный тип, который можно использовать повторно.
3.  Указание глобального типа для корневого навигатора позволит избежать ручных аннотаций во многих местах.

С учетом этих рекомендаций файл, содержащий типы, может выглядеть примерно так:

```ts
import type {
    CompositeScreenProps,
    NavigatorScreenParams,
} from '@react-navigation/native';
import type { StackScreenProps } from '@react-navigation/stack';
import type { BottomTabScreenProps } from '@react-navigation/bottom-tabs';

export type RootStackParamList = {
    Home: NavigatorScreenParams<HomeTabParamList>;
    PostDetails: { id: string };
    NotFound: undefined;
};

export type RootStackScreenProps<
    T extends keyof RootStackParamList
> = StackScreenProps<RootStackParamList, T>;

export type HomeTabParamList = {
    Popular: undefined;
    Latest: undefined;
};

export type HomeTabScreenProps<
    T extends keyof HomeTabParamList
> = CompositeScreenProps<
    BottomTabScreenProps<HomeTabParamList, T>,
    RootStackScreenProps<keyof RootStackParamList>
>;

declare global {
    namespace ReactNavigation {
        interface RootParamList
            extends RootStackParamList {}
    }
}
```

Теперь при аннотировании компонентов можно написать:

```ts
import type { HomeTabScreenProps } from './navigation/types';

function PopularScreen({
    navigation,
    route,
}: HomeTabScreenProps<'Popular'>) {
    // ...
}
```

Если вы используете хуки, такие как `useRoute`, вы можете написать:

```ts
import type { HomeTabScreenProps } from './navigation/types';

function PopularScreen() {
    const route = useRoute<
        HomeTabScreenProps<'Popular'>['route']
    >();

    // ...
}
```

## Ссылки

-   [Type checking with TypeScript](https://reactnavigation.org/docs/typescript/)
