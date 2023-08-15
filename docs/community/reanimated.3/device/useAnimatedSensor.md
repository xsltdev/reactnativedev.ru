---
description: С помощью хука useAnimatedSensor можно легко создавать классные интерактивные анимации на основе данных от датчиков устройства, таких как гироскоп, акселерометр и т. д.
---

# useAnimatedSensor

!!!info ""

    Эта страница была перенесена из старой версии документации.

    В процессе переписывания документации некоторые страницы могут быть немного устаревшими.

С помощью хука `useAnimatedSensor` можно легко создавать классные интерактивные анимации на основе данных от датчиков устройства, таких как гироскоп, акселерометр и т. д.

```js
useAnimatedSensor(
	sensorType: [SensorType],
	config?: [UserConfig]
) -> [AnimatedSensor]
```

## Аргументы

### `sensorType` - [[SensorType](#sensortype-enum)]

Вы можете выбрать датчик, доступный в перечне [[SensorType](#sensortype-enum)].

### `config` - [[UserConfig](#userconfig-object)]

Опционально можно передать конфигурацию для настройки поведения датчика.

## Returns

Хук `useAnimatedSensor` возвращает экземпляр [[AnimatedSensor](#animatedsensor-object)];

## Типы

### `AnimatedSensor: [object]`

Свойства:

-   `sensor`: [[SharedValue](../core/useSharedValue.md)] содержит [[3DVector](#3dvector-object)] или [[RotationVector](#rotationvector-object)] или `null` содержит реальные измерения датчика как общее значение
-   `unregister: [function]` позволяет прекратить прослушивание обновлений датчика
-   `isAvailable: [boolean]` флаг содержит информацию о доступности датчиков в устройстве
-   `config`: [[UserConfig](#userconfig-object)] конфигурация, предоставленная пользователем

### `SensorType: [enum]`

`SensorType` - это перечисление, содержащее возможные поддерживаемые датчики. Значения:

-   `ACCELEROMETER` измерения выводятся в виде [[3DVector](#3dvector-object)]. Измеряется в м/с², без учета силы тяжести.
-   Измерения `GYROSCOPE` выводятся в виде [[3DVector](#3dvector-object)]. Измеряется в рад/с.
-   Измерения `GRAVITY` выводятся в виде [[3DVector](#3dvector-object)]. Измеряется в м/с².
-   Измерения `MAGNETIC_FIELD` выводятся в виде [[3DVector](#3dvector-object)]. Измеряется в мкТл.
-   Измерения `ROTATION` выводятся в виде [[RotationVector](#rotationvector-object)]. [qx, qy, qz, qw] - нормализованный кватернион. [yaw, pitch, roll] - вращение в радианах вдоль соответствующих осей. Мы следуем [конвенции iOS](https://developer.apple.com/documentation/coremotion/getting_processed_device-motion_data/understanding_reference_frames_and_device_attitude).

### `UserConfig: [object]`

Свойства:

-   `interval: [number | auto]` - интервал в миллисекундах между обновлениями общих значений. Передайте `'auto'` для выбора интервала на основе частоты кадров устройства. По умолчанию: `'auto'`.
-   `iosReferenceFrame`: [[IOSReferenceFrame](#iosreferenceframe-enum)]`- опорная рамка для использования на iOS. По умолчанию:`Auto`.
-   `adjustToInterfaceOrientation: [boolean]` - нужно ли подстраивать измерения под текущую ориентацию интерфейса. Например, при альбомной ориентации оси x и y могут быть перевернуты при рисовании на экране. По умолчанию это значение равно `true`.

### `IOSReferenceFrame: [enum]`

`IOSReferenceFrame` - перечисление, описывающее систему отсчета, используемую в iOS. Он соответствует [документации Apple](https://developer.apple.com/documentation/coremotion/cmattitudereferenceframe). Возможные значения:

-   `XArbitraryZVertical`
-   `XArbitraryCorrectedZVertical`
-   `XMagneticNorthZVertical`
-   `XTrueNorthZVertical`
-   `Auto` - на устройствах без магнитометра (например, iPod) `XArbitraryZVertical`, на устройствах с магнитометром `XArbitraryCorrectedZVertical`.

### `3DVector: [object]`

Свойства:

-   `x: number`
-   `y: number`
-   `z: number`
-   `interfaceOrientation: [[InterfaceOrientation](#interfaceorientation-enum)]`

### `RotationVector: [object]`

Свойства:

-   `qw: number`
-   `qx: number`
-   `qy: number`
-   `qz: number`
-   `yaw: number`
-   `pitch: number`
-   `roll: number`
-   `interfaceOrientation: [[InterfaceOrientation](#interfaceorientation-enum)]`

### `InterfaceOrientation: [enum]`

Значения:

-   `ROTATION_0` - поворот по умолчанию на Android, портретная ориентация на iOS
-   `ROTATION_90` - поворот на 90 градусов на Android, альбомная ориентация справа на iOS (альбомная ориентация и кнопка home справа)
-   `ROTATION_180` - поворот на 180 градусов на Android, перевернутая ориентация на iOS
-   `ROTATION_270` - поворот на 270 градусов на Android, альбомная левая ориентация на iOS (альбомная и кнопка "Домой" слева)

## Пример

```js
function UseAnimatedSensorExample() {
    const animatedSensor = useAnimatedSensor(
        SensorType.ROTATION,
        {
            interval: 10,
        }
    ); // <- initialization
    const style = useAnimatedStyle(() => {
        const yaw = Math.abs(
            animatedSensor.sensor.value.yaw
        );
        const pitch = Math.abs(
            animatedSensor.sensor.value.pitch
        );
        return {
            height: withTiming(yaw * 200 + 20, {
                duration: 100,
            }), // <- usage
            width: withTiming(pitch * 200 + 20, {
                duration: 100,
            }), // <- usage
        };
    });

    return (
        <View
            style={{
                flex: 1,
                justifyContent: 'center',
                alignItems: 'center',
            }}
        >
            <Animated.View
                style={[
                    { backgroundColor: 'black' },
                    style,
                ]}
            />
        </View>
    );
}
```

## Живой пример

<video
  src="https://user-images.githubusercontent.com/36106620/158634922-eaad656e-c837-44d5-8d51-8e7fa27c5a16.mp4"
  controls="controls"
  muted="muted"
  width="400"></video>

## Советы

!!!warning ""

    На iOS для считывания данных с датчиков необходимо включить на устройстве службы определения местоположения (`Настройки > Конфиденциальность > Службы определения местоположения`).

## Ссылки

-   [useAnimatedSensor](https://docs.swmansion.com/react-native-reanimated/docs/device/useAnimatedSensor)
