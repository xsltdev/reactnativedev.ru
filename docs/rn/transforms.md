---
description: Transforms — это свойства стиля, которые помогут вам изменить внешний вид и положение компонентов с помощью 2D- или 3D-трансформаций
---

# Transforms

**`Transforms`** — это свойства стиля, которые помогут вам изменить внешний вид и положение компонентов с помощью 2D- или 3D-трансформаций. Однако после применения трансформаций макеты остаются неизменными вокруг преобразованного компонента, поэтому он может накладываться на соседние компоненты. Чтобы предотвратить такое перекрытие, можно применить отступ к преобразованному компоненту, соседним компонентам или подкладку к контейнеру.

## Пример {#example}

<div data-snack-id="@bndby/transforms" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

## Методы {#methods}

### transform()

`transform` принимает массив объектов преобразования или разделенных пробелами строковых значений. Каждый объект указывает свойство, которое будет преобразовано в качестве ключа, и значение, которое будет использоваться в преобразовании. Объекты не должны объединяться. Используйте одну пару ключ/значение для каждого объекта.

Для преобразований rotate требуется строка, чтобы преобразование можно было выразить в градусах (deg) или радианах (rad). Например:

```js
transform([
    { rotateX: '45deg' },
    { rotateZ: '0.785398rad' },
]);
```

Того же можно добиться и с помощью строки, разделенной пробелами:

```js
transform('rotateX(45deg) rotateZ(0.785398rad)');
```

Для преобразований перекоса требуется строка, чтобы преобразование можно было выразить в градусах (deg). Например:

```js
transform([{ skewX: '45deg' }]);
```

| Тип                                                                                                                                                                                                                                                                                 | Требуется |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| Массив объектов: {matrix: number[]}, {perspective: number}, {rotate: string}, {rotateX: string}, {rotateY: string}, {rotateZ: string}, {scale: number}, {scaleX: number}, {scaleY: number}, {translateX: number}, {translateY: number}, {skewX: string}, {skewY: string} или string | No        | No |

### decomposedMatrix, rotation, scaleX, scaleY, transformMatrix, translateX, translateY

!!!danger "Устарело"

    Вместо этого используйте пропс [`transform`](transforms.md#transform).
