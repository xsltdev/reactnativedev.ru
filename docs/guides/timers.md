# Таймеры

Таймеры являются важной частью приложения, и в React Native реализованы [браузерные таймеры](https://developer.mozilla.org/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals).

## Таймеры

-   `setTimeout`, `clearTimeout`
-   `setInterval`, `clearInterval`
-   `setImmediate`, `clearImmediate`
-   `requestAnimationFrame`, `cancelAnimationFrame`

`requestAnimationFrame(fn)` - это не то же самое, что `setTimeout(fn, 0)` - первый сработает после того, как все кадры будут смыты, тогда как второй сработает настолько быстро, насколько это возможно (более 1000 раз в секунду на iPhone 5S).

Функция `setImmediate` выполняется в конце текущего блока выполнения JavaScript, непосредственно перед отправкой пакетного ответа обратно в native. Обратите внимание, что если вы вызываете `setImmediate` внутри обратного вызова `setImmediate`, то он будет выполнен сразу же, и не будет передан обратно в native в промежутке.

Реализация `Promise` использует `setImmediate` в качестве реализации асинхронности.

## InteractionManager

Одна из причин, по которой хорошо сделанные нативные приложения работают так плавно, - это избегание дорогостоящих операций во время взаимодействия и анимации. В React Native в настоящее время существует ограничение, что существует только один поток выполнения JS, но вы можете использовать `InteractionManager`, чтобы убедиться, что длительная работа запланирована на запуск после завершения любых взаимодействий/анимаций.

Приложения могут планировать выполнение задач после взаимодействия с помощью следующего:

```ts
InteractionManager.runAfterInteractions(() => {
    // ...long-running synchronous task...
});
```

Сравните это с другими альтернативами планирования:

-   `requestAnimationFrame()`: для кода, который анимирует представление во времени.
-   `setImmediate`/`setTimeout`/`setInterval()`: запуск кода позже, обратите внимание, что это может задержать анимацию.
-   `runAfterInteractions()`: запуск кода позже, без задержки активных анимаций.

Система обработки касаний рассматривает одно или несколько активных касаний как "взаимодействие" и откладывает вызов `runAfterInteractions()` до тех пор, пока все касания не закончатся или не будут отменены.

`InteractionManager` также позволяет приложениям регистрировать анимации, создавая "хэндл" взаимодействия при запуске анимации и очищая его по завершении:

```ts
const handle = InteractionManager.createInteractionHandle();
// run animation... (`runAfterInteractions` tasks are queued)
// later, on animation completion:
InteractionManager.clearInteractionHandle(handle);
// queued tasks run if all handles were cleared
```
