---
description: API модуля Async Storage
---

# API

<!-- ------------------------ GET ITEM ------------------------ -->

## `getItem`

Получает строковое значение для заданного `key`. Эта функция может либо возвращать строковое значение для существующего `key`, либо возвращать `null` в противном случае.

Для хранения значения объекта его необходимо десериализовать, например, с помощью `JSON.parse()`.

!!!note "Примечание"

    в качестве альтернативы возвращаемому обещанию можно использовать опциональный обратный вызов.

**Описание**:

```js
static getItem(
	key: string,
	[callback]: ?(error: ?Error, result: ?string) => void,
): Promise
```

**Возвращает**:

`Promise`, разрешающий строковое значение, если запись для данного `key` существует, или `null` в противном случае.

`Promise` может быть также отклонена в случае ошибки хранения данных.

**Пример**:

```js
getMyStringValue = async () => {
    try {
        return await AsyncStorage.getItem('@key');
    } catch (e) {
        // read error
    }

    console.log('Done.');
};
```

```js
getMyObject = async () => {
    try {
        const jsonValue = await AsyncStorage.getItem(
            '@key'
        );
        return jsonValue != null
            ? JSON.parse(jsonValue)
            : null;
    } catch (e) {
        // read error
    }

    console.log('Done.');
};
```

<!-- ------------------------ SET ITEM ------------------------ -->

## `setItem`

Устанавливает строку `value` для заданного `key`. Эта операция может либо модифицировать существующую запись, если она уже существовала для данного `key`, либо добавить новую.

Для хранения значения объекта необходимо его сериализовать, например, с помощью `JSON.stringify()`.

!!!note "Примечание"

    в качестве альтернативы возвращаемому обещанию можно использовать опциональный обратный вызов.

**Описание**:

```js
static setItem(
	key: string,
	value: string,
	[callback]: ?(error: ?Error) => void,
): Promise
```

**Возвращает**:

`Promise`, разрешающееся по завершении операции set.

`Promise` также может быть отвергнут в случае ошибки хранения данных.

**Пример**:

```js
setStringValue = async (value) => {
    try {
        await AsyncStorage.setItem('key', value);
    } catch (e) {
        // save error
    }

    console.log('Done.');
};
```

```js
setObjectValue = async (value) => {
    try {
        const jsonValue = JSON.stringify(value);
        await AsyncStorage.setItem('key', jsonValue);
    } catch (e) {
        // save error
    }

    console.log('Done.');
};
```

<!-- ------------------------ MERGE ITEM ------------------------ -->

## `mergeItem`

Объединяет существующее значение, хранящееся под `key`, с новым `value`, предполагая, что оба значения являются **строками JSON**.

**Описание**:

```js
static mergeItem(
	key: string,
	value: string,
	[callback]: ?(error: ?Error) => void,
): Promise
```

**Возвращает**:

`Promise` с объединенными данными, если они существуют, `null` в противном случае.

**Пример**:

```js
const USER_1 = {
    name: 'Tom',
    age: 20,
    traits: {
        hair: 'black',
        eyes: 'blue',
    },
};

const USER_2 = {
    name: 'Sarah',
    age: 21,
    hobby: 'cars',
    traits: {
        eyes: 'green',
    },
};

mergeUsers = async () => {
    try {
        //save first user
        await AsyncStorage.setItem(
            '@MyApp_user',
            JSON.stringify(USER_1)
        );

        // merge USER_2 into saved USER_1
        await AsyncStorage.mergeItem(
            '@MyApp_user',
            JSON.stringify(USER_2)
        );

        // read merged item
        const currentUser = await AsyncStorage.getItem(
            '@MyApp_user'
        );

        console.log(currentUser);

        // console.log result:
        // {
        //   name: 'Sarah',
        //   age: 21,
        //   hobby: 'cars',
        //   traits: {
        //     eyes: 'green',
        //     hair: 'black'
        //   }
        // }
    }
};
```

<!-- ------------------------ REMOVE ITEM ------------------------ -->

## `removeItem`

Удаляет элемент для `key`, после завершения вызывает (необязательный) обратный вызов.

**Описание**:

```js
static removeItem(
	key: string,
	[callback]: ?(error: ?Error) => void,
): Promise
```

**Возвращает**:

Объект `Promise`.

**Пример**:

