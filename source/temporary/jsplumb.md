https://zhuanlan.zhihu.com/p/41808577

1. 基本概念：

Source: 源对象。jsPlumb 通过元素的 id 属性获取对象。

Target: 目标对象。jsPlumb 通过元素的 id 属性获取对象。

Source 和 Target 都可以是任何元素，区别是，Source 是起点，Target 是终点。 例如，connector 中的箭头总是从 Source 指向 Target。

Anchor：锚点。是 Source 和 Target 对象上可以连接 Connector 的点。Anchor 并不是一个视觉概念，它是不可见的。

Connector: 连接线。

Endpoint: 端点。需要注意的是，箭头并不是一种端点样式，它是通过 overlay 添加的。

Overlay: 添加到连接线上的附件。例如箭头和标签。
