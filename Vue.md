## inheritAttrs
是否允许组件根元素继承 attribute。

```
true: 继承，默认
false: 不继承
```

为什么会出现这个属性，在 Vue 里面，未声明为 props 的属性会传递到组件的根元素上，但如果传入不恰当，可能达到不可预期的效果，比如下面的组件
```
Vue.component('my-component', {
  template: '<input type="text" placeholder="请输入">'
})
```
`<my-component type="date"></my-component>`，这么使用的时候，会渲染出一个日期选择的输入框，type 被我们替换了，为了避免发生这种情况，我们
可以将组件的`inheritAttrs`设置为 false。

另外，就是 class、style 不会受 inheritAttrs 的影响，还是会进行正确的合并。
