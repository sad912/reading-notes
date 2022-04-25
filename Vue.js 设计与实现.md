# 序 尤雨溪
1. 霍春阳为 Vue.js 3 提交了大量补丁，修复了一些非常深层的渲染更新 bug，为 Vue.js 3 做出了很多贡献，成为了官方团队成员。
2. 春阳对 Vue.js 的高层设计思想的理解非常精准，并在框架的设计权衡层面有自己深入思考，这是本书区别于市面上其他源码分析类图书的地方。

# 前言
1. Vue.js 3.0 在框架设计与实现上做了很多创新，在一定程度上，我们可以认为 Vue.js 3.0 还清了在 Vue.js 2 中欠下的技术债务。
2. 3.0 秉承了 2.0 的易用性，做到了使用更少的代码实现更多的功能。
3. 3.0 在模块的拆分和设计上做得非常合理。模块之间的耦合度非常低，很多模块可以独立安装使用，不需要依赖完整的 Vue.js 运行时。
4. 3.0 在设计内建组件和模块时花费了很多精力，配合构建工具以及 Tree-Shaking 机制，实现了内建能力的按需引入，实现了用户 bundle 的体积最小化。
5. 3.0 的扩展能力非常强，可以编写自定义的渲染器，甚至可以编写编译器插件来自定义模版语法。
6. 3.0 的核心设计思想不仅能够让我们更加从容地面对复杂问题，还能指导我们在其他领域进行架构设计。
7. 3.0 中很多功能的设计需要谨遵规范。使用 Proxy 实现响应系统需要遵循 ECMAScript 规范；模版解析器需要遵从 WHATWG 规范。
## 本书内容
1. 本书内容并非"源码解读"，而是建立在对框架设计的理解之上，由简入繁的介绍如何实现各个功能模块。例如，通过阅读 ECMAScript 规范，基于 Proxy 实现一个完善的响应系统；通过阅读 WHATWG 规范，实现一个类 HTML 语法的模版解析器，并在此基础上实现一个支持插件架构的模版编译器。
2. 本书还讨论
    1. 框架设计的核心要素以及设计过程中所做出的权衡；
    2. 三种常见的虚拟 DOM 的 Diff 算法；
    3. 组件化的实现与 Vue.js 内建组件的原理；
    4. 服务端渲染、客服端渲染、同构渲染之间的差异以及同构渲染的原理。
## 本书结构
1. 框架设计概览
    1. 第一章
        1. 命令式和声明式两种范式的差异以及二者对框架设计的影响；
        2. 虚拟 DOM 的性能状况；
        3. 运行时和编译时的相关知识；
        4. 介绍 Vue.js 3.0 是一个运行时和编译时的框架。
    2. 第二章
        1. 从用户的开发体验、控制框架代码的体积、Tree-shaking 的工作机制、框架产物、特性开关、错误处理、TypeScript 支持等几个方便出发，讨论了框架设计者在设计框架时应该考虑的内容。
    3. 第三章
        1. 全局视角介绍 Vue.js 3.0 的设计思路。
        2. 介绍各个模块之间是如何协作的。
2. 响应系统
    1. 第四章
        1. 宏观视角讲述了 Vue.js 3.0 响应系统的实现机制。
        2. 从副作用函数开始，逐步实现响应系统。
        3. 讲述了计算属性和 watch 的实现原理。
        4. 讨论了实现响应系统过程中所遇到的问题，以及相应解决方案。
    2. 第五章
        1. 从 ECMAScript 规范入手，从最基本的 Proxy、Reflect 以及 JavaScript 对象的工作原理开始，讨论了使用 Proxy 代理 JavaScript 对象的方式。
    3. 第六章
        1. 讨论了 ref 的概念，并基于 ref 实现原始值的响应式方案。
        2. 讨论了如何使用 ref 解决响应丢失问题。
3. 渲染器
    1. 第七章
        1. 渲染器与响应系统的关系
4. 组件化
    1. 
5. 编译器
6. 服务端渲染

