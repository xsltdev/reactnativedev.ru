---
description: Функция useFrameCallback позволяет запускать функцию при каждом обновлении кадра.
---

# useFrameCallback

Функция `useFrameCallback` позволяет запускать функцию при каждом обновлении кадра.

```ts
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
    useFrameCallback,
    useSharedValue,
    useAnimatedStyle,
} from 'react-native-reanimated';

export default function App() {
    const t = useSharedValue(0);

    // highlight-start
    useFrameCallback((frameInfo) => {
        t.value = frameInfo.timeSinceFirstFrame / 350;
    });
    // highlight-end

    const infinityStyle = useAnimatedStyle(() => {
        const scale =
            (2 / (3 - Math.cos(2 * t.value))) * 200;
        return {
            transform: [
                { translateX: scale * Math.cos(t.value) },
                {
                    translateY:
                        scale * (Math.sin(2 * t.value) / 2),
                },
            ],
        };
    });

    return (
        <View style={styles.container}>
            <Animated.View
                style={[styles.dot, infinityStyle]}
            />
        </View>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        height: 150,
    },
    dot: {
        width: 60,
        height: 60,
        borderRadius: 30,
        backgroundColor: '#b58df1',
        position: 'absolute',
    },
});
```

## справочник

```javascript
import { useFrameCallback } from 'react-native-reanimated';

function App() {
    const frameCallback = useFrameCallback((frameInfo) => {
        // Increment a value on every frame update
        sv.value += 1;
    });

    return (
        <Button
            title="Start/Stop"
            onPress={() =>
                frameCallback.setActive(
                    !frameCallback.isActive
                )
            }
        />
    );
}
```

Определения типов

```typescript
type FrameInfo = {
    timestamp: number;
    timeSincePreviousFrame: number | null;
    timeSinceFirstFrame: number;
};

type FrameCallback = {
    setActive: (isActive: boolean) => void;
    isActive: boolean;
    callbackId: number;
};

function useFrameCallback(
    callback: (frameInfo: FrameInfo) => void,
    autostart = true
): FrameCallback;
```

</details>

### Аргументы

#### `callback`

Функция, выполняемая при каждом обновлении кадра. Эта функция получает объект `frameInfo`, содержащий следующие поля:

-   `timestamp` - число, указывающее системное время (в миллисекундах), когда был отрендерен последний кадр.
-   `timeSincePreviousFrame` - число, указывающее время (в миллисекундах), прошедшее с момента последнего кадра. На первом кадре после активации это значение будет равно null. Начиная со второго кадра, оно должно составлять ~16 мс на 60 Гц и ~8 мс на 120 Гц дисплеях (при условии отсутствия провалов кадров).
-   `timeSinceFirstFrame` - число, указывающее время (в миллисекундах) с момента активации обратного вызова.

#### `autostart`

Должен ли обратный вызов запускаться автоматически. По умолчанию `true`.

### Возвращает

`useFrameCallback` возвращает объект, содержащий такие поля:

-   `setActive` - функция, позволяющая запустить обратный вызов кадра или остановить его выполнение
-   `isActive` булево число, указывающее, запущен ли обратный вызов
-   `callbackId` - число, указывающее на уникальный идентификатор обратного вызова кадра.

## Пример

