<!--
 * @Author: yaohebin
 * @Date: 2022-06-17 08:38:55
 * @LastEditTime: 2022-06-29 08:08:18
 * @LastEditors: yaohebin
 * @Description: drag
-->

# drag

> **参考**
>
> [《HTML 拖放 API》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API)
>
> [《拖拽操作》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations)
>
> [《推荐拖动类型》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API/Recommended_drag_types)
>
> [《DragEvent》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/DragEvent)
>
> [《DataTransfer》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer)
>
> [《响应式图片 srcset 全新释义 sizes 属性 w 描述符》(作者：张鑫旭)【来源：http://www.zhangxinxu.com】](https://www.zhangxinxu.com/wordpress/2014/10/responsive-images-srcset-size-w-descriptor/)

[TOC]

HTML 拖放（Drag and Drop）接口使应用程序能够在浏览器中使用拖放功能。例如，用户可使用鼠标选择可拖拽（draggable）元素，将元素拖拽到可放置（droppable）元素，并释放鼠标按钮以放置这些元素。拖拽操作期间，会有一个可拖拽元素的半透明快照跟随着鼠标指针。

## 属性介绍

| 事件      | On 型事件处理程序 | 触发时刻                                                                |
| --------- | ----------------- | ----------------------------------------------------------------------- |
| drag      | ondrag            | 当拖拽元素或选中的文本时触发。                                          |
| dragend   | ondragend         | 当拖拽操作结束时触发 (比如松开鼠标按键或敲“Esc”键)。                    |
| dragenter | ondragenter       | 当拖拽元素或选中的文本到一个可释放目标时触发。                          |
| dragexit  | ondragexit        | 当元素变得不再是拖拽操作的选中目标时触发。                              |
| dragleave | ondragleave       | 当拖拽元素或选中的文本离开一个可释放目标时触发。                        |
| dragover  | ondragover        | 当元素或选中的文本被拖到一个可释放目标上时触发（每 100 毫秒触发一次）。 |
| dragstart | ondragstart       | 当用户开始拖拽一个元素或选中的文本时触发。                              |
| drop      | ondrop            | 当元素或选中的文本在可释放目标上被释放时触发。                          |

## 基础使用

### 可拖拽（draggable）属性

要使其他的 HTML 元素可拖拽，必须做三件事：

1. 将想要拖拽的元素的 `draggable` 属性设置成 `draggable="true"`。
2. 为 `dragstart` 事件添加一个监听程序。
3. 在上一步定义的监听程序中 `设置拖拽数据`。

```html
<p id="p1" draggable="true">This element is draggable.</p>

<script>
  function dragstart_handler(ev) {
    // Add the target element's id to the data transfer object
    ev.dataTransfer.setData('text/plain', ev.target.id);
  }

  window.addEventListener('DOMContentLoaded', () => {
    // Get the element by id
    const element = document.getElementById('p1');
    // Add the ondragstart event listener
    element.addEventListener('dragstart', dragstart_handler);
  });
</script>
```

`draggable` 属性可在任意元素上设置，包括图像和链接。然而，对于后两者，该属性的默认值是 true，所以你只会在禁用这二者的拖拽时使用到 `draggable` 属性，将其设置为 false。

> **注意：** 当一个元素被设置成可拖拽时， 元素中的文本和其他子元素不能再以正常的方式（通过鼠标点击和拖拽）被选中。用户必须按住 `alt` 键，再用鼠标选择文本，或者使用键盘选择。

### 开始拖拽操作

当用户开始拖拽时，会触发 `dragstart` 事件，如上段代码所示，`dragstart` 事件监听程序被添加到可拖拽元素 `<p>`；然而，你可以监听一个祖先元素，因为就像大多数其他事件一样，拖拽事件会冒泡。

在 `dragstart` 事件中，你可以指定**拖拽数据**、**反馈图像**和**拖拽效果**，所有这些都将在下面描述。不过，我们只需要设置拖拽数据，因为在大多数情况下默认的图像和拖拽效果都是适用的。

### 拖拽数据

每个 拖拽事件 都有一个 `dataTransfer` 属性，其中保存着事件的数据。这个属性（DataTransfer 对象）也有管理拖拽数据的方法。

当拖拽发生时，数据必须与被拖拽的项目相关联。例如，当在文本框中拖拽选定的文本时，与拖拽数据项相关联的数据就是文本本身。

拖拽数据 包含两个信息，数据的类型（或格式）和数据值。

1. 格式是一个类型字符串（例如文本数据的格式是 `text/plain`）
2. 值是一个文本字符串。

拖拽开始时，你提供数据类型和数据值。在拖拽过程中，在 `dragenter` 和 `dragover` 事件监听程序中，你使用拖拽数据的类型来检查是否允许放置（drop）。例如，接受链接的放置目标将检查链接类型 `text/uri-list`。在放置事件中，监听程序将取回拖拽数据，并将其插入到放置位置。

一次拖拽可能包括几个不同类型的数据项。这使得数据可以更具体的类型提供，通常是自定义类型，但若放置目标不支持这些具体类型，则会提供回退（fallback）数据。通常情况下，最不具体的类型是 text/plain 类型的普通文本数据，即一些简单的文本表示。

要在 `dataTransfer` 中设置拖拽数据项，使用 `setData()` 方法。这个方法接收两个参数，即数据类型和数据值。例如：

```js
function dragstart_handler(ev) {
  // 添加拖拽数据
  ev.dataTransfer.setData('text/plain', ev.target.innerText);
  ev.dataTransfer.setData('text/html', ev.target.outerHTML);
  ev.dataTransfer.setData('text/uri-list', ev.target.ownerDocument.location.href);
}
```

如果你试图以相同的格式添加两次数据，那么新的数据将替换旧的数据。你可以使用 `clearData()` 方法清除这些数据，该方法接收一个参数，即要删除的数据类型。

```js
event.dataTransfer.clearData('text/uri-list');
```

`clearData()` 方法的 type 参数是可选的。如果没有声明 type，则所有类型的数据都会被删除。如果拖拽不包含拖拽数据项，或者所有的数据项都被清除，那么就不会出现拖拽行为。

### 设置拖拽反馈图像

当拖拽发生时，会生成拖拽目标的一个半透明图像（触发 `dragstart` 事件的元素），并在拖拽过程中跟踪鼠标指针。这个图像是自动创建的，所以你不需要自己创建它。但是，你可以使用 `setDragImage()` 方法来自定义拖拽反馈图像。

设置格式为：

```js
event.dataTransfer.setDragImage(image, xOffset, yOffset);
```

这三个参数都是必要的。

1. 第一个是图像的引用。这个引用通常是一个 `<img>` 元素，但也可以是 `<canvas>` 或任何其他元素。生成的反馈图像就是该图像在屏幕上的样子，以图像原始的大小绘制。
2. 第二、三个参数是图像位置相对于鼠标指针位置的偏移量。

示例：

```js
function dragstart_handler(ev) {
  // Create an image and then use it for the drag image.
  // NOTE: change "example.gif" to a real image URL or the image
  // will not be created and the default drag image will be used.
  var img = new Image();
  img.src = 'example.gif';
  ev.dataTransfer.setDragImage(img, 10, 10);
}

/* canvas */

function dragWithCustomImage(event) {
  var canvas = document.createElementNS('http://www.w3.org/1999/xhtml', 'canvas');
  canvas.width = canvas.height = 50;

  var ctx = canvas.getContext('2d');
  ctx.lineWidth = 4;
  ctx.moveTo(0, 0);
  ctx.lineTo(50, 50);
  ctx.moveTo(0, 50);
  ctx.lineTo(50, 0);
  ctx.stroke();

  var dt = event.dataTransfer;
  dt.setData('text/plain', 'Data to Drag');
  dt.setDragImage(canvas, 25, 25);
}
```

### 拖拽效果

你可以在 `dragstart` 事件监听程序中设置 `effectAllowed` 属性以指定允许拖拽源头执行三种操作中的哪几种。

可以不同的方式组合这些值：

- `none` 不允许才走哦
- `copy` 只复制
- `move` 只移动
- `link` 只链接
- `copyMove` 复制或移动
- `copyLink` 复制或链接
- `linkMove` 链接或移动
- `all` 复制、移动或链接

```js
function dragstart_handler(ev) {
  ev.dataTransfer.effectAllowed = 'copy';
}
```

**注意**，这些值必须像上面列出的那样使用。例如，将 `effectAllowed` 属性设置为 `copyMove` 允许复制或移动操作，但阻止用户执行链接操作。属性默认允许以上所有的操作（`all`），所以你不需要调整这个属性，除非你想要排除某个特定操作。

在拖拽操作期间，`dragenter` 或 `dragover` 事件的监听程序可以检查 `effectAllowed` 属性，以查看哪些操作是允许的。

相关的 `dropEffect` 属性应该在其中的一个事件中设置，来指定应该执行哪一个单项操作。`dropEffect` 是 `none, copy, move, 或 link`。这个属性不使用上述的组合值。

你可以在 `dragenter` 或 `dragover` 事件期间修改 `dropEffect` 属性，例如将某个放置目标设置为只支持某些操作。你可以修改 `dropEffect` 属性来覆盖用户指定的效果，并强制修改为一个特定的放置操作。 注意，这个效果必须是 `effectAllowed` 属性中的一个。否则，它将被设置为允许的替代值。

在拖拽过程中，拖拽效果也许会被修改以用于表明在具体位置上具体效果是否被允许，如果允许，在该位置则被允许放置。

```js
function dragstart_handler(ev) {
  ev.dataTransfer.dropEffect = 'copy';
}
```

### 指定放置目标

`dragenter` 或 `dragover` 事件的监听程序用于表示有效的放置目标，也就是被拖拽项目可能放置的地方。网页或应用程序的大多数区域都不是放置数据的有效位置。因此，这些事件的默认处理是不允许放置。

如果你想要允许放置，你必须取消 `dragenter` 和 `dragover` 事件来阻止默认的处理。你可以在属性定义的事件监听程序返回 false，或者调用事件的 `preventDefault()` 方法来实现这一点。在一个独立脚本中的定义的函数里，可能后者更可行。

```html
<p id="target" ondrop="drop_handler(event)" ondragover="dragover_handler(event)">Drop Zone</p>

<script>
  function dragover_handler(ev) {
    ev.preventDefault();
    ev.dataTransfer.dropEffect = 'move';
  }
  function drop_handler(ev) {
    ev.preventDefault();
    // Get the id of the target and add the moved element to the target's DOM
    var data = ev.dataTransfer.getData('text/plain');
    ev.target.appendChild(document.getElementById(data));
  }
</script>
```

**注意：每个处理程序调用 `preventDefault()` 来阻止对这个事件的其它处理过程（如触点事件或指针事件）。**

### 放置反馈

有几种方法可以向用户表明哪个位置允许放置。鼠标指针将根据 `dropEffect` 属性的值做必要的更新。鼠标指针具体的外观取决于用户平台，典型的如加号图标会出现在 `copy` 中，而不允许放置时，会出现禁止放置的图标。在许多情况下，鼠标指针反馈就足够了。

但是，你还可以根据需要更新用户界面，如添加一个插入标记或使用高亮显示。对于简单的高亮显示，你可以在放置目标上使用 -moz-drag-over CSS 伪类。

```css
.droparea:-moz-drag-over {
  border: 1px solid black;
}
```

> **注意**：要使这个伪类生效，你必须在 `dragenter` 事件中调用 `preventDefault()` 方法，因为这个伪类状态不会检查 `dragover` 事件（即在 dragover (en-US) 事件中调用 preventDefault() 方法也不会使伪类生效，尽管这个伪类叫做 “`-moz-drag-over`”）。

对于更复杂的视觉效果，你可以在 `dragenter` 事件中执行其他操作。例如在放置位置插入一个元素，这样的元素可以表示一个插入标记，或表示被拖拽的元素移动到了新位置。为此你可以在 `dragenter` 事件中创建一个新元素，然后将其插入到文档中。

`dragover` 事件在鼠标指向的元素上触发。自然，你可能需要将插入标记移动到事件发生的位置附近。你可以使用事件的 clientX 和 clientY 属性，还有其他鼠标事件的属性来确定鼠标的位置。

最后，`dragleave` 事件会在拖拽离开元素时在该元素上触发。这是移除插入标记或高亮的好时机。你不需要取消这个事件（即不需要使用 `preventDefault()` ）。使用 `-moz-drag-over` 伪类设置的高亮或其他视觉效果会被自动移除。即使拖拽被取消了，`dragleave` 事件也会照常触发，所以你可以确保在这个事件中对任何插入标记的清除操作都一定可以完成。

### 执行放置

当用户放开鼠标，拖放操作就会结束。

如果在有效的放置目标元素（即取消了 `dragenter` 或 `dragover` 的元素）上放开鼠标，放置会成功实现，`drop` 事件在目标元素上被触发。否则，拖拽会被取消，不会触发 `drop` 事件。

在 `drop` 事件中，你应该取回放置的数据并将其插入到放置的位置。你可以使用 `dropEffect` 属性来确定需要哪种拖拽操作。

在所有拖拽操作相关的事件中，事件的 `dataTransfer` 属性会一直保存着拖拽数据。可使用 `getData()` 方法来取回数据。

下面的例子展示了一个处理程序，从拖拽数据中获取事件源元素的 id 然后根据 id 移动源元素到目标元素：

```js
function drop_handler(ev) {
  ev.preventDefault();
  // Get the id of the target and add the moved element to the target's DOM
  var data = ev.dataTransfer.getData('application/my-app');
  ev.target.appendChild(document.getElementById(data));
}
```

`getData()` 方法接收一个参数，取回数据的类型。这个方法会返回拖拽操作开始时调用 `setData()` 方法设置的字符串值。如果不存在传入类型的数据，则会返回空字符串。自然，你应该知道哪种类型的数据可用，因为之前你应该已经在 `dragover` 事件中检查过数据类型了。

### 完成拖拽

一旦拖拽完成，`dragend` 事件会在拖拽源头（即触发 `dragstart` 的元素）上发生。无论拖拽是成功还是被取消，这个事件都会被触发。然而，你可以使用 `dropEffect` 属性来决定执行什么放置操作。

如果在 `dragend` 事件中，`dropEffect` 属性值为 `none`，则拖拽会被取消。否则，这个属性会规定需要执行什么操作。源头元素可使用这个信息以在拖拽操作完成后从原来的位置移除被拖拽的项目。`mozUserCancelled` 属性会在用户取消拖拽（按下 Esc 键）时设置为 true，在拖拽因为其他原因如无效放置目标等被取消时，或拖拽成功时，则设置为 false。

放置可发生在同一窗口或另一个应用程序中。两种情况都会触发 `dragend` 事件。事件的 `screenX` 和 `screenY` 属性会被设置为放置发生时鼠标在屏幕上的坐标。