# 第一篇 框架设计概览
## 第 1 章 权衡的艺术
### 概述
1. 框架设计里到处都体现了权衡的艺术。
2. 讨论框架各个模块的实现思路和细节前，应该先讨论框架设计。
3. 作为设计者，要有框架定位和方向全局的把控；作为学习者，也应该从全局角度对框架设计拥有清晰的认知。
4. 另外，从范式的角度，框架应该设计成命令式还是声明式还是？两种范式有何优缺点？是否能汲取两者的优点？
5. 框架要设计成运行时还是编译时，甚至运行时和编译时呢？两者差异、优缺点都需要权衡。
### 命令式和声明式
1. 从范式上来看，视图层框架通常分为命令时和声明式。
2. 命令式框架的一大特点就是关注过程。
3. jQuery 就是典型的命令式框架。
4. 声明式框架更加关注结果。
5. 我们给 Vue.js 一个结果，告诉框架帮我们实现这个结果的过程。Vue.js 帮我们封装了实现结果的过程。
### 性能与可维护性
1. 声明式代码的性能不优于命令式代码的性能。
2. 声明式代码的更新性能消耗 = 找出差异的性能消耗 + 直接修改的性能消耗。
3. 声明式代码的可维护性更强。
4. 框架设计者要做的是：在保持可维护性的同时让性能损失最小化。
### 虚拟 DOM 的性能到底如何
1. 如果可以最小化的找出差异的性能消耗，就可以让声明式代码的性能无限接近命令式代码的性能。
2. 所谓的虚拟 DOM ，就是为了最小化找出差异这一步的性能消耗而出现的。
3. 采用虚拟 DOM 的更新技术的性能理论不可能比原生 JavaScript 操作 DOM 更高。
4. 但在大部分情况下，我们很难写出绝对优化的命令式代码，尤其是当应用程序规模很大的时候，即使写出了极致优化的代码，也耗费了很多精力，且不便于维护。
5. 所以虚拟 DOM 解决的问题是：不用付出太多声明式代码的努力，就能保证应用程序的性能下限，甚至逼近命令式代码的性能。
6. 上述的原生 JavaScript 操作指的是像 `document.createElement` 之类的 DOM 操作方法，并不包含`innerHTML` 。
7. `innerHTML` 比较特殊，在给元素的 `innerHTML` 上赋值后，首先要把赋值的字符串解析成 DOM 树，这是一个 DOM 层面的计算。
8. 纯 JavaScript 层面的操作比 DOM 操作要快的多，不在一个数量级上。
9. innerHTML 创建页面的性能：HTML 字符串拼接的计算量 + innerHTML 的 DOM 计算量。
10. 虚拟 DOM 创建页面的过程分为两步，第一步是创建 JavaScript 对象（真实 DOM 的描述），第二步是递归地遍历虚拟 DOM 树并创建真实的 DOM。
11. 虚拟 DOM 创建页面的性能 = 创建 JavaScript 对象的计算量 + 创建真实 DOM 的计算量。
12. 所以 innerHTML 和虚拟 DOM 在创建页面时的性能差距不大， 在同一个数量级。
13. 使用 innerHTML 更新页面的过程是重新构建 HTML 字符串，再重新设置 DOM 元素的 innerHTML 属性，这意味着我们要销毁所有旧的 DOM 元素，然后创建新的 DOM 元素。
14. 虚拟 DOM 更新页面的过程是，重新创建 JavaScript 对象（虚拟 DOM 树），对比新旧对象，找到变化的元素，然后更新变化的 DOM。
15. 在更新页面时，虚拟 DOM 在 JavaScript 层面的运算要比创建页面多出一个 Diff 的性能消耗，但是是一个 JavaScript 层面的运算，所以不会产生数量级的差异；而在 DOM 层面的运算上，虚拟 DOM 在更新页面时只需要更新必要的元素，而 innerHTML 需要全量更新。
16. 更新页面时
    1. 耗费精力：虚拟 DOM < innerHTML < 原生 JavaScript；
    2. 维护性：原生 JavaScript < innerHTML < 虚拟 DOM；
    3. 性能：innerHTML < 虚拟 DOM < 原生 JavaScript；
17. 为了省力，可维护，高性能，选择虚拟 DOM 实现更新页面是一个不错的选择。
### 运行时和编译时
1. 设计框架有三种选择
    1. 纯运行时；
    2. 运行时 + 编译时；
    3. 纯编译时；
2. 纯运行时的框架，没有编译的过程，因此我们没法分析用户提供的内容。
3. 加入编译步骤，我们可以分析用户提供的内容，可以在编译时得到额外信息，利用额外信息可以对运行时优化。
4. 纯编译时的框架，我们可以分析用户提供的内容，性能也会更好，但是会有损灵活性，必须编译后才能使用。
5. 业界三个方向都有探索，其中 Svelte 就是纯编译时的框架，但它的真实性可能达不到理论高度。
## 第 2 章 框架设计的核心要素
### 概述
1. 框架设计不仅仅要把功能开发完成，还有很多学问。
    1. 框架应该提供哪些构建产物？
    2. 产物的模块格式如何？
    3. 用户没有以预期的方式使用框架时，是否应该打印合适的警告信息从而提供更好的开发体验？
    4. 开发版本的构建和生产版本的构建有何区别？
    5. 热更新的支持与否？
    6. 框架提供的多个功能是否可以选择部分功能来使用以减少最终资源的打包体积？
