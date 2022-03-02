# 《CSS 世界》读书笔记

> 先前读过很多张鑫旭写的有关于 CSS 的博客，他是一个钟爱 CSS 且热衷于分享的一名前端开发工程师。张所著的 CSS 系列书包括《CSS 世界》《CSS 选择器世界》《CSS 新世界》，待我娓娓读来。

# 前言

1. 张鑫旭是一个利他主义者，他想帮助更多人去学习 CSS。
2. 张鑫旭认为高不成低不就的前端人员急需本书深入“CSS世界”，突破瓶颈，告别迷茫。

> 在CSS的世界里，页面上的任何看似简单的呈现都是由许许多多CSS属性共同作用的结果。

> 有些人的CSS水平之所以停滞不前，是因为他们没有把所有的CSS当作一个整体，放在一个完整的世界中去看待。所以，没有比“学CSS看看CSS中文手册就够了”更愚蠢的言论了，手册仅仅是表层的、独立的一些特性，每个CSS属性在CSS世界中都是有其存在的原因的，都是和其他多个CSS属性发生着千丝万缕的关系的，这背后的种种远比他们想象得要庞大很多。

3. 张鑫旭认为这本书是他个人的理解，不是技术文档，也不是参考手册，不会面面俱到。
4. 本书配套了一个用于提问和反馈的论坛。
5. 感谢出版社每个人，感谢编辑、同仁、读者、妻子。

# 第1章 概述

1. 先对 CSS 的整体认知有利于对其深入理解。
2. CSS 本身并无逻辑可言，看中的事特性之间的相互联系和具象能力。
3. CSS，Cascading Style Sheets， 层叠样式表。
4. CSS 世界的诞生就是为图文信息展示服务的。
5. SVG 文字排版做不好，CSS 压制 SVG 数年，直到矢量图形得到重视，SVG 迎来第一春。
6. CSS 的“流”使得 CSS 图文显示能力如此强劲，因为流影响了 CSS 世界的基石 HTML。
7. 本书的“世界”特指 CSS2.1 的世界。
8. table 标签不属于 CSS 世界。
9. 本书不会深入 CSS3 的知识点。

# 第2章 需提前了解的术语和概念

1. 基本概念，略。
2. CSS 世界的“未定义行为”：不同浏览器会出现不一样的行为或样式表现，这并不是浏览器的 bug，只是没有具体规范来限制此行为。

# 第3章 流、元素与基本尺寸

1. HTML 标签通常分为两类：块级元素（block level element）和内联元素（inline element）。
2. 块级元素和 display 为 block 的元素不是一个概念。li 元素默认的 display 值为 list-item， table 元素默认的 display 值是 table，但它们都是块级元素。
3. 块级元素在一个水平流上只能单独显示一个元素，多个块级元素则换行显示。
4. 利用块级元素具有换行的特性，理论上可以使用块级元素配合 clear 属性来清除浮动带来的影响。

```
.clear:after {
    content: '';
    display: table; //or block, list-item
    clear: both;
}
```

5. 实际开发中不会使用 list-item 来清除浮动，原因有三。
   - 字符较多。
   - 会出现 list-item 符号，需要添加 list-style:none 声明。
   - 根本原因是其兼容性不好。
6. display 属性其实规定了元素的内外两个盒子的级别。
7. width/height 属性作用在内在盒子上。
8. width 属性默认值为 auto，其至少包括以下四种情况
   - fill-available：比如 div、p 元素的默认宽度是 100% 与父级容器的。
   - shrink-to-fit：比如浮动、绝对定位、inline-block 元素、table元素。类似 CSS3 中的 fit-content。
   - minimum-content-width：类似 mini-content。
   - maximum-content-width：类似 max-content。
9. 在 CSS 世界里，尺寸也分内部尺寸和外部尺寸，表示内部尺寸由内部元素决定，宽度（外部尺寸）由外部元素决定。
10. fill-available 是外部尺寸，而其余三个是内部尺寸。
11. “无宽度，无图片，无浮动。” ——鑫三无准则。
12. 利用流的特性编写 CSS 代码，记住无宽度这条准则，既减少代码，又减少计算，还减少维护。
13. 绝对定位模型中，默认情况下，宽度的表现是由内部尺寸决定；当绝对定位模型同时具有对立方为属性值的时候，其宽度大小由外部具有定位特性的祖先元素的尺寸决定。
14. 如果一个元素里面没有内容，宽度为 0，那么这个元素使用的就是为“内部尺寸”规则。
15. shrink-to-fit 指的是元素内部尺寸由内部元素决定，但永远小于外部容器的尺寸。
16. button 标签按钮是 inline-block 元素，而 input 标签按钮默认 white-space。
17. button 按钮文字越多，宽度越宽，文字足够多，则会在容器宽度处自动换行。
18. word-break:break-all，英文字符换行。
19. minimum-content-width 指的是元素最适合的最小宽度。具体实现为 shrink-to-fit 元素设置 width 为 0。
20. 可以利用伪类元素和 minimum-content-width 规则实现凹、凸的图形。
21. maximum 指的是元素可以有的最大宽度。具体实现为 shrink-to-fit 元素设置 white-space:nowrap。如果内部没有块级元素或块级元素没有设定宽度值，则这种元素的宽度实际上是最大连续内联盒子的宽度和
22. 盒模型分为 `content-box`、`padding-box`、`border-box`。
23. `background-clip: border-box | padding-box | content-box | text`。
24. `background-origin: border-box | padding-box | content-box`。
25. `content-box` 盒模型的宽度指的是 content box 上的宽度。
26. `content-box` 的设计大概是满足当年面向内容的 CSS 2.1 的需求所设计的，这种设计在当下丰富的图文需求下使布局的流动性变得不灵活。
27. 宽度分离原则：给定父元素宽度，使子元素通过 margin、padding 和 border 属性自动适应宽度。
28. `padding-box` 属性已经被弃用。
29. `box-sizing` 属性的作用是改变 width 的作用细节。
30. 张鑫旭认为不支持 `margin-box` 的最大原因是其本身没有价值。
31. 全局设置盒模型为 `border-box` 的问题：
    1. 产生没必要的消耗：通配符 * 对默认为 `box-sizing` 盒模型元素而言是没有必要的消耗。
    2. 不能解决所有问题，因为 margin 仍需要计算。
32. `<textarea>` 元素不能通过 display 来实现尺寸自适应父容器，只能通过 width 来实现。
33. CSS border-box 重置应如下：
```css
input, textarea, img, video, object {
   box-sizing: border-box;
}
```
34. 如果父元素 height 为 auto，只要子元素在文档流中，其百分比值完全就被忽略了。`'auto' * 100% = NaN`。
35. 如何让元素支持 height:100% ?
    1. 设定显式的高度值。非绝对定位的元素宽高百分比是相对于 content box 计算的。
    2. 使用绝对定位。绝对定位的元素宽高百分比计算是相对于 padding box 计算的。
36. 