# 踩坑心得



## Element-UI



#### MessageBox 组件自定义显示内容

有时候我们希望确认弹窗的提示内容可进一步自定义，比如显示一个列表，显示不同的颜色及字体大小等等。

这时候需要用到MessageBox.confirm方法，该方法有三个参数，分别是message,title和options，其中，message是必填的，但可以options中覆盖其内容，下面是代码演示：

```javascript
const h = this.$createElement
MessageBox.confirm('该信息不会被显示', {
  title: `归档确认`,
  message: h('div', {'class': 'confirm-con'}, [
    h('h4', {'class': 'tit'}, warning),
    h('div', {'class': 'msg'}, `确认归档这6位客户?`)
  ]),
  confirmButtonText: '确定',
  cancelButtonText: '取消'
})
```



#### Form 表单验证

1. 如果form绑定的数据为动态生成，那么rules（验证规则）必须在其之后声明；
2. 我们项目中要注意表单字段（attr）对应的validate的属性值，同个字段后端可能传来不一样的值，配置验证的时候要注意


#### 组件命名冲突

在工单详情中有个“工单进展”组件，随手命名为 `Progress` ，然后发现浏览器报了一个错误：`Do not use built-in or reserved HTML elements as component id: Progress`。原因是Element-UI中也有个 `Progress`组件，而这个组件是全局注册的，从而导致了组件命名冲突。

关于组件的命名规范，可以看一下这篇拓展阅读：[聊聊 Vue 组件命名那些事](http://cnodejs.org/topic/5816aabdcf18d0333412d323)



#### 引入组件路径，注意区分大小写

1. 如果路径中错写了大小写，具体表现为组建加载正常，但是修改组件内容不会触发更新；
2. Linux中严格区分大小写，如果项目中错写了大小写路径，发布到线上后会导致引用错误。



## Javascript





## Vuejs

#### 父子组件之间的传参

1. 如果父组件传给子组件的值是数组或对象，即使把参数赋给子组件的一个变量，子组件的变量变也会影响父组件，如果不想影响父组件，最好在赋值前对参数进行深度拷贝，方法有JSON.parse(JSON.stringify())。字符串、数字类型和布尔值不会变。
2. 父子组件之间传参，如果参数是数组和对象，最好用工厂函数来设置默认值。
 
#### 动态引入图片

img标签动态绑定静态src地址时，图片不能正常显示，解决办法用require引入地址（做应用中心轮播时有碰到）
 
<img :src='imgUrl'>
...
data() {
  return {
    imgUrl: require('./img.png')
  }
}

#### 对象响应式

Vue中的属性如果是Object，或者是数组，数组中有Object，那么这些Object最好在最开始就把所有需要用到的属性都定义一遍，如果在运行中重新添加属性，这个属性并不是响应式的，就不会实现双向绑定，例如：
const vm = new Vue({
  data: {
    a: {
      text: 'aaa'
    }
  }
})
vm.a.b = 'ccc'
这样的情况，a的b属性不是响应式的，所以不会双向绑定（）
（之前在给列表另外加属性控制列表的展开和收起的时候，是在一开始拿到数据的时候就遍历一遍，把属性加上，不然动态改变这个属性是没有用的）
 
#### ref问题(新建的时候如果同样的控件多次使用，用到$refs时需要遍历)
<ul>
  <li ref="text">dfj</li>
  <li ref="text">dfj</li>
  <li ref="text">dfj</li>
</ul>
这样写的话this.$refs.text获取的是最后一个li

<ul>
  <li ref="text" v-for="i in arr">dfj</li>
</ul>
这样写的话this.$refs.text获取的是数组

## Vuex





## Vue-router

一.路由传参  query 与 params 区别（应用于跳转界面并传参）
1.用法上
this.$router.push({
    path: 'jxcAddPage',
    query: {
      moduleName: 'purchase',
      id: ''
    }
})
this.$router.push({
    name: 'jxcAddPage',
    params: {
      moduleName: 'purchase',
      id: '',
    }
})
接收参数都是类似的，分别是...this.$route.query.name...和...this.$route.params.name...
注意接收参数的时候，已经是...$route...而不是...$router...了

2.展示上
query传参会在浏览器地址栏中显示参数，params则不显示
...http://localhost:8080/#/jxcAddPage?moduleName=instock&id=395...
...http://localhost:8080/#/jxcAddPage...

## 项目页面坑点
（列表页）
 如果是新增一个列表页：
 
1.需要在'src/config/list_config'至少设置一项最小列宽，正常来说一般关于名称的都会设置个宽度，毕竟默认的宽度很窄，以待采购列表为例，
在list_config 里的COLMUN_WIDTH这个对象里添加一个对象，对象名waitPurchaseApi为模块名，对象里每个属性与列表项的attr名对应，后面的是宽度值
  // 待采购
```
  waitPurchaseApi: {
		productUnit: 80,
		supplierJoinName: 180
  }
```
  
 2.操作列的按钮需要在'src/config/common_config'里配置好，这里要注意后端传过来的每条数据里的listPermissionSet值，它是个数组，用来告诉你每条数据会用到的操作按钮，比如```listPermissionSet:["offline", "edit", "del"]```，然后common_config里menuType对象里没有offline的话就往里面加一个关于offline的对象，对象里会包含：name（按钮名称），type按钮样式（element UI）
 ```
  offline: {
    name: '下架',
    type: 'warning'
  }
```
 之所以记录下这个问题是因为我当时在common_config添加了但是页面还是报错，最后才发现后端这个数据传的有问题。





