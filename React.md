###1.react生命周期
    1).初始化阶段:
      getDefaultProps:获取实例的默认属性
      getInitialState:获取每个实例的初始化状态
      componentWillMount:组件即将被装载,渲染到页面上.
      render:组件在这里生成虚拟的DOM节点.
      componentDidMount:组件真正在被装载之后.
    2)运行中状态:
      componentWillReceiveProps:组件将要接收到属性的时候调用.
      shouldComponentUpdate:组件接受到新属性或者新状态的时候(可以返回false,接收数据后不更新,阻止render调用,后面的函数不会被继续执行了)
      componentWillUpdate:组件即将更新不能修改属性和状态.
      render:组件重新描绘
      componentDidUpdate:组件已经更新
    3)销毁阶段:
      componentWillUnmount:组件即将销毁
###2.react性能优化是哪个周期函数?
    shouldComponentUpdate这个方法用来判断是否需要调用render方法重新描绘dom.因为dom的描绘非常消耗性能,如果我们能在shouldComponentUpdate方法中能够写出更优化的dom diff算法,可以极大的提高性能.
###3.为什么虚拟dom会提高性能?
    虚拟dom相当于在js和真实dom中间加了一个缓存,利用dom diff算法避免了没有必要的dom操作,从而提高性能.
    具体实现步骤如下:
    1.用JavaScript对象结构表示DOM树的结构;然后用这个树构建一个真正的DOM树,插到文档当中.
    2.当状态变更的时候,重新构造一棵新的对象树.然后用新的树和旧的树进行比较,记录两棵树差异.
    把2所记录的差异应用到步骤1所构建的真正的DOM树上,视图就更新了.
###4.diff算法
    把树形结构按照层级分解,只比较同级元素.
    给列表结构的每个单元添加唯一的key属性,方便比较.
    React只会匹配相同class的component(这里class指的是组件的名字)
    合并操作,调用component的setState方法的时候,React将其标记为dirty,到每一个事件循环结束,React检查所有标记dirty的component重新绘制.
    选择性子树渲染.可以重写shouldComponentUpdate提高diff的性能.
###5.react性能优化方案
    1)重写shouldComponentUpdate来避免不必要的dom操作
    2)使用production版本的react.js
    3)使用key来帮助React识别列表中所有子组件的最小变化.
###6.调用setState后发生了什么?
    在代码中调用setState函数之后,React会将传入的参数对象与组件当前的状态合并,然后触发所谓的调和过程(Reconciliation).经过调和过程,React会以相对高效的方式根据新的状态构建React元素树并且着手重新渲染整个UI界面.在React得到元素树之后,React会自动计算出新的树与老树的节点差异,然后根据差异对界面进行最小化重渲染.在差异计算算法中,React能够相对精确地知道哪些位置发生了改变以及应该如何改变,这就保证了按需更新,而不是全部重新渲染.
###7.React中Element与Component的区别是?
    React Element是描述屏幕上所见内容的数据结构,是对于UI的对象表述,典型的React Element是利用JSX构建的声明式代码片段然后转化为createElement的调用组合,而React Component则是可以接收参数输入并且返回某个React Element的函数或者类.
###8.在什么情况下你会选择使用Class Component而不是Function Compinent?
    在组件需要包含内部状态或者使用到生命周期函数的时候使用Class Component,否则使用函数式组件.
###9.React中refs的作用是什么?
    Resfs是React提供给我们的安全访问DOM元素或者某个组件实例的句柄.我们可以为元素添加ref属性然后在回调函数中接受该元素在DOM树中的句柄,该值会作为回调函数的第一个参数返回:
###10.React中keys的作用是什么?
    keys是React用于追踪哪些列表中元素被修改,被添加或者被移除的辅助标识.
    