### 提升用户的开发体验
1. 根据可能的条件提供友好的警告信息。
2. 通过 custom formatter 提供定制的格式化打印信息。
### 控制框架代码的体积
1. 提供越完善的警告信息就意味着要编写更多的代码，但代码越少浏览器加载资源的时间才会越少。如何解决这个问题？
2. Vue.js 3 中，每一个 warn 函数的调用都会配合 `__DEV__` 常量进行 `if` 检查，这样就做到了在开发环境为用户提供友好警告的同时，不会增加生产环境代码的体积。
### 框架要做到良好的 Tree-Shaking
1. 在前端领域，Tree-Shaking 因 rollup.js 而普及。
2. Tree-Shaking 指的是移除那些永远不会被执行的代码。
3. 想实现 Tree-shaking，必须要使用 ES Module，因为 Tree-Shaking 依赖 ESM 的静态结构。
4. 存在 dead code 不需要被移除，因为函数的调用可能会产生副作用，产生副作用的函数的相关代码，不应该被自动删掉。
5. 读取对象的值也可能会产生副作用，因为如果该对象时通过 Proxy 创建的代理对象，那么读取是就会触发代理钩子，进而产生副作用。
6. Tree-Shaking 工具都会提供一个机制，让我们通过注释的方法（`/*#__PURE__*/`）来告诉工具这个函数不会产生副作用。
7. 因此，编写框架的时候应该合理使用 `/*#__PURE__*/` 注释。
### 框架应该输出怎样的构建产物
1. Vue.js 3 提供生产环境（vue.global.prod.js）和开发环境（vue.global.js）的构建产物。
2. Vue.js 3 还会根据场景的不同输出其他形式的产物。
3. 我们希望用户可以使用 `<script>` 标签引入框架并使用。
    1. 为了实现这种功能，我们需要输出一个立即调用的函数表达式。
    2. vue.global.js 就是立即调用的函数表达式的资源。
    3. 我们使用 `<script>`标签直接引入 vue.global.js 文件后，全局变量 Vue 就是可用的了。
    4. 在 rollup.js 中，我们可以通过配置 format: 'iife' 来输出这种形式的资源。
4. 不过主流浏览器对原生的 ESM 的支持都不错，用户除了用 `<script>`标签引用 IIFE 格式的资源外，还可以直接引入 ESM 格式的资源。
    1. Vue.js 3 还会输出 vue.esm-browser.js 文件。
    2. 用户可以直接用 `<script type="module" src="/path/to/vue.esm-brower.js"></sciprt>` 标签引入。
5. 我们还提供了 `vue.esm-bundler.js` 文件，这种带有 `-bundler` 字样的 ESM 资源是给 rollup.js 或 webpack 等打包工具使用的。
    1. 我们构建提供给打包工具的 ESM 格式资源时，不能简单地设置 __DEV__ 变量为 true 或 false，而是要根据构建工具的开发环境而定。
6. 我们还希望用户可以在 Node.js 中通过 require 语句引用资源。
    1. `const Vue = require('vue)`。
    2. 当在服务端渲染时，Vue.js 的代码是在 Node.js 环境中运行的，资源的模块格式应该是  CommonJS。
    3. 为 了输出 CommonJS 模块 的资源，我们可以通过修改 rollup.config.js 的配置 `format: 'cjs'` 来实现。
### 特性开关
1. 在我们向用户提供诸多功能的同时，我们还应该提供对应的功能开关。
2. 通过功能的开关，我们可以实现良好的 Tree-Shaking。
3. 这种特性开关机制为框架的设计提供了灵活性，可以通过特性开关任意为框架添加新的特性，而不用担心资源体积变大。
4. 框架升级的时候可以通过特性开关来支持遗留 API，用户可以选择其保留与否，从而使打包资源体积最小化。
5. 利用 rollup.js 的预定义常量插件来实现特性开关。
6. 利用 webpack.DefinePlugin 插件来实现特性开关。
### 错误处理
1. Vue.js 3 为用户是提供了注册错误处理程序函数 `registerErrorHandle` ，并且封装了自动异常捕获函数 `callWithErrorHandling`。
2. 用户可以调用 `registerErrorHandle` 函数来注册统一的错误处理函数
3. 在 Vue.js 中，我们也可以注册统一的错误处理函数：
```JavaScript
import App from 'App.vue'
const app = create(App)
app.config.errorHandler = () => {
    // 错误处理程序
}
```