```ts
import Animated, {
    useSharedValue,
    useFrameCallback,
    useAnimatedStyle,
    withSequence,
    withTiming,
    Easing,
} from 'react-native-reanimated';
import {
    View,
    StyleSheet,
    Pressable,
    Text,
} from 'react-native';
import React from 'react';

const HEIGHT = 200;
const DEFAULT_VELOCITY = 0.6;
const VELOCITY_INCREMENT = 0.00005;
const GROUND_LEVEL = 80;
const DEFAULT_Y = HEIGHT - GROUND_LEVEL - 80;
const DEFAULT_X = 1000;

const DEFAULT_OBSTACLE = {
    height: 85,
    width: 82,
    x: 0,
    y: DEFAULT_Y,
};
const DEFAULT_HORSE = {
    height: 85,
    width: 82,
    x: 0,
    y: DEFAULT_Y,
};

export default function FrameCallbackDino() {
    const vx = useSharedValue(DEFAULT_VELOCITY);
    const width = useSharedValue(0);

    const obstacleX = useSharedValue(DEFAULT_X);
    const horseY = useSharedValue(DEFAULT_Y);

    const gameOver = useSharedValue(false);

    const getDimensions = (event) => {
        width.value = event.nativeEvent.layout.width;
    };

    // highlight-next-line
    useFrameCallback((frameInfo) => {
        const { timeSincePreviousFrame: dt } = frameInfo;
        if (dt == null) {
            return;
        }

        const horse = { ...DEFAULT_HORSE, y: horseY.value };
        const obstacle = {
            ...DEFAULT_OBSTACLE,
            x: obstacleX.value,
        };

        if (
            isColliding(horse, obstacle) ||
            gameOver.value
        ) {
            gameOver.value = true;
            return;
        }

        obstacleX.value =
            obstacleX.value > -100
                ? obstacleX.value - vx.value * dt
                : width.value;

        vx.value += VELOCITY_INCREMENT;
        // highlight-next-line
    });

    const obstacleStyles = useAnimatedStyle(() => ({
        transform: [
            { translateX: obstacleX.value },
            { translateY: DEFAULT_OBSTACLE.y },
        ],
    }));

    const horseStyles = useAnimatedStyle(() => ({
        transform: [
            { translateX: DEFAULT_HORSE.x },
            { translateY: horseY.value },
            { rotateY: '180deg' },
        ],
    }));

    const overlayStyles = useAnimatedStyle(() => ({
        transform: [
            {
                translateY:
                    gameOver.value === true ? 0 : -1000,
            },
        ],
    }));

    const handleTap = () => {
        if (gameOver.value) {
            handleRestart();
        } else {
            handleJump();
        }
    };

    const handleJump = () => {
        if (horseY.value === DEFAULT_Y) {
            horseY.value = withSequence(
                withTiming(DEFAULT_Y - 120, {
                    easing: Easing.bezier(
                        0.3,
                        0.11,
                        0.15,
                        0.97
                    ),
                }),
                withTiming(DEFAULT_Y, {
                    easing: Easing.poly(4),
                })
            );
        }
    };

    const handleRestart = () => {
        gameOver.value = false;
        obstacleX.value = DEFAULT_X;
        horseY.value = DEFAULT_Y;
        vx.value = DEFAULT_VELOCITY;
    };

    return (
        <>
            <Pressable
                style={styles.container}
                onLayout={getDimensions}
                onPressIn={handleTap}
            >
                <Animated.View
                    style={[styles.overlay, overlayStyles]}
                >
                    <Text style={styles.text}>
                        Game Over
                    </Text>
                </Animated.View>
                <Animated.Text
                    style={[
                        styles.obstacle,
                        obstacleStyles,
                    ]}
                >
                    🌵
                </Animated.Text>
                <Animated.Text
                    style={[styles.horse, horseStyles]}
                >
                    🐎
                </Animated.Text>
                <View style={styles.ground} />
            </Pressable>
        </>
    );
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        height: 200,
    },
    horse: {
        position: 'absolute',
        fontSize: 80,
    },
    ground: {
        position: 'absolute',
        right: 0,
        bottom: GROUND_LEVEL - 10,
        width: '100%',
        height: 2,
        backgroundColor: '#000',
    },
    obstacle: {
        position: 'absolute',
        fontSize: 80,
    },
    text: {
        fontSize: 40,
        color: 'white',
        backgroundColor: 'rgba(0,0,0,0.5)',
        paddingHorizontal: 8,
    },
    overlay: {
        justifyContent: 'center',
        alignItems: 'center',
        position: 'absolute',
        width: '100%',
        zIndex: 1,
    },
});

function isColliding(obj1, obj2) {
    'worklet';
    return (
        obj1.x < obj2.x + obj2.width &&
        obj1.x + obj1.width > obj2.x &&
        obj1.y < obj2.y + obj2.height &&
        obj1.y + obj1.height > obj2.y
    );
}
```

## Замечания

-   Функция, переданная в аргументе `callback`, автоматически [workletized](../fundamentals/glossary.md#to-workletize) и запускается на [UI thread](../fundamentals/glossary.md#ui-thread).

## Совместимость с платформами

| Android | iOS | Web |
| ------- | --- | --- |
| ✅      | ✅  | ✅  |

## Ссылки

-   [useFrameCallback](https://docs.swmansion.com/react-native-reanimated/docs/advanced/useFrameCallback)
