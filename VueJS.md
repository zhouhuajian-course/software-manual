# VueJS

1. createApp 传的是 根组件，其他组件为子组件，组件有点像类，组件实例有点像类创建的对象
2. 同名简写
3. createApp 创建出来的 app 叫应用，和 App.vue 的 App 不用，这个是 根组件
4. <div id="app"></div>，这个div元素叫容器元素，这个容器元素本身将不会被视为应用的一部分，这里面的内容innerHTML，会成为根组件的template模板，跟写在组件的template效果一样
5. 指令名:参数，Vue指令 冒号 后面的是 指令的参数
6. <a v-bind:href="url"> ... </a> 的 v-blind: 简写为 :，省略指令名是，默认为v-blind
7. <a v-on:click="doSomething"> ... </a> 的 v-on: 简写为 @，省略指令名v-on，是@时 默认为v-on，参数为click
8. <template> 只是一个不可见的包装器元素，最后渲染的结果并不会包含这个 <template> 元素。
9. 关于 v-for 里面的 key，只展示可不加key，需要动态修改，需要加， 推荐使用数据的唯一标识作为key，比如id，身份证号，手机号等等，通常这些数据由后端提供。后续操作不破坏原来数据顺序的话，使用index作为key也没有任何问题。
10. 事件处理，模板编译器会通过检查 v-on 的值是否是合法的 JavaScript 标识符或属性访问路径来断定是何种形式的事件处理器。举例来说，foo、foo.bar 和 foo['bar'] 会被视为方法事件处理器，而 foo() 和 count++ 会被视为内联事件处理器。
11. 你会注意到，每当点击这些按钮时，每一个组件都维护着自己的状态，是不同的 count。这是因为每当你使用一个组件，就创建了一个新的实例。