### 良好的 TypeScript 类型支持
1. 使用 TS 具有代码即文档、编辑器自动提示、一定程度上能够避免低级 bug、代码可维护性更强等优点，因此对 TS 类型的支持是否完善也称为评价一个框架的重要指标。
2. 在编写大型框架时，想要做到完善的 TS 类型支持很不容易，可以查看 Vue.js 源码中的 runtime-core/src/apiDefineComponent.ts 文件，此文件内容都是为类型支持服务的。
3. 同时，还要考虑 TSX 的支持。
## 第 3 章 Vue.js 3 的设计思路
### 概述
1. 本章全局了解 Vue.js 3 的设计思路、工作机制及其重要组成部分。
2. 这些重要组成部分作为独立的模块之间相互配合。
### 声明式地描述 UI
1. 使用和 HTML 一样的方式来描述 DOM 元素、属性以及层级结构。
2. 使用 `:`或 `v-bind` 来描述动态绑定的属性。
3. 使用 `@` 或 `v-on` 来描述事件。
4. 我们还可以使用 JavaScript 对象来描述 UI。
```JavaScript
let level = 3
const title = {
    tag: `h${level}`,
    props: {
        onClick: handler
    },
    children: [
        { tag: 'span' }
    ]
}
```

5. 使用 JavaScript 对象描述 UI 更加灵活，可以生成动态标签。
6. 这种用 JavaScript 对象描述 UI 的方式，其实就是所谓的虚拟 DOM。
### 初识渲染器
1. 渲染器的作用就是吧虚拟 DOM 渲染为真实的 DOM。
2. 编写一个渲染器。
```JavaScript
function mountElement(vnodem, container) {
    const el = document.createElement(vnode.tag)
    for (const key in vnode.props) {
        if (/^on/.test(key))
            el.addEventListener(key.substr(2).toLowerCase(), vnode.props[key])
    }
    if (typeof vnode.children === 'string')
       el.appendChild(document.createTextNode(vnode.children))
    else if (Array.isArray(vnode.children))
        vnode.children.forEach(child => renderer(child, el))
    container.appendChild(el)
```

3. 上述渲染器的功能仅是创建节点。
4. 对于渲染器来说，精髓在于精确地找到 vnode 对象的变更点而且只更新变更的内容。
### 组件的本质
1. 组件就是一组 DOM 元素的分装。
2. 定义一个函数来代表组件，而函数的返回值就代表组件要渲染的内容。
3. 一个组件的对象例子
```JavaScript
const vnode = {
    tag: function () {
        return {
            tag: 'div',
            props: {
                onClick: () => alert('hello')
            },
            children: 'click me'
        }
    }
}
```

4. 渲染器需要根据节点的类型来渲染标签元素或组件。
```JavaScript
function renderer (vnode, container) {
    if (typeof vnode.tag === 'string')
        mountElement(vnode, container)
    else if (typeof vnode.tag === 'function')
        mountComponent(vnode, container)
}
```

5. `mountComponent` 函数的实现。
```JavaScript
function mountComponent(vnode, container) {
    const subtree = vnode.tag()
    renderer(subtree, container)
}
```

6. 组件并非一定得是函数，我们完全可以使用一个 JavaScript 对象来表达组件。
```javaScript
const vnode = {
    tag: {
        render () {
            return {
                tag: 'div',
                props: {
                    onClick: () => alert('hello')
                },
                children: 'click me'
            }
        }
    }
}
```

7. 此时渲染器的判断条件为 `object`。
```JavaScript
function renderer (vnode, container) {
    if (typeof vnode.tag === 'string')
        mountElement(vnode, container)
    else if (typeof vnode.tag === 'object')
        mountComponent(vnode, container)
}
```

8. 同时，在 `mountComponent` 函数中，应该调用 `tag.render()` 函数。
```JavaScript
function mountComponent(vnode, container) {
    const subtree = vnode.tag.render()
    renderer(subtree, container)
}
```

9. 上述例子分别实现了用函数和对象表示组件的需求。
10. 其实 Vue.js 中的有状态组件就是使用对象结构来表达的。

### 模版的工作原理
1. 无论是手写虚拟 DOM，还是使用模版，都属于声明式的描述 UI。
2. 编译器使虚拟 DOM 是如何渲染成真实的 DOM。
3. 编译器和渲染器一样，只是一段程序而已，编译器的作用是将模版编译为渲染函数。
4. 编译器会把模版内容编译成渲染函数并添加到 `<script>` 标签块的组件对象上。
### Vue.js 是各个模版组成的有机整体
1. 组件的实现依赖于渲染器，模版的编译依赖于编译器，编译后的代码是根据渲染器和虚拟 DOM 的设计决定的。
2. 编译器和渲染器相互配合工作，实现性能提升。
3. 我们在生成虚拟 DOM 对象的时候，可以附带一些数据字段，用于表示一定含义，比如某个属性是动态的，这样，编译器和渲染器之间通过数据字段进行信息交流，从而提高性能。

# 第二篇 响应系统
## 第 4 章 响应系统的作用与实现

## 第 5 章 非原始值的响应式方案
## 第 6 章 原始值的响应式方案 
