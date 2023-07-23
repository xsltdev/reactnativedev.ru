---
description: Async Storage может хранить только строковые данные. Для хранения объектных данных их необходимо сначала сериализовать
---

# Использование

**Async Storage** может хранить только `string` данные. Для хранения объектных данных их необходимо сначала сериализовать. Для данных, которые могут быть сериализованы в JSON, можно использовать `JSON.stringify()` при сохранении данных и `JSON.parse()` при их загрузке.

## Импорт

```js
import AsyncStorage from '@react-native-async-storage/async-storage';
```

## Хранение данных

Функция `setItem()` используется как для добавления нового элемента данных (при отсутствии данных для данного ключа), так и для модификации существующего элемента (при наличии предыдущих данных для данного ключа).

### Хранение строкового значения

```js
const storeData = async (value) => {
    try {
        await AsyncStorage.setItem('my-key', value);
    } catch (e) {
        // saving error
    }
};
```

### Хранение значения объекта

```js
const storeData = async (value) => {
    try {
        const jsonValue = JSON.stringify(value);
        await AsyncStorage.setItem('my-key', jsonValue);
    } catch (e) {
        // saving error
    }
};
```

## Чтение данных

`getItem` возвращает обещание, которое либо разрешается в хранимое значение, если данные найдены для заданного ключа, либо возвращает `null` в противном случае.

### Чтение строкового значения

```js
const getData = async () => {
    try {
        const value = await AsyncStorage.getItem('my-key');
        if (value !== null) {
            // value previously stored
        }
    } catch (e) {
        // error reading value
    }
};
```

### Чтение значения объекта

```js
const getData = async () => {
    try {
        const jsonValue = await AsyncStorage.getItem(
            'my-key'
        );
        return jsonValue != null
            ? JSON.parse(jsonValue)
            : null;
    } catch (e) {
        // error reading value
    }
};
```

## Подробнее

Для получения дополнительных примеров [перейдите в раздел API](api.md).
