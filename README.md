# scss-basis
scss基本使用语法

## 注释

scss支持单行注释
```scss
.userCard {
  // 边框
  border: 1px solid #000;
}
```

## 嵌套规则

1. BEM命名法
2. 嵌套选择器
3. & 符号
4. 嵌套属性

先认识一个命名法：

BEM（block element modifier），css命名法，非常啰嗦。

1. 取一个主要名字，比如：User Card
2. 所有命名都围绕User Card（Block）
   1. 头像：.user-card__picture（Element 使用__）
   2. 姓名：.user-card__name （Element 使用__）
3. 个人介绍：.user-card__description（Modifier不同状态 使用-- ）
   1. 如果某个元素有某些变化（点击、hover等）
      1. User Card被激活: .user-card--active
      2. User Card被取消：.user-card--deactive
   2. 如果是头像被激活：.user-card__picture--active

使用BEM的思想，形成一套更简洁（BEM太啰嗦了）又直观的的命名法：
`.user-card__picture--active` 简化为 `.userCard-picture .active`

根据这种命名规则，可以用css`&-`进行嵌套：

```html
  <div class="userCard active">
    <div class="userCard-picture">picture</div>
  </div>
```

```css
.userCard {
  width: 100px;
  &-picture {
    color: red;

    font: {
      size: 28px;
      weight: bold;
      family: serif;
    }
    background: {
      // ...
    }
  }
  &.active {
    background: green;
  }
}
```

## 变量

可以使用!global修饰强行变局部变量为全局变量
如果你定义了一个名为`$max-width`的变量，使用`$max_width`也可以访问它

scss中的变量有作用域:

```html
  <div class="userCard active">
    <div class="userCard-picture">picture</div>
  </div>
```

```css
$red: #f00;
.userCard {
  $red: #f90;
  border: 1px solid $red;

  &-picture {
    // 为#f90
    color: $red;
  }
}
```

## 运算

支持：
1. 数字的加减乘除取模5中运算
2. 颜色相关运算
3. 字符串插值

画一个圆：

```scss
.circle {
  $width: 10 * 100px;
  width: $width;
  height: $width;
  border-radius: $width / 2;
  border: 1px solid #000;
  border-width: (10px / 2);
  position: fixed;
  top: 197 % 99px + 100px;
  left: 100px / 2 - 0;
}
```

字符串插值`#{}`

```scss
.colorVal {
  $color: red;
  &::before {
    content: 'color value: 『#{$color}'
  }
  &::after {
    content: '』'
  }
}
```

## mixin

基本用法：

```html
<body>  
  <div class="smallBox"></div>
  <div class="bigBox"></div>
</body>
```

```scss
@mixin box {
  box-shadow: 0 0 5px black;
  margin: 10px;
}
.smallBox {
  width: 100px;
  height: 100px;
  // 相当于把@mixin box里的内容复制到这里
  @include box;
}
.bigBox {
  width: 300px;
  height: 300px;
  // 相当于把@mixin box里的内容复制到这里
  @include box;
}
```

也可以支持参数：

```scss
@mixin box($color: black, $margin: 10px) {
  box-shadow: 0 0 5px $color;
  margin: $margin;
}
.smallBox {
  width: 100px;
  height: 100px;
  // 相当于把@mixin box里的内容复制到这里
  @include box(red);
}
.bigBox {
  width: 300px;
  height: 300px;
  // 相当于把@mixin box里的内容复制到这里
  @include box(green, 20px);
}
```

## %(placeholder)

```scss
%box {
  box-shadow: 0 0 5px black;
  margin: 10px;
}

.smallBox {
  width: 100px;
  height: 100px;
  @extend %box;
}
.bigBox {
  width: 300px;
  height: 300px;
  @extend %box;
}

// 相当于
// .bigBox, .smallBox {
//   box-shadow: 0 0 5px black;
//   margin: 10px;
// }
```

mixin和%(placeholder)的区别：

mixin复制css属性，%复制选择器。能使用%请尽量使用，因为实际代码会少一些。

## function

一般用于对某个css属性进行统一计算，也就是复用属性值的计算逻辑。

```scss
@function double($value) {
  @return $value * 2px;
}
@function half($value) {
  @return $value / 2 + px;
}
.smallBox {
  // 50px
  width: half(100);
  // 50px
  height: half(100);
  border: 1px solid #000;
}

.bigBox {
  // 200px
  width: double(100);
  // 200px
  height: double(100);
  border: 1px solid #000;
}
```

## 循环

一般用在动画帧里：

https://www.jianshu.com/p/86dc2d8b7870

## 响应式

核心：利用scss函数将设计稿里所有元素宽高相对于设计稿的宽高占比，自动转为vw

例子：设计稿的宽度为375px，页面内容为一个方框包含四个水平均匀分布的圆环(直径为69px)，要响应式适配所有设备

```html
<div class="icons">
  <div class="icon">1</div>
  <div class="icon">2</div>
  <div class="icon">3</div>
  <div class="icon">4</div>
</div>
```

```scss
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
// 计算适配比
@function px($npx) {
  @return $npx / 375 * 100vw;
}
.icons {
  border: 1px solid red;
  display: flex;
  justify-content: space-between;
  > .icon {
    border: 1px solid green;
    height: px(69);
    width: px(69);
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
```
