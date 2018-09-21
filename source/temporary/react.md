

1. 使用图片，需要使用require

2. className onClick 

3. CSS Modules 样式使用驼峰命名法 （first-name => firstName） , jsx 中通过 import styles from 'path.less' 引用, className = {styles.firstName}, 可通过：global设置全局样式



关于无状态组件

<!-- React 的设计哲学是 v=f(props)。props 是传入的属性；f 其实就是嵌套的函数，每个函数我们也称之为“组件”；v 是最终生成的 Virtual DOM。理论上。每个嵌套函数是纯函数，因此传给这些函数的参数如果是确定的，那么其结果（Virtual DOM 上的某个子分支）也是确定的。小的纯函数最终组合成 f，因此 f 也是一个纯函数，那么给定 props，整个 Virtual DOM 也是确定的。纯函数彻底抛弃了 Side Effect (外部作用)，因此非常利于测试，纯函数的组合也是如此，整个程序的状态可预测性也强，自然也就更健壮。至于 Side Effect (外部作用)，那么交给提供 Props 的人来处理吧/但是，工程上很难都这么理想。我们之所以要构造组件树（嵌套的函数），就是为了将复杂的 UI 分而治之。例如，一个 Tabs 组件可能要包含 N 个 Buttons 组件，用于点击切换 tab。那么问题来了，事件处理是应该在 Button 组件中处理的吗？如果放在 Button 组件中处理，那么 Button 就不是一个纯函数了，它的显示状态就不仅仅是依赖于传入的 Props (背景色、字体...)，同时依赖于用户按下/抬起鼠标的行为。如果我们想保持 Button 的 “纯”本色，那么显然事件处理不能放在 Button 组件中，要放在“所谓”的外部，可是这样做，又让我们的“分而治之”的目的流产了。所以在工程上，React 没有坚持一定要用纯函数来写组件（事实上纯函数写组件是 React 较后版本才提供的），而是允许我们写派生自 React.Component 的组件，这样不仅可以使用事件来改变内部状态（this.state）, 而且为我们提供组件生命周期的回调方法，让组件可以精确地和外部互动。因此我们很难说 React 是推崇无状态函数，只能说，React 推崇有意识地使用有状态组件。就像我们声明变量，如非必要就都加上 const 前缀，让 immutable 成为缺省。HoC 也是基于这个思想，将 Side Effect (外部作用)隔离出来，显示且单独地设计。 -->

React.createContext())