```js
removeValue = async () => {
    try {
        await AsyncStorage.removeItem('@MyApp_key');
    } catch (e) {
        // remove error
    }

    console.log('Done.');
};
```

<!-- ------------------------ GET ALL KEYS ------------------------ -->

## `getAllKeys`

Возвращает все ключи, известные вашему приложению, для всех вызывающих, библиотек и т.д. После завершения вызывается `callback` с ошибками (если таковые имеются) и массивом ключей.

**Описание**:

```js
static getAllKeys(
	[callback]: ?(error: ?Error, keys: ?Array<string>) => void,
): Promise
```

**Возвращает**:

Объект `Promise`.

**Пример**:

```js
getAllKeys = async () => {
    let keys = [];
    try {
        keys = await AsyncStorage.getAllKeys();
    } catch (e) {
        // read key error
    }

    console.log(keys);
    // example console.log result:
    // ['@MyApp_user', '@MyApp_key']
};
```

<!-- ------------------------ MULTI GET ITEM ------------------------ -->

## `multiGet`

Пакетная выборка нескольких пар ключ-значение для заданного массива `keys`. После завершения вызывается `callback` с ошибками (если таковые имеются) и результатами.

**Описание**:

```js
static multiGet(
	keys: Array<string>,
	[callback]: ?(
		errors: ?Array<Error>,
		result: ?Array<Array<string>>
	) => void,
): Promise
```

**Возвращает**:

`Promise` массива с найденными соответствующими парами ключ-значение, хранящегося в виде массива `[key, value]`.

**Пример**:

```js
getMultiple = async () => {
    let values;
    try {
        values = await AsyncStorage.multiGet([
            '@MyApp_user',
            '@MyApp_key',
        ]);
    } catch (e) {
        // read error
    }
    console.log(values);

    // example console.log output:
    // [ ['@MyApp_user', 'myUserValue'], ['@MyApp_key', 'myKeyValue'] ]
};
```

<!-- ------------------------ MULTI SET ITEM ------------------------ -->

## `multiSet`

Пакетно сохраняет несколько пар ключ-значение. После завершения будет вызван `callback` с любыми ошибками.

**Описание**:

```js
static multiSet(
	keyValuePairs: Array<Array<string>>,
	[callback]: ?(errors: ?Array<Error>) => void
): Promise
```

**Возвращает**:

Объект `Promise`.

**Пример**:

```js
multiSet = async () => {
    const firstPair = ['@MyApp_user', 'value_1'];
    const secondPair = ['@MyApp_key', 'value_2'];
    try {
        await AsyncStorage.multiSet([
            firstPair,
            secondPair,
        ]);
    } catch (e) {
        //save error
    }

    console.log('Done.');
};
```

<!-- ------------------------ MULTI MERGE ------------------------ -->

## `multiMerge`

Многократное объединение существующих и новых значений в пакетном режиме. Предполагается, что значения представляют собой _строки JSON_. После завершения вызывается `callback` с ошибками (если таковые имеются).

**Описание**:

```js
static multiMerge(
	keyValuePairs: Array<Array<string>>,
	[callback]: ?(errors: ?Array<Error>) => void,
): Promise
```

**Возвращает**:

Объект `Promise`.

**Пример**:

```js
const USER_1 = {
    name: 'Tom',
    age: 30,
    traits: { hair: 'brown' },
};

const USER_1_DELTA = {
    age: 31,
    traits: { eyes: 'blue' },
};

const USER_2 = {
    name: 'Sarah',
    age: 25,
    traits: { hair: 'black' },
};

const USER_2_DELTA = {
    age: 26,
    traits: { hair: 'green' },
};

const multiSet = [
    ['@MyApp_USER_1', JSON.stringify(USER_1)],
    ['@MyApp_USER_2', JSON.stringify(USER_2)],
];

const multiMerge = [
    ['@MyApp_USER_1', JSON.stringify(USER_1_DELTA)],
    ['@MyApp_USER_2', JSON.stringify(USER_2_DELTA)],
];

mergeMultiple = async () => {
    let currentlyMerged;

    try {
        await AsyncStorage.multiSet(multiSet);
        await AsyncStorage.multiMerge(multiMerge);
        currentlyMerged = await AsyncStorage.multiGet([
            '@MyApp_USER_1',
            '@MyApp_USER_2',
        ]);
    } catch (e) {
        // error
    }

    console.log(currentlyMerged);
    // console.log output:
    // [
    //   [
    //     'USER_1',
    //     {
    //       name:"Tom",
    //       age:30,
    //       traits: {
    //         hair: 'brown'
    //         eyes: 'blue'
    //       }
    //     }
    //   ],
    //   [
    //     'USER_2',
    //     {
    //       name:'Sarah',
    //       age:26,
    //       traits: {
    //         hair: 'green'
    //       }
    //     }
    //   ]
    // ]
};
```

