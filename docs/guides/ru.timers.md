

---
id: timers
title: Timers
---

Таймеры являются важной частью приложения, и в React Native реализованы [браузерные таймеры](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals).



## Таймеры




- setTimeout, clearTimeout

- setInterval, clearInterval

- setImmediate, clearImmediate

- requestAnimationFrame, cancelAnimationFrame




`requestAnimationFrame(fn)` - это не то же самое, что `setTimeout(fn, 0)` - первый сработает после того, как все кадры будут смыты, тогда как второй сработает настолько быстро, насколько это возможно (более 1000 раз в секунду на iPhone 5S).




Функция `setImmediate` выполняется в конце текущего блока выполнения JavaScript, непосредственно перед отправкой пакетного ответа обратно в native. Обратите внимание, что если вы вызываете `setImmediate` внутри обратного вызова `setImmediate`, то он будет выполнен сразу же, и не будет передан обратно в native в промежутке.




Реализация `Promise` использует `setImmediate` в качестве реализации асинхронности.




:::примечание

При отладке на Android, если время между отладчиком и устройством изменилось, такие вещи, как анимация, поведение событий и т.д., могут работать неправильно или результаты могут быть неточными.

Пожалуйста, исправьте это, запустив `` adb shell "date ``date +%m%d%H%M%Y.%S%3N``" `` на вашей машине с отладчиком. Для использования на реальном устройстве требуется Root-доступ.

:::




## InteractionManager




Одна из причин, по которой хорошо сделанные нативные приложения работают так плавно, - это избегание дорогостоящих операций во время взаимодействия и анимации. В React Native в настоящее время существует ограничение, что существует только один поток выполнения JS, но вы можете использовать `InteractionManager`, чтобы убедиться, что длительная работа запланирована на запуск после завершения любых взаимодействий/анимаций.




Приложения могут планировать выполнение задач после взаимодействия с помощью следующего:

```tsx
InteractionManager.runAfterInteractions(() => {
  // ...long-running synchronous task...
});
```

Compare this to other scheduling alternatives:

- requestAnimationFrame(): for code that animates a view over time.
- setImmediate/setTimeout/setInterval(): run code later, note this may delay animations.
- runAfterInteractions(): run code later, without delaying active animations.

The touch handling system considers one or more active touches to be an 'interaction' and will delay `runAfterInteractions()` callbacks until all touches have ended or been cancelled.

InteractionManager also allows applications to register animations by creating an interaction 'handle' on animation start, and clearing it upon completion:

```tsx
const handle = InteractionManager.createInteractionHandle();
// run animation... (`runAfterInteractions` tasks are queued)
// later, on animation completion:
InteractionManager.clearInteractionHandle(handle);
// queued tasks run if all handles were cleared
```

