# Прямое управление

Иногда бывает необходимо внести изменения непосредственно в компонент, не используя state/props для запуска повторного рендеринга всего поддерева. Например, при использовании React в браузере иногда требуется напрямую изменить узел DOM, то же самое справедливо и для представлений в мобильных приложениях. `setNativeProps` — это эквивалент React Native для установки свойств непосредственно на узле DOM.

!!!warning ""

    Используйте `setNativeProps`, когда частые повторные рендеринги создают узкое место в производительности!

    Прямые манипуляции не будут тем инструментом, к которому вы часто обращаетесь. Как правило, вы будете использовать его только для создания непрерывной анимации, чтобы избежать накладных расходов на рендеринг иерархии компонентов и согласование множества представлений.

    `setNativeProps` является императивным и хранит состояние в нативном слое (DOM, UIView и т.д.), а не внутри ваших компонентов React, что делает ваш код более сложным для рассуждений.

    Прежде чем использовать его, попробуйте решить свою проблему с помощью `setState` и [`shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action).

## setNativeProps с TouchableOpacity

[TouchableOpacity](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/Touchable/TouchableOpacity.js) использует `setNativeProps` для обновления непрозрачности своего дочернего компонента:

```ts
const viewRef = useRef<View>();
const setOpacityTo = useCallback((value) => {
    // Redacted: animation related code
    viewRef.current.setNativeProps({
        opacity: value,
    });
}, []);
```

Это позволяет нам написать следующий код и знать, что непрозрачность дочернего объекта будет обновляться в ответ на касания, при этом сам объект не будет знать об этом факте и не потребует изменений в своей реализации:

```ts
<TouchableOpacity onPress={handlePress}>
    <View>
        <Text>Press me!</Text>
    </View>
</TouchableOpacity>
```

Представим, что функция `setNativeProps` недоступна. Один из способов, который мы могли бы реализовать с таким ограничением — хранить значение непрозрачности в состоянии, а затем обновлять это значение при каждом срабатывании `onPress`:

```ts
const [buttonOpacity, setButtonOpacity] = useState(1);
return (
    <TouchableOpacity
        onPressIn={() => setButtonOpacity(0.5)}
        onPressOut={() => setButtonOpacity(1)}
    >
        <View style={{ opacity: buttonOpacity }}>
            <Text>Press me!</Text>
        </View>
    </TouchableOpacity>
);
```

Это требует больших вычислительных затрат по сравнению с исходным примером — React необходимо перерисовывать иерархию компонентов каждый раз, когда меняется непрозрачность, даже если другие свойства представления и его дочерних элементов не изменились. Обычно эти накладные расходы не вызывают беспокойства, но при выполнении непрерывной анимации и реагировании на жесты разумная оптимизация компонентов может улучшить точность анимации.

Если вы посмотрите на реализацию `setNativeProps` в [NativeMethodsMixin](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Renderer/implementations/ReactNativeRenderer-prod.js), то заметите, что она является оберткой вокруг `RCTUIManager.updateView` — это точно такой же вызов функции, который происходит в результате повторного рендеринга — см. [receiveComponent в ReactNativeBaseComponent](https://github.com/facebook/react-native/blob/fb2ec1ea47c53c2e7b873acb1cb46192ac74274e/Libraries/Renderer/oss/ReactNativeRenderer-prod.js#L5793-L5813).

## Составные компоненты и setNativeProps

Составные компоненты не поддерживаются нативным представлением, поэтому вы не можете вызвать `setNativeProps` для них. Рассмотрим этот пример:

=== "TypeScript"

    <div data-snack-id="@bndby/ts-setnativeprops-with-composite-components" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-setnativeprops-with-composite-components" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Если вы запустите его, то сразу же увидите ошибку: `Дочерний элемент должен быть либо родным, либо передавать setNativeProps родному компоненту`. Это происходит потому, что `MyButton` не поддерживается непосредственно нативным представлением, чья непрозрачность должна быть установлена. Об этом можно подумать следующим образом: если вы определяете компонент с помощью `createReactClass`, вы не ожидаете, что сможете установить для него style prop и это сработает — вам нужно будет передать style prop дочернему компоненту, если только вы не обертываете родной компонент. Аналогично, мы собираемся передать `setNativeProps` дочернему компоненту с поддержкой native.

### Передача setNativeProps дочернему компоненту

Поскольку метод `setNativeProps` существует для любой ссылки на компонент `View`, достаточно переслать ссылку на ваш пользовательский компонент на один из компонентов `<View />`, которые он рендерит. Это означает, что вызов `setNativeProps` на пользовательском компоненте будет иметь такой же эффект, как если бы вы вызвали `setNativeProps` на самом обернутом компоненте `View`.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-forwarding-setnativeprops" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-forwarding-setnativeprops" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Теперь вы можете использовать `MyButton` внутри `TouchableOpacity`!

Возможно, вы заметили, что мы передали все реквизиты дочернему представлению с помощью `{...props}`. Причина этого в том, что `TouchableOpacity` на самом деле является составным компонентом, и поэтому в дополнение к зависимости от `setNativeProps` для своего дочернего компонента, он также требует, чтобы дочерний компонент выполнял обработку касаний. Для этого он передает [различные реквизиты](../components/view.md#onmoveshouldsetresponder), которые вызываются обратно к компоненту `TouchableOpacity`. `TouchableHighlight`, напротив, поддерживается родным представлением и требует только реализации `setNativeProps`.

## setNativeProps для редактирования значения TextInput

Еще один очень распространенный случай использования `setNativeProps` — это редактирование значения TextInput. Свойство `controlled` для TextInput иногда может пропустить символы, когда `bufferDelay` мал и пользователь набирает текст очень быстро. Некоторые разработчики предпочитают полностью отказаться от этого свойства и вместо этого используют `setNativeProps` для прямого манипулирования значением TextInput, когда это необходимо. Например, следующий код демонстрирует редактирование ввода при нажатии на кнопку:

=== "TypeScript"

    <div data-snack-id="@bndby/ts-clear-text" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-clear-text" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Вы можете использовать метод [`clear`](../components/textinput.md#clear) для очистки `TextInput`, который очищает текущий текст ввода, используя тот же подход.

## Избегание конфликтов с функцией рендеринга

Если вы обновляете свойство, которое также управляется функцией рендеринга, вы можете столкнуться с непредсказуемыми и запутанными ошибками, потому что при любом повторном рендеринге компонента и изменении этого свойства, значение, которое было ранее установлено с помощью `setNativeProps`, будет полностью проигнорировано и отменено.

## setNativeProps & shouldComponentUpdate

Путем [разумного применения `shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#avoid-reconciliation) вы можете избежать ненужных накладных расходов, связанных с согласованием неизмененных поддеревьев компонентов, до такой степени, что может оказаться достаточно эффективным использовать `setState` вместо `setNativeProps`.

## Другие нативные методы

Описанные здесь методы доступны для большинства компонентов по умолчанию, предоставляемых React Native. Обратите внимание, однако, что они _не_ доступны для составных компонентов, которые не поддерживаются непосредственно нативным представлением. Это, как правило, включает большинство компонентов, которые вы определяете в своем собственном приложении.

### measure(callback)

Определяет местоположение на экране, ширину и высоту в области просмотра данного представления и возвращает значения через асинхронный обратный вызов. В случае успеха обратный вызов будет вызван со следующими аргументами:

-   `x`
-   `y`
-   `width`
-   `height`
-   `pageX`
-   `pageY`

Обратите внимание, что эти измерения доступны только после завершения рендеринга в native. Если вам нужны измерения как можно скорее и вам не нужны `pageX` и `pageY`, подумайте об использовании свойства [`onLayout`](../components/view.md#onlayout) вместо этого.

Также ширина и высота, возвращаемые `measure()`, являются шириной и высотой компонента в области просмотра. Если вам нужен фактический размер компонента, используйте свойство [`onLayout`](../components/view.md#onlayout).

### measureInWindow(callback)

Определяет местоположение заданного представления в окне и возвращает значения через async-обратный вызов. Если корневое представление React встроено в другое нативное представление, это даст вам абсолютные координаты. В случае успеха обратный вызов будет вызван со следующими аргументами:

-   `x`
-   `y`
-   `width`
-   `height`

### measureLayout(relativeToNativeComponentRef, onSuccess, onFail)

Аналогично `measure()`, но измеряет вид относительно предка, указанного ссылкой `relativeToNativeComponentRef`. Это означает, что возвращаемые координаты относятся к началу координат `x`, `y` вида-предка.

!!!note ""

    Этот метод также может быть вызван с обработчиком `relativeToNativeNode` (вместо ссылки), но этот вариант устарел.

=== "TypeScript"

    <div data-snack-id="@bndby/ts-measurelayout-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

=== "JavaScript"

    <div data-snack-id="@bndby/js-measurelayout-example" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

### focus()

Запрашивает фокус для заданного ввода или представления. Точное поведение будет зависеть от платформы и типа представления.

### blur()

Убирает фокус с ввода или представления. Это противоположность `focus()`.
