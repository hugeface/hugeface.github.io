---
title: React从子组件到父组件的关联渲染
date: 2018-4-27 13:09:50
tags: [React]
---

开发过程中会遇到这样的场景：组件A是组件B的父组件，当用户对组件B进行操作时，需要组件A进行响应的状态或者展示效果改变。一般情况下，我们会用React的action达到所需要的效果，
<!-- more -->
除此还可以用回调函数实现，在组件复用场景下，回调可能是更为轻便的实现。如下代码所示：

```jsx
// Child Component
const optFuc = () => { this.props.optFuc; }

// Parent Component
<Child onOperation={optFuc} />
```
