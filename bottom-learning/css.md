# css 那些事

## 1.no-image

能用 iconfont 绝对不用图片，能用代码实现不用 iconfont。还是那句话越接近底层性能越好

css icon > iconfont > image

## 2.css-lint

- 1.不要使用多个 class 选择元素，如 a.foo.boo，这在 ie6 及以下不能正确解析
- 2.移除空的 css 规则，如 a{}
- 3.正确的使用显示属性，如 display:inline 不要和 width，height，float，margin,padding 同时使用
  display:inline-block 不要和 float 同时使用等
- 4.避免过多的浮动，当浮动次数超过十次时，会显示警告
- 5.避免使用过多的字号，当字号声明超过十种时，显示警告
- 6.避免使用过多 web 字体，当使用超过五次时，显示警告
- 7.避免使用 id 作为样式选择器
- 8.标题元素只定义一次
- 9.使用 width:100%时要小心
- 10.属性值为 0 时不要写单位
- 11.各浏览器专属的 css 属性要有规范，例如.foo{-moz-border-radius:5px;border-radius:5px}
- 12.避免使用看起来像正则表达式的 css3 选择器
- 13.遵守盒模型规则

## 3.BFC IFC GFC FFC

Box 是 CSS 布局的对象和基本单位， 直观点来说，就是一个页面是由很多个 Box 组成的。元素的类型和 display 属性，决定了这个 Box 的类型。 不同类 型的 Box， 会参与不同的 Formatting Context（一个决定如何渲染文档的容 器），因此 Box 内的元素会以不同的方式渲染。

BFC(Block Formatting Contexts)直译为"块级格式化上下文"，block-level box:display 属性为 block, list-item, table 的元素，会生成 block-level box。并且参与 block fomatting context；
IFC(Inline Formatting Contexts)直译为"内联格式化上下文"，IFC 的 line box（线框）高度由其包含行内元素中最高的实际高度计算而来（不 受到竖直方向的 padding/margin 影响)
FFC(Flex Formatting Contexts)直译为"自适应格式化上下文"，display 值 为 flex 或者 inline-flex 的元素将会生成自适应容器（flex container）
GFC(GridLayout Formatting Contexts)直译为"网格布局格式化上下文"， 当为一个元素设置 display 值为 grid 的时候，此元素将会获得一个独立 的渲染区域，我们可以通过在网格容器（grid container）上定义网格 定义行（grid definition rows）和网格定义列（grid definition columns） 属性各在网格项目（grid item）上定义网格行（grid row）和网格列 （grid columns）为每一个网格项目（grid item）定义位置和空间。
其实我们最常见的就是 BFC，详细说一说。


## 4.哪些元素会生成BFC?
- 根元素
- float属性不为none
- position为absolute或fixed
- display为inline-block, table-cell, table-caption, flex, inline-flex
- overflow不为visible
## 5.BFC能解决什么问题
BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。触发BFC的区域相互独立不重叠，举个例子：BFC的区域不会与float box重叠，因为float不为none时也会触发BFC，bfc之间相互独立。
案例1：
BFC能解决的最为经典的一个问题就是，margin重叠。两个div，1个设置margin-bottom: 20px, 一个设置margin-top:10px;这时你会发现两个div之间其实只相距20px而不是30px，出现了重叠现象，这时通过触发bfc就能解决
案例2：
浮动元素导致父元素高度塌陷；父元素采用overflow:hidden;父元素浮动；父元素添加一个伪类
## 6.矩阵（Matrix）
可能我们在日常开发中很少用到矩阵，用的更多的是transform,其实transform的skew（斜拉）、scale（缩放）、rotate（旋转）、translate（位移）等这些api只是封装好的便于使用的api，其实现原理还是Matrix（矩阵）

transform: matrix(a,b,c,d,e,f);
矩阵看上去有6个参数，晦涩难懂，但其实只需要通过线性方程式的推导，我们会发现transform的api都能使用矩阵来实现

例如：matrix(sx, 0, 0, sy, 0, 0); 等同于scale(sx, sy);

translate 等同于 transform: matrix(x,x,x,x, 水平偏移距离, 垂直偏移距离);

matrix的确可以提高性能，但是处理起来比较麻烦，我们可以利用生成工具来实现


