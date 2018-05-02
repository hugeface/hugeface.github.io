---
title: React的ref属性
date: 2018-04-30 16:19:50
tags: [React, ref]
---

# 前言
 **react典型的数据流**：通过传递一个新的props值来使子组件重新re-render

**ref方式**是除react典型数据流之外，实现修改子组件的另一种方式，这种方式适合与==第三方DOM库的整合，或者某个DOM元素Focus==

<!-- more -->

# ref简介

React提供ref属性，表示为对组件真正实例的引用，就是ReactDOM.render()返回的组件实例。---- ReactDOM.render()渲染组件时返回的是组件实例；而渲染dom元素时，返回是具体的DOM节点。可以使用以下代码观察输出：

```JSX
const domCom = <button type="button">button<[表情]tton>;
const refDom = ReactDOM.render(domCom，container);
    //ConfirmPass的组件内容省略
const refCom = ReactDOM.render(<ConfirmPass/>,container);
console.log(refDom);
console.log(refCom);
```

ref可以挂到任何组件上，可以挂到组件上也可以是DOM元素上：挂到组件（特指有状态组件）上的ref表示对组件实例的引用；挂在DOM元素上时表示具体的DOM元素节点。

==**注意：无状态组件不会被实例化，因此无法使用ref获取组件实例，其值为null。**==

对于无状态组件，想访问的无非是其中包含的组件或者DOM元素，可以通过一个变量来保存想要的组件或者DOM元素组件的实例引用。例如下面代码：

```
function TestComp(props){
    let reDom;
    return (
        <div>
            <div ref={(node) => refDom = node}>
                ...
            </div>
        </div>
    );
}
```

这样，可以通过变量refDom来访问无状态组件中的指定DOM元素了。

# ref的使用

#### ref可以设置回调函数

官方推荐将ref属性设置为一个回调函数，这个函数执行的时机为：

- 组件被挂载后，回调函数被立即执行，回调函数的参数为该组件的具体实例。
- 组件被卸载或者原有的ref属性本身发生变化时，回调函数也会被立即执行，此时回调函数参数为null，确保内存不会泄露

可运行下方代码加深了解：
```JSX
RegisterStepTwo = React.createClass({
    getInitialState(){
      return {visible: true};
    },
  changeVisible(){
    this.setState({visible: !this.state.visible});
  },
  refCb(instance){
    console.log(instance);
  },
  render(){
    return(
      <div>
        <button type="button" onClick={this.changeVisible}>{this.state.visible ? '卸载' : '挂载'}ConfirmPass
        <[表情]tton>
        {
          this.state.visible ?
            <ConfirmPass ref={this.refCb} onChange={this.handleChange}/>: null
         }
       </div>
     )
  }
});
```

#### ref可以设置字符串

这种方式基本不推荐使用，或者在未来的React版本中不会再支持该方式。

```JSX
<input ref="input" />
```

在其他地方，如事件回调中通过this.refs.input 可以访问到该组件实例：

```
let inputEl = this.refs.input;
// TODO - 通过inputEl完成后续逻辑，如Focus、值获取等等
```

<hr />

不管ref设置的值是回调函数还是字符串，都可以通过ReactDOM.findDOMNode(ref)来获取组件挂载后真正的DOM节点。

但是对于HTML元素使用ref的情况，ref本身引用的就是该元素的实际DOM节点，无需使用ReactDOM.findDOMNode(ref)来获取，该方法常用于React组件上的ref。

# 总结

ref提供了一种对于react标准的数据流不太适用的情况下组件间交互的方式，例如管理dom元素focus、text selection以及与第三方的dom库整合等等。 但是在大多数情况下应该使用react响应数据流那种方式，不要过度使用ref。

另外，在使用ref时，不用担心会导致内存泄露的问题，react会自动帮你管理好，在组件卸载时ref值也会被销毁。

最后：不要在组件的render方法中访问ref引用，render方法只是返回一个虚拟DOM，这是组件不一定挂载到DOM中，或者render返回的虚拟DOM不一定会更新到DOM中。


# 参考

- [React之ref详细用法](https://segmentfault.com/a/1190000008665915)
- [React创建组件的三种方式及其区别](http://www.cnblogs.com/wonyun/p/5930333.html)
- [React从入门到精通系列之(14)refs和DOM元素](https://segmentfault.com/a/1190000007815434)
- [对组件的引用(refs)](http://bbs.reactnative.cn/topic/608/%E5%AF%B9%E7%BB%84%E4%BB%B6%E7%9A%84%E5%BC%95%E7%94%A8-refs/2)
