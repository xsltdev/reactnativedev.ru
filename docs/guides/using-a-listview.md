# Использование представлений списков

React Native предоставляет набор компонентов для представления списков данных. Как правило, вы захотите использовать либо [`FlatList`](../components/flatlist.md), либо [`SectionList`](../components/sectionlist.md).

Компонент `FlatList` отображает прокручивающийся список изменяющихся, но одинаково структурированных данных. `FlatList` хорошо подходит для длинных списков данных, в которых количество элементов может меняться с течением времени. В отличие от более общего [`ScrollView`](using-a-scrollview.md), `FlatList` отображает только элементы, которые в данный момент отображаются на экране, а не все элементы сразу.

Компонент `FlatList` требует два пропса: `data` и `renderItem`. `data` является источником информации для списка. `renderItem` берет один элемент из источника и возвращает отформатированный компонент для рендеринга.

Этот пример создает базовый `FlatList` из жестко закодированных данных. Каждый элемент в пропсе `data` отображается как компонент `Text`. Компонент `FlatListBasics` затем рендерит `FlatList` и все компоненты `Text`.

<div data-snack-id="@bndby/flatlist-basics" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Если вы хотите отобразить набор данных, разбитых на логические секции, возможно, с заголовками секций, подобно `UITableView` на iOS, то вам подойдет [`SectionList`](../components/sectionlist.md).

<div data-snack-id="@bndby/sectionlist-basics" data-snack-platform="web" data-snack-preview="true" data-snack-theme="light" style="overflow:hidden;background:#F9F9F9;border:1px solid var(--color-border);border-radius:4px;height:505px;width:100%"></div>

Одно из самых распространенных применений представления списка — отображение данных, которые вы получаете с сервера. Для этого вам понадобится [узнать о сетевом взаимодействии в React Native](network.md).
