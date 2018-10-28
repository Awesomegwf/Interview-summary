###1.对于MVVM的理解
    Model:代表数据模型,也可以在Model中定义数据修改和操作的业务逻辑.
    View:代表UI组件,它负责将数据模型转化成UI展现出来.
    ViewModel:监听模型数据的改变和控制视图行为,处理用户交互,而Model数据的变化也会立即反映到View上.
    
    在MVVM架构下,View和Model之间并没有直接的联系,而是通过ViewModel进行交互,Model和ViewModel之间的交互是双向的,因此view数据的变化会同步到Model中,而Model数据的变化也会立即反映到View上.
    
    ViewModel通过双向数据绑定把view层和Model层链接起来,而View和Model之间的同步工作完全是自动的,无需人为干涉,因此开发者只需关注业务逻辑,不需要手动操作dom,不需要关注数据状态的同步问题,复杂的数据状态维护完全由MVVM来统一管理.
---
###2.Vue的生命周期
    beforeCreate(创建前)在数据观测和初始化事件还未开始.
    
    created(创建后)完成数据观测,属性和方法的运算,初始化事件,$属性还没有显示出来(载入前)在挂载开始之前被调用,相关的函数首次被调用.实例已经完成以下配置:编译模板,把里面的数据和模板生成,注意此时还没有挂载到页面上.(载入后)在新创建的el属性还没有显示出来.

    beforeMount(载入前)在挂载开始之前被调用,相关的render函数首次被调用.实例已完成以下配置:编译模板,把data里面的数据和模板生成html,注意此时还没有挂载html到页面上.

    mounted(载入后)在el被新创建的vm.$el替换,并挂载到实例上去之后调用,实例已完成以下配置:用上面编译好的html内容替换el属性指向的DOM对象,完成模板中的html渲染到html页面中,此过程中进行ajax交互.

    beforeUpdate(更新前)在数据更新之前被调用,发生在虚拟DOM重新渲染和打补丁之前,可以在该钩子中进一步地更改状态,不会触发附加的渲染过程.

    updated(更新后)在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用,调用时,组件DOM已经更新,所以可以执行依耐于DOM的操作.然而在大多数情况下,应该避免在从期间更改状态,因为这可能会导致更新无限循环.该钩子在服务器端渲染期间不被调用.

    beforeDestory(销毁前)在实例销毁之前调用,实例仍然完全可用.
    destory(销毁后)在实例销毁之后调用.调用后,所有的事件监听器会被移除,所有的钩子实例也会被销毁.该钩子在服务器端渲染期间不被调用.

    第一次页面加载会触发mounted(包括)之前的钩子.
---
###3Vue实现数据双向绑定的原理
    注:Object.defineProperty()方法会直接在一个对象上定义一个新属性,或者修改一个对象的现有属性,并返回这个对象.
    Vue实现双向数据绑定主要是:采用数据劫持结合发布者-订阅者模式的方式,通过Object.defineProperty()来劫持各个属性的setter,getter,在数据变动时发布 消息给订阅者,触发相应监听回调.当把一个普通js对象传给Vue实例来作为它的data选项时,Vue将遍历它的属性,用Object.defineProperty将它们转为getter/setter.用户看不到getter/setter,但是在内部它们让vue追踪依耐,在属性被访问和修改时通知变化.
    Vue的数据双向绑定将MVVM作为数据绑定的入口,整合Observer,Compile和Watcher三者,通过Observer来监听自己的model的数据变化,通过Compile来解析编译模板指令,最终利用watcher搭起observer和Compile之间的通信桥梁,达到数据变化->视图更新.视图交互变化(input)->数据model变更双向绑定效果.
````
<body>
    <div id="app">
    <input type="text" id="txt">
    <p id="show"></p>
</div>
</body>
<script type="text/javascript">
    var obj = {}
    Object.defineProperty(obj, 'txt', {
        get: function () {
            return obj
        },
        set: function (newValue) {
            document.getElementById('txt').value = newValue
            document.getElementById('show').innerHTML = newValue
        }
    })
    document.addEventListener('keyup', function (e) {
        obj.txt = e.target.value
    })
</script>
````
---
###4.Vue组件间参数传递
    父组件传给子组件:子组件通过props方法接受数据
    子组件传给父组件:$emit方法传递参数
