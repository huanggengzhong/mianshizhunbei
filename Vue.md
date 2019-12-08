#### Vue生命周期
  Vue生命周期的第一个钩子函数是beforeCreate,在这个生命周期函数里还获取不到props和data中的数据的,因为这些数据的初始化还在initState当中,实际的用途是我们可以用来做loading加载效果;
  接着会执行created钩子函数,在这一步中可以访问到之前不能访问到的数据,这里可以发异步请求,但是这个时候组件是还没有挂载,所以是看不到的;
  接下来会执行beforeMounted钩子函数,开始创建VDOM,最后执行mounted钩子函数,并将VDOM渲染为真实的DOM并且渲染数据.如果组件中有子组件的话,会递归挂载子组件,当所有的子组件全部加载完毕,才会执行根组件的mounted挂载钩子(如下图渲染过程);

![1574676979194](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574676979194.png)

接下来是数据更新时会调用的钩子函数,它有两个beforeUpdate和updated,这个两个钩子没什么好说的,分别代表数据更新前和数据更新完成后执行的构子;

另外如果使用了keep-alive组件,那它还有两个独有的生命钩子函数,分别是actived和deactived.用keep-alive包裹的组件在路由切换的时候不会进行销毁,而是缓存到内存中并执行,当引入keep-alive的时候,页面第一次进入时会触发created-mounted-actived,退出时执行deactived,当再次进入或者后退,只执行actived钩子函数;

最后就是销毁组件的钩子函数beforeDestroy和destroyed,beforeDestroy适合移除事件,定时器等,没有移除的话可以能引起内存泄漏的问题,在这里执行一系列的销毁操作,如果有子组件也会递归销毁子组件,当所有的组件包括子组件销毁完毕之后才会执行最后一个destroyed钩子函数.



2019年12月8日21:34:13未完待续