<!-- ------------------------ MULTI REMOVE ------------------------ -->

## `multiRemove`

Пакетно очищает несколько записей типа "ключ-значение" для заданного массива `keys`. После завершения вызывается `callback` с ошибками (если таковые имеются).

**Описание**:

```js
static multiRemove(
	keys: Array<string>,
	[callback]: ?(errors: ?Array<Error>) => void,
): Promise
```

**Возвращает**:

Объект `Promise`.

**Пример**:

```js
removeFew = async () => {
    const keys = ['@MyApp_USER_1', '@MyApp_USER_2'];
    try {
        await AsyncStorage.multiRemove(keys);
    } catch (e) {
        // remove error
    }

    console.log('Done');
};
```

<!-- ------------------------ CLEAR ------------------------ -->

## `clear`

Удаляет **все** данные `AsyncStorage`, для всех клиентов, библиотек и т.д. Вероятно, для очистки только ключей вашего приложения лучше использовать [`removeItem`](#removeitem) или [`multiRemove`](#multiremove).

**Описание**:

```js
static clear(
	[callback]: ?(error: ?Error) => void,
): Promise
```

**Возвращает**:

Объект `Promise`.

**Пример**:

```js
clearAll = async () => {
    try {
        await AsyncStorage.clear();
    } catch (e) {
        // clear error
    }

    console.log('Done.');
};
```

<!-- ------------------------ HOOKS ------------------------ -->

## `useAsyncStorage`

!!!note "Примечание"

    Интерфейс, похожий на хуки, с которым мы экспериментируем. В ближайшем будущем он будет изменен, чтобы полностью задействовать Hooks API, поэтому не стесняйтесь [следить за этим обсуждением, чтобы узнать больше](https://github.com/react-native-community/react-native-async-storage/issues/32).

Функция `useAsyncStorage` возвращает объект, раскрывающий все методы, позволяющие взаимодействовать с хранимым значением.

**Описание**:

```js
static useAsyncStorage(key: string): {
  getItem: (
    callback?: ?(error: ?Error, result: string | null) => void,
  ) => Promise<string | null>,
  setItem: (
    value: string,
    callback?: ?(error: ?Error) => void,
  ) => Promise<null>,
  mergeItem: (
    value: string,
    callback?: ?(error: ?Error) => void,
  ) => Promise<null>,
  removeItem: (
	callback?: ?(error: ?Error) => void
  ) => Promise<null>,
}
```

**Возвращает**:

`object`.

**Специальный пример**:

Вы можете заменить свой `App.js` на следующий, чтобы увидеть его в действии.

```js
import React, { useState, useEffect } from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
import { useAsyncStorage } from '@react-native-async-storage/async-storage';

export default function App() {
    const [value, setValue] = useState('value');
    const { getItem, setItem } = useAsyncStorage(
        '@storage_key'
    );

    const readItemFromStorage = async () => {
        const item = await getItem();
        setValue(item);
    };

    const writeItemToStorage = async (newValue) => {
        await setItem(newValue);
        setValue(newValue);
    };

    useEffect(() => {
        readItemFromStorage();
    }, []);

    return (
        <View style={{ margin: 40 }}>
            <Text>Current value: {value}</Text>
            <TouchableOpacity
                onPress={() =>
                    writeItemToStorage(
                        Math.random()
                            .toString(36)
                            .substr(2, 5)
                    )
                }
            >
                <Text>Update value</Text>
            </TouchableOpacity>
        </View>
    );
}
```

В данном примере:

1.  При монтировании мы считываем значение по адресу `@storage_key` и сохраняем его в состоянии под `value`.
2.  При нажатии на кнопку "обновить значение" генерируется новая строка, сохраняется в асинхронном хранилище и в состоянии компонента
3.  Попробуйте перезагрузить приложение - вы увидите, что последнее значение по-прежнему считывается из async-хранилища