###5.非父子组件间的数据传递,兄弟组件传值
    eventBus,就是创建一个事件中心,相当于中转站,可以用它来传递事件和接收事件.
###6.Vue的路由实现:hash模式和history模式
    has模式: 在浏览器中符号“#”，#以及#后面的字符称之为hash，用window.location.hash读取；特点：hash虽然在URL中，但不被包括在HTTP请求中；用来指导浏览器动作，对服务端安全无用，hash不会重加载页面。

    history模式
    history采用HTML5的新特性；且提供了两个新方法：pushState（），replaceState（）可以对浏览器历史记录栈进行修改，以及popState事件的监听到状态变更
###7.Vue路由的钩子函数
    首页可以控制导航跳转,beforeEach,afterEach等,一般用于页面title修改.一些需要登录才能调整页面的重定向功能:
    beforeEach:主要有3个参数to, from, next
    to: route即将进入的目标路由对象
    from: route当前导航正要离开的路由
    next: function一定要调用该方法resolve这个钩子,执行效果依耐next方法的调用参数.可以控制网页的跳转.
###8.Vue的计算属性
    在模板中放入太多的逻辑会让模板过重且难以维护,在需要对数据进行复杂处理,且可能多次使用的情况下,尽量采取计算属性的方式:好处
    (1)使得数据处理结构清晰
    (2)依耐于数据,数据更新,处理结果自动更新
    (3)计算属性内部的this指向vm实例
    (4)在template调用时,直接写计算属性名即可
    (5)常用的是getter方法,获取数据,也可以使用set方法改变数据.
    (6)相较于methods,不管依耐的数据变不变,methods都会重新计算,但是依耐数据不变的时候computed从缓存中获取,不会重新计算.
###9.$route和$router的区别
    $route是“路由信息对象”，包括path，params，hash，query，fullPath，matched，name等路由信息参数。而$router是“路由实例”对象包括了路由的跳转方法，钩子函数等。
    keep-alive是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染
###10.vue-cli如何新增自定义指令？
````
var app = new Vue({
    el: '#app',
    data: {    
    },
    // 创建指令(可以多个)
    directives: {
        // 指令名称
        dir1: {
            inserted(el) {
                // 指令中第一个参数是当前使用指令的DOM
                console.log(el);
                console.log(arguments);
                // 对DOM进行操作
                el.style.width = '200px';
                el.style.height = '200px';
                el.style.background = '#000';
            }
        }
    }
})

//全局指令
Vue.directive('dir2', {
    inserted(el) {
        console.log(el);
    }
})
````

###11Vue与Angular以及React的区别？
    1.与AngularJS的区别 
      相同点：
      都支持指令：内置指令和自定义指令；都支持过滤器：内置过滤器和自定义过滤器；都支持双向数据绑定；都不支持低端浏览器。

      不同点：
      AngularJS的学习成本高，比如增加了Dependency Injection特性，而Vue.js本身提供的API都比较简单、直观；在性能上，AngularJS依赖对数据做脏检查，所以Watcher越多越慢；Vue.js使用基于依赖追踪的观察并且使用异步队列更新，所有的数据都是独立触发的。

    2.与React的区别
      相同点：
      React采用特殊的JSX语法，Vue.js在组件开发中也推崇编写.vue特殊文件格式，对文件内容都有一些约定，两者都需要编译后使用；中心思想相同：一切都是组件，组件实例之间可以嵌套；都提供合理的钩子函数，可以让开发者定制化地去处理需求；都不内置列数AJAX，Route等功能到核心包，而是以插件的方式加载；在组件开发中都支持mixins的特性。
      不同点：
      React采用的Virtual DOM会对渲染出来的结果做脏检查；Vue.js在模板中提供了指令，过滤器等，可以非常方便，快捷地操作Virtual DOM。
###12 vuex是什么？怎么使用？哪种功能场景使用它？
      只用来读取的状态集中放在store中； 改变状态的方式是提交mutations，这是个同步的事物； 异步逻辑应该封装在action中。
      在main.js引入store，注入。新建了一个目录store，….. export 。
      场景有：单页应用中，组件之间的状态、音乐播放、登录状态、加入购物车