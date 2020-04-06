---
title: CSS 拷问：水平垂直居中方法你会几种？
date: 2020-04-07 01:04:48
tags:
  - CSS
  - CSS 布局
categories:
  - CSS 基础
---

CSS 实现水平垂直居中，这是一道经典的面试题，也是我们平时开发经常遇见的问题。本文总结了常用的方法，以及各种奇淫巧技，并且会注明每种方法的兼容性。

<!-- more -->

开始正文之前，我们准备一些元素：

```html
<div class="outer">
  <div class="inner">hello world</div>
</div>
```

为了便于查看，加上一些样式：

```css
.outer {
  width: 300px;
  height: 300px;
  border: 1px solid #999;
}

.inner {
  width: 100px;
  height: 100px;
  border: 1px solid #999;
}
```

**上面这些代码会在下文中复用，并且不再提及，请注意记忆。**

## 水平垂直居中

下面是水平和垂直居中都适用的方法：

1. Flex 方案
2. Grid 方案
3. `absolute` + `transform`
4. `absolute` + `calc`
5. `absolute` + 负 `margin`
6. `absolute` + `margin: auto`
7. `writing-mode`

### Flex 方案

```css
/* 这里引用复用代码 */

.outer {
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center; /* 垂直居中 */
}
```

该方案的另一种用法：

```css
/* 这里引用复用代码 */

.outer {
  display: flex;
}

.inner {
  margin: auto;
}
```

目前在移动端已经完全可以使用 Flex 布局了，PC 端需要看自己业务的兼容性情况。

优点：不需要固定居中元素的宽高。

兼容性：

- IE 10+, Chrome 4+, Firefox 2+
- Android 2.3+, iOS 6+

### Grid 方案

```css
/* 这里引用复用代码 */

.outer {
  display: grid;
}

.inner {
  justify-self: center; /* 水平居中 */
  align-self: center; /* 垂直居中 */
}
```

该方案的另一种使用方法：

```css
/* 这里引用复用代码 */

.outer {
  display: grid;
}

.inner {
  margin: auto;
}
```

该方案的兼容性比 Flex 差一点，同样的根据自己业务的兼容情况选择使用。

优点：不需要固定居中元素的宽高。

兼容性：

- IE 10+, Chrome 57+, Firefox 52+
- Android 6+, iOS 10.3+

### absolute + transform

CSS3 中新增的 `transform`，其 `translate` 属性是根据元素自身计算的。例如：设置 `transform: translateX(-50%);`，元素会向左偏移自身宽度的一半。根据这一特性，很容易实现元素的居中：

```css
/* 这里引用复用代码 */

.outer {
  position: relative;
}

.inner {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
```

优点：不需要固定居中元素的宽高。

兼容性：

- IE 9+, Chrome 4+, Firefox 3.5+
- Android 3+, iOS 6+

### absolute + calc

```css
/* 这里引用复用代码 */

.outer {
  position: relative;
}

.inner {
  position: absolute;
  left: calc(50% - 50px);
  top: calc(50% - 50px);
}
```

缺点：需要固定居中元素的宽高。

兼容性：

- IE 9+, Chrome 19+, Firefox 4+
- Android 4.4+, iOS 6+

### absolute + 负 margin

```css
/* 这里引用复用代码 */

.outer {
  position: relative;
}

.inner {
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
}
```

缺点：需要固定居中元素的宽高。

兼容性：

- IE 6+, Chrome 4+, Firefox 2+
- Android 2.3+, iOS 6+

### absolute + margin: auto

```css
/* 这里引用复用代码 */

.outer {
  position: relative;
}

.inner {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
}
```

该方案的原理是：使用了 CSS 中的定位属性（`absolute`、`fixed` 等）后，如果 `left` 设置了具体值，没有设置 `right` 和 `width`，那么就会自动计算，把剩余的空间分配给 `right` 和 `width`。如果 `left`、`right` 和 `width` 都设置了具体值，并且没有占满横向空间，那么剩余空间就处于待分配状态，此时设置 `margin: auto;` 意味着把剩余的空间分配给 `margin`，并且左右均分，所以就实现了水平居中，垂直方向同理。

但是要知道该方法的副作用：

- `left: 0; right: 0;` 相当于 `width: 100%;`
- `top: 0; bottom: 0;` 相当于 `height: 100%;`

缺点：需要固定居中元素的宽高，否则其宽高会被设为 `100%`。

兼容性：

- IE 6+, Chrome 4+, Firefox 2+
- Android 2.3+, iOS 6+

### writing-mode

`writing-mode` 可以改变文字显示方向。例如，将文字显示为垂直方向：

```html
<div>垂直显示</div>
```

```css
div {
  writing-mode: vertical-lr;
}
```

显示效果如下：

```
垂
直
显
示
```

该属性最神奇的地方在于，不仅可以使文字显示方向改变，还能使所有作用于水平方向上的属性，变成作用于垂直方向。所以，通过 `writing-mode` 和 `text-align: center` 就可以实现垂直居中了。

这里，我们需要改造下复用代码中的 DOM 结构：

```html
<div class="outer">
  <div class="inner">
    <div class="content"></div>
  </div>
</div>
```

然后，该方案的核心代码如下：

```css
/* 这里引用复用代码 */

.outer {
  writing-mode: vertical-lr;
  text-align: center;
}

.inner {
  display: inline-block;
  width: 100%;
  height: auto;
  writing-mode: horizontal-tb;
  text-align: center;
}

.content {
  display: inline-block;
  text-align: left; /* 重置文字位置（如果需要） */
}
```

优点：不需要固定居中元素的宽高。

兼容性：

- IE 6+, Chrome 4+, Firefox 3.5+
- Android 2.3+, iOS 5.1+

## 水平居中

下面介绍只适用于水平居中的方案：

1. `margin: auto`
2. `text-align` + `inline-block`
3. `fit-content` + `margin`

### margin: auto

```css
/* 这里引用复用代码 */

.inner {
  display: block; /* 还可以是 table | flex | grid，但不能是 inline-xxx */
  margin: 0 auto;
}
```

该方案的原理是：元素被设置成块级后，会独占一行，如果其宽度没有占满横向空间，那么剩余的空间就处于待分配状态，此时设置 `margin: auto;` 意味着把剩余的空间分配给 `margin`，并且左右均分，所以就实现了水平居中。

缺点：需要固定居中元素的宽。

### text-align + inline-block

```css
/* 这里引用复用代码 */

.outer {
  text-align: center;
}

.inner {
  display: inline-block;
  text-align: left; /* 重置文字位置（如果需要） */
}
```

该方案的原理是：上面代码中的 `text-align: center;` 会使文本居中，但是对元素无效，如果将元素设置为 `inline-block`，就会使元素被当做文本对待，从而实现元素居中。

缺点：为了居中元素，使文本也居中了，因此可能需要重置文本位置。

优点：不需要固定居中元素的宽。

### fit-content + margin

该方案用法有两种，一种是直接应用于元素上，另一种是应用于元素的父元素上。

- 直接应用于元素上

  ```css
  /* 这里引用复用代码 */

  .inner {
    width: fit-content;
    margin: auto;
  }
  ```

  缺点：会使元素的宽度改变（同内容宽度）。

- 应用于元素的父元素上

  ```css
  /* 这里引用复用代码 */

  .outer {
    width: fit-content;
    margin: auto;
  }
  ```

  优点：即使子元素是浮动元素也适用。

由于目前 `fit-content` 属性兼容性较差，因此该方案不推荐使用。

## 垂直居中

下面介绍只适用于垂直居中的方案：

1. `table-cell` + `vertical-align`
2. `inline-block` + `line-height`
3. `inline-block` + `vertical-align`

### table-cell + vertical-align

```css
/* 这里引用复用代码 */

.outer {
  display: table-cell;
  vertical-align: middle;
}
```

优点：不需要固定居中元素的高。

该方案可配合水平居中的方案 1、2，实现水平垂直居中，如下所示：

- 配合水平居中方案 1

  ```css
  /* 这里引用复用代码 */

  .outer {
    display: table-cell;
    vertical-align: middle;
  }

  .inner {
    display: block; /* 还可以是 table | flex | grid，但不能是 inline-xxx */
    margin: 0 auto;
  }
  ```

  缺点：需要固定居中元素的宽。

  优点：不需要固定居中元素的高。

- 配合水平居中方案 2

  ```css
  /* 这里引用复用代码 */

  .outer {
    display: table-cell;
    vertical-align: middle;

    text-align: center;
  }

  .inner {
    display: inline-block;

    text-align: left; /* 重置文字位置（如果需要） */
  }
  ```

  优点：不需要固定居中元素的宽高。

### inline-block + line-height

```css
/* 这里引用复用代码 */

.outer {
  line-height: 300px;
}

.inner {
  line-height: initial; /* 重置 */
  vertical-align: middle;
  display: inline-block;
}
```

众所周知，元素的 `height` 和 `line-height` 相同的时候，会使其文本内容垂直居中。因此该方案利用了这一特点，不过文本内容虽然垂直居中了，但是元素并没有，因此再将元素设置为 `display: inline-block;`，这样元素就会被当做文本对待了。同时由于具有了 `inline` 属性，`vertical-align: middle;` 也就可以生效了。

缺点：需要知道其父元素高度。

优点：不需要固定居中元素的高。

该方案可配合水平居中的方案 2，实现水平垂直居中，如下所示：

```css
/* 这里引用复用代码 */

.outer {
  line-height: 300px;

  text-align: center;
}

.inner {
  line-height: initial; /* 重置行高 */
  vertical-align: middle;
  display: inline-block;

  text-align: left; /* 重置文字位置（如果需要） */
}
```

缺点：需要知道其父元素高度。

优点：不需要固定居中元素的宽高。

### inline-block + vertical-align

```css
/* 这里引用复用代码 */

.outer::after {
  content: "";
  display: inline-block;
  vertical-align: middle;
  height: 100%;
}

.inner {
  display: inline-block;
  vertical-align: middle;
}
```

该方案的原理是：在居中元素的父元素上，使用一个伪元素，将这个伪元素设置为 `inline-block` 后，就好像它原来就是一个真正的 DOM 元素，存在于页面上。然后再将居中元素设置为 `inline-block`，根据 `inline-block` 的特性（多个相邻的 `inline-block` 元素会横向并排显示），居中元素会和伪元素横向并排显示。并且设置 `vertical-align: middle;` 后，它们会互相垂直对齐，最后将伪元素高度撑起来，居中元素会对齐伪元素，从而实现了垂直居中。

优点：不需要固定居中元素的高。

该方案可配合水平居中的方案 2，实现水平垂直居中，如下所示：

```css
/* 这里引用复用代码 */

.outer {
  text-align: center;
}

.outer::after {
  content: "";
  display: inline-block;
  vertical-align: middle;
  height: 100%;
}

.inner {
  display: inline-block;
  vertical-align: middle;

  text-align: left; /* 重置文字位置（如果需要） */
}
```

优点：不需要固定居中元素的宽高。

## 文章小结

本文介绍的方法中，除了 `fit-content` 方案和 `Grid` 方案兼容性较差，其他的方案都可以在实际项目中使用。每种方案都有其应用场景，应该根据实际业务灵活运用，而不是 Flex 一把梭，香的一批。
