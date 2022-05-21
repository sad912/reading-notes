# 「JavaScript Dom编程艺术」读书笔记

# 第四章

childNodes

nodeType

nodeValve

firstChild

lastChild

# 第五章

#### 平稳退化

javascript:伪协议

#### 分离 JavaScript

#### 向后兼容

   - 对象检测
   - 浏览器嗅探（browser sniffing）技术

#### 性能考虑

1. 尽量减少访问 DOM 和尽量减少标记
2. 合并和放置脚本
3. 压缩脚本

[JSMin](https://www.crockford.com/jsmin.html)

[https://developer.yahoo.com/yui/compressor](https://developer.yahoo.com/yui/compressor)

[http://closure-compiler.appspot.com/home](http://closure-compiler.appspot.com/home)

# 第六章

正如上一章讲到的那样，与 DOM 脚本编程工作有关的问题不外乎平稳退化、向后兼容和分离 JavaScript 这几大类。这些问题的解决方式和解决程度影响着网页的可用性和可访问性。

新坑《Advanced DOM Scripting: Dynamic Web Design Techniques》

#### 共享 onload 事件

```other
function addLoadEvent (func) {
	let oldOnload = window.onload
	if (typeof window.onload != 'function') 
		window.onload = func
	else {
		window.onload = function() {
			oldOnload()
			func()
		}
	}
}
```

# 第七章

#### 传统方法添加标记

- document.write方法
- innerHTML属性

#### DOM 方法

- createElement 方法
- createTextNode 方法
- appendChild 方法
- insertBefore 方法

# 第八章

1. <abbr>标签
2. <blockquato>标签
3. Element.assessKey属性

#### DOM 方法

- getElementById
- getElementsByTagName
- getAttribute
- createElement
- createTextNode
- appendChild
- insertBefore
- setAttribute

# 第九章

> 在用CSS安排你的内容时，千万不要人云亦云地认为表格都是不好的。虽然利用表格来做页面布局不是好主意，但利用表格来显示表格数据却是理所应当的。

- :first-child
- :last-child
- :nth-child()
- :nth-of-type()
- :hover

#### 添加类名

```other
addClass => (element, value) {
	element.className = element.className ? `${element.className} ${value}` : value
}
```

> 只要有可能，就应选择更新className属性，而不是去直接更新style对象的有关属性。

# 第十章

- setTimeout(“function”, *interval*)
- clearTimeout(*variable*)

#### 实现 moveElement 动画函数

![Image.png](https://res.craft.do/user/full/3a190bec-5a1d-c7ff-6eba-777c2791ff14/doc/11941C71-BF1E-46F9-9AFC-C059F1D9E2C4/5196A7A6-C46A-48F1-8CCC-C2BC8CF5BF46_2/9PK8UvVvLTheFUp8IMlBXXatxp6xEiJvHaxJmWX3y7wz/Image.png)

本书介绍的实现此函数的过程十分详细，体现了由「实例」到「抽象」到思想，十分经典。

#### 巧用属性

> 既不能使用全局变量，也不能使用局部变量。我们需要一种介乎它们二者之间的东西，需要一个只与正在被移动的那个元素有关的变量。只与某个特定元素有关的变量是存在的。事实上，我们一直在使用它们。那就是“属性”。

# 第十一章

> Modernizr（[http://www.modernizr.com/](http://www.modernizr.com/)）是一个开源的JavaScript库，利用它的富特性检测功能，可以对HTML5文档进行更好的控制。

可以使用 Modernizr 来解决不同浏览器兼容问题。

#### 实现图片数据对应彩色灰度化

![Image.png](https://res.craft.do/user/full/3a190bec-5a1d-c7ff-6eba-777c2791ff14/doc/11941C71-BF1E-46F9-9AFC-C059F1D9E2C4/10383C20-A7DA-456A-AD92-0342F92DE253_2/udPctLSunbxxMBO8Y873le8MEi5Gs06LUmLXEo8SF6sz/Image.png)

#### HTML5 vdieo 相关属性

[HTML Standard](https://html.spec.whatwg.org/multipage/media.html#video)

#### HTML5 form 相关属性

#### HTML5 其他特性

1. localStorage 和 sessionStorage
2. WebSocket
3. Web Worker
4. 拖放实现
5. 地理位置服务API

详细查看

[HTML Standard](https://html.spec.whatwg.org/)

#### 第十二章

实践

