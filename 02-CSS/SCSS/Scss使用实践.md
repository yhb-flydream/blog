# Scss 使用实践

Scss 作为 CSS 的预处理器，能完全兼容所有版本的 CSS，且可以设置变量，声明mixin，使用循环，自定义function函数等，对 CSS 进行扩展，使其拥有更多的功能和特性。本文列举了一些在项目中使用的一些实践。

[Scss](https://www.sasscss.com/) 作为 CSS 的预处理器，能完全兼容所有版本的 CSS，且可以设置**变量**，声明**mixin**，使用**for from through/to**、**each**、**while**循环，自定义**function**函数，**extend**复用样式，**use**规则，**color**、**math**等，对 CSS 进行扩展，使其拥有更多的功能和特性。

本文列举了一些在项目中使用的一些实践。

```scss
/* 变量 */
$main-color: #000000;
$gray-color: #999999;

/* use 规则 */
/* math 模块 */
@use 'sass:math';

/* while */
/* each */
// margin, padding
$number: 50px;
$dir: top, right, bottom, left;
@while $number >= 5px {
  // $n: calc($number / 1px);
  $n: math.div($number, 1px);

  @each $item in $dir {
    .margin-#{$item}-#{$n} {
      margin-#{$item}: $number;
    }
    .padding-#{$item}-#{$n} {
      padding-#{$item}: $number;
    }
  }
  $number: $number - 5px;
}
```

上面使用 `$` 符号声明变量 `$main-color: #000000;`，并使用 `while`、`each` 循环，可以快速生成一些有规律的 CSS 规则。

这里设置了一些 margin、padding 的预制规则。

转译之后的 CSS 为：

```css
.margin-top-50 {
  margin-top: 50px;
}

.padding-top-50 {
  padding-top: 50px;
}

.margin-right-50 {
  margin-right: 50px;
}

.padding-right-50 {
  padding-right: 50px;
}

.margin-bottom-50 {
  margin-bottom: 50px;
}

.padding-bottom-50 {
  padding-bottom: 50px;
}

.margin-left-50 {
  margin-left: 50px;
}

.padding-left-50 {
  padding-left: 50px;
}

.margin-top-45 {
  margin-top: 45px;
}

.padding-top-45 {
  padding-top: 45px;
}

.margin-right-45 {
  margin-right: 45px;
}

.padding-right-45 {
  padding-right: 45px;
}

.margin-bottom-45 {
  margin-bottom: 45px;
}

.padding-bottom-45 {
  padding-bottom: 45px;
}

.margin-left-45 {
  margin-left: 45px;
}

.padding-left-45 {
  padding-left: 45px;
}

.margin-top-40 {
  margin-top: 40px;
}

.padding-top-40 {
  padding-top: 40px;
}

.margin-right-40 {
  margin-right: 40px;
}

.padding-right-40 {
  padding-right: 40px;
}

.margin-bottom-40 {
  margin-bottom: 40px;
}

.padding-bottom-40 {
  padding-bottom: 40px;
}

.margin-left-40 {
  margin-left: 40px;
}

.padding-left-40 {
  padding-left: 40px;
}

.margin-top-35 {
  margin-top: 35px;
}

.padding-top-35 {
  padding-top: 35px;
}

.margin-right-35 {
  margin-right: 35px;
}

.padding-right-35 {
  padding-right: 35px;
}

.margin-bottom-35 {
  margin-bottom: 35px;
}

.padding-bottom-35 {
  padding-bottom: 35px;
}

.margin-left-35 {
  margin-left: 35px;
}

.padding-left-35 {
  padding-left: 35px;
}

.margin-top-30 {
  margin-top: 30px;
}

.padding-top-30 {
  padding-top: 30px;
}

.margin-right-30 {
  margin-right: 30px;
}

.padding-right-30 {
  padding-right: 30px;
}

.margin-bottom-30 {
  margin-bottom: 30px;
}

.padding-bottom-30 {
  padding-bottom: 30px;
}

.margin-left-30 {
  margin-left: 30px;
}

.padding-left-30 {
  padding-left: 30px;
}

.margin-top-25 {
  margin-top: 25px;
}

.padding-top-25 {
  padding-top: 25px;
}

.margin-right-25 {
  margin-right: 25px;
}

.padding-right-25 {
  padding-right: 25px;
}

.margin-bottom-25 {
  margin-bottom: 25px;
}

.padding-bottom-25 {
  padding-bottom: 25px;
}

.margin-left-25 {
  margin-left: 25px;
}

.padding-left-25 {
  padding-left: 25px;
}

.margin-top-20 {
  margin-top: 20px;
}

.padding-top-20 {
  padding-top: 20px;
}

.margin-right-20 {
  margin-right: 20px;
}

.padding-right-20 {
  padding-right: 20px;
}

.margin-bottom-20 {
  margin-bottom: 20px;
}

.padding-bottom-20 {
  padding-bottom: 20px;
}

.margin-left-20 {
  margin-left: 20px;
}

.padding-left-20 {
  padding-left: 20px;
}

.margin-top-15 {
  margin-top: 15px;
}

.padding-top-15 {
  padding-top: 15px;
}

.margin-right-15 {
  margin-right: 15px;
}

.padding-right-15 {
  padding-right: 15px;
}

.margin-bottom-15 {
  margin-bottom: 15px;
}

.padding-bottom-15 {
  padding-bottom: 15px;
}

.margin-left-15 {
  margin-left: 15px;
}

.padding-left-15 {
  padding-left: 15px;
}

.margin-top-10 {
  margin-top: 10px;
}

.padding-top-10 {
  padding-top: 10px;
}

.margin-right-10 {
  margin-right: 10px;
}

.padding-right-10 {
  padding-right: 10px;
}

.margin-bottom-10 {
  margin-bottom: 10px;
}

.padding-bottom-10 {
  padding-bottom: 10px;
}

.margin-left-10 {
  margin-left: 10px;
}

.padding-left-10 {
  padding-left: 10px;
}

.margin-top-5 {
  margin-top: 5px;
}

.padding-top-5 {
  padding-top: 5px;
}

.margin-right-5 {
  margin-right: 5px;
}

.padding-right-5 {
  padding-right: 5px;
}

.margin-bottom-5 {
  margin-bottom: 5px;
}

.padding-bottom-5 {
  padding-bottom: 5px;
}

.margin-left-5 {
  margin-left: 5px;
}

.padding-left-5 {
  padding-left: 5px;
}
```

---

```scss
/* for from through （包含 20） */
/* each */
// border
$border-style: solid, dashed, dotted;
@for $i from 1px through 20px {
  $v: calc($i / 1px);

  @each $style in $border-style {
    .border-#{$style}-#{$v} {
      border: $i $style $main-color;
    }
  }
}

/* for from to （不包含 20） */
/* each */
// border
// $border-style: solid, dashed, dotted;
// @for $i from 1px to 20px {
//   $v: calc($i / 1px);

//   @each $style in $border-style {
//     .border-#{$style}-#{$v} {
//       border: $i $style $main-color;
//     }
//   }
// }
```

上面使用 `for from through/to` 和 `each` 循环设置了一些 border 的预制规则。

转译之后的 CSS 为：

```css
.border-solid-1 {
  border: 1px solid #000000;
}

.border-dashed-1 {
  border: 1px dashed #000000;
}

.border-dotted-1 {
  border: 1px dotted #000000;
}

.border-solid-2 {
  border: 2px solid #000000;
}

.border-dashed-2 {
  border: 2px dashed #000000;
}

.border-dotted-2 {
  border: 2px dotted #000000;
}

.border-solid-3 {
  border: 3px solid #000000;
}

.border-dashed-3 {
  border: 3px dashed #000000;
}

.border-dotted-3 {
  border: 3px dotted #000000;
}

.border-solid-4 {
  border: 4px solid #000000;
}

.border-dashed-4 {
  border: 4px dashed #000000;
}

.border-dotted-4 {
  border: 4px dotted #000000;
}

.border-solid-5 {
  border: 5px solid #000000;
}

.border-dashed-5 {
  border: 5px dashed #000000;
}

.border-dotted-5 {
  border: 5px dotted #000000;
}

.border-solid-6 {
  border: 6px solid #000000;
}

.border-dashed-6 {
  border: 6px dashed #000000;
}

.border-dotted-6 {
  border: 6px dotted #000000;
}

.border-solid-7 {
  border: 7px solid #000000;
}

.border-dashed-7 {
  border: 7px dashed #000000;
}

.border-dotted-7 {
  border: 7px dotted #000000;
}

.border-solid-8 {
  border: 8px solid #000000;
}

.border-dashed-8 {
  border: 8px dashed #000000;
}

.border-dotted-8 {
  border: 8px dotted #000000;
}

.border-solid-9 {
  border: 9px solid #000000;
}

.border-dashed-9 {
  border: 9px dashed #000000;
}

.border-dotted-9 {
  border: 9px dotted #000000;
}

.border-solid-10 {
  border: 10px solid #000000;
}

.border-dashed-10 {
  border: 10px dashed #000000;
}

.border-dotted-10 {
  border: 10px dotted #000000;
}

.border-solid-11 {
  border: 11px solid #000000;
}

.border-dashed-11 {
  border: 11px dashed #000000;
}

.border-dotted-11 {
  border: 11px dotted #000000;
}

.border-solid-12 {
  border: 12px solid #000000;
}

.border-dashed-12 {
  border: 12px dashed #000000;
}

.border-dotted-12 {
  border: 12px dotted #000000;
}

.border-solid-13 {
  border: 13px solid #000000;
}

.border-dashed-13 {
  border: 13px dashed #000000;
}

.border-dotted-13 {
  border: 13px dotted #000000;
}

.border-solid-14 {
  border: 14px solid #000000;
}

.border-dashed-14 {
  border: 14px dashed #000000;
}

.border-dotted-14 {
  border: 14px dotted #000000;
}

.border-solid-15 {
  border: 15px solid #000000;
}

.border-dashed-15 {
  border: 15px dashed #000000;
}

.border-dotted-15 {
  border: 15px dotted #000000;
}

.border-solid-16 {
  border: 16px solid #000000;
}

.border-dashed-16 {
  border: 16px dashed #000000;
}

.border-dotted-16 {
  border: 16px dotted #000000;
}

.border-solid-17 {
  border: 17px solid #000000;
}

.border-dashed-17 {
  border: 17px dashed #000000;
}

.border-dotted-17 {
  border: 17px dotted #000000;
}

.border-solid-18 {
  border: 18px solid #000000;
}

.border-dashed-18 {
  border: 18px dashed #000000;
}

.border-dotted-18 {
  border: 18px dotted #000000;
}

.border-solid-19 {
  border: 19px solid #000000;
}

.border-dashed-19 {
  border: 19px dashed #000000;
}

.border-dotted-19 {
  border: 19px dotted #000000;
}

.border-solid-20 {
  border: 20px solid #000000;
}

.border-dashed-20 {
  border: 20px dashed #000000;
}

.border-dotted-20 {
  border: 20px dotted #000000;
}
```

---

```scss
/* function */
/* if else */
@function colorFn($color) {
  @if ($color == $main-color) {
    @return $gray-color;
  } @else {
    @return fade-out($color, 0.2);
  }
}

/* mixin */
// border style
@mixin border-style($width: 1px, $style: solid, $color: $main-color) {
  border: $width $style colorFn($color);
}

div {
  @include border-style;
}

p {
  @include border-style($color: $gray-color);
}

/* mixin */
// base font style
@mixin base-style($color: $main-color, $font-size: 16px, $font-weight: 400, $line-height: 1) {
  color: colorFn($color);
  font-size: $font-size;
  font-weight: $font-weight;
  line-height: $line-height;
}

/* include */
h1 {
  @include base-style;
}

/* extend */
h2 {
  @extend h1;
}

h3 {
  @include base-style($color: red);
}

/* mixin */
// hover color
@mixin hover-color($color: $main-color) {
  color: colorFn($color);
  transition: color 0.3s;
}

a {
  &:hover {
    @include hover-color;
  }
}
```

自定义 `function` 可以对一些逻辑进行封装处理，另外可以使用 `if else` 进行一些逻辑判断。

`mixin` 可以对一段复用比较多的样式进行包装处理，并可以进行传参修改默认设置的值。使用 `@include` 对 `mixin` 进行引用，并可以调用传参。

`extend` 可以完全复用某一个已声明的类。

转译之后的 CSS 为：

```css
div {
  border: 1px solid #999999;
}

p {
  border: 1px solid rgba(153, 153, 153, 0.8);
}

h1,
h2 {
  color: #999999;
  font-size: 16px;
  font-weight: 400;
  line-height: 1;
}

h3 {
  color: rgba(153, 153, 153, 0.8);
  font-size: 16px;
  font-weight: 400;
  line-height: 1;
}

a:hover {
  color: #999999;
  transition: color 0.3s;
}
```

---

```scss
/* mixin */
// flex
@mixin display-flex($direction: row, $justify: center, $align: center) {
  display: flex;
  flex-direction: $direction;
  justify-content: $justify;
  align-items: $align;
}

/* each */
$direction: left, center, right;
@each $i in $direction {
  .display-flex-justify-#{$i} {
    @include display-flex($justify: $i);
  }
  .display-flex-align-#{$i} {
    @include display-flex($align: $i);
  }
}
```

转译之后的 CSS 为：

```css
.display-flex-justify-left {
  display: flex;
  flex-direction: row;
  justify-content: left;
  align-items: center;
}

.display-flex-align-left {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: left;
}

.display-flex-justify-center {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.display-flex-align-center {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.display-flex-justify-right {
  display: flex;
  flex-direction: row;
  justify-content: right;
  align-items: center;
}

.display-flex-align-right {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: right;
}
```
