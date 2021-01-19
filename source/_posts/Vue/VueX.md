---
title: Vue - VueX 学习手记
tags:
  - Vue
categories: []
date: 2021-01-15 17:15:00
updated: 2021-01-15 17:15:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611037467/vuex_uetuhy.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611037467/vuex_uetuhy.png
---

### 一、序言

本篇文章更像是我学习vuex的一个笔记，学习的资源主要是来自官方文档教程，官方教程已经讲的比较细致了，部分地方也有自己不理解的地方，所以也查过其他资料来辅助自己理解，本手记在官方的教程上加了一些自己的补充内容，希望能给你带来一些参考价值，另外也感谢互联网上其他分享知识的大佬，让我少走了些弯路！如果文章有理解不到位的地方，还请各位多批评指正！

---
### 二、Vuex之初体验
---
##### 为何使用Vuex
使用Vue开发的过程中，我们经常会遇到一个状态可能会在多个组件之间使用，比如我们在做项目时使用到的用户的信息，什么昵称、头像这些，这些信息会在不同的组件用到，一旦改变这些状态，我们希望其他组件也跟随变化，比如用户充值了100元，或者改变了昵称，所以这个时候就需要状态管理模式来集中管理，关于Vuex的详细介绍可以移步到官网。
##### 学习之前的准备
本次我的学习都是在官方提供的脚手架搭建的项目下学习的，关于脚手架的使用本次就不再赘述，可以移步到Vue CLI，在使用Vue CLI生成的项目时会让你选择store，选择了后会在页面给你生成一个store.js，这就是最初的store，里面结构如下：
```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)

export default new Vuex.Store({
  state: {
  },
  mutations: {
  },
  actions: {
  }
})
```
### 三、State
Vuex的核心就是仓库store，这个store实例会被注入到所有子组件里面，里面的state属性保存着我们的状态，比如我们定义一个状态count：
```js
export default new Vuex.Store({
  state: {
    count: 10
  },
})
```
这样我们就有一个集中管理的状态count，那其他组件如何取到这个count呢，可以计算属性来获得：
```js
export default {
  data() {
    
  },
  computed: {
    count() {
      return this.$store.state.count;
    }
  }
}
```
因为根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 this.$store 访问到。通过计算属性，我们就可以在模板里面使用模板语法来调用count了，如下：
```html
<template>
  <div>
    <p>{{ count }}</p>
  </div>
</template>
```
mapState有时候需要获取多个状态，但是使用计算属性会调用多次，显得麻烦，这里借助mapState方法来获取state。
使用mapState需要引入该方法
```js
import { mapState } from 'vuex';
```
注意：这里使用了mapState方法后，computed的写法有点区别，比如默认情况你的computed属性是这样写的：
```js
data(){
  return{
    msg: 'hello '
  }
}
computed: {
  msg() {
    return this.msg + 'world!';
  }
}
```
那么你使用了mapState后需要这样写computed，把msg()放入mapState，不然会报错。
```js
data(){
  return{
    msg: 'hello ',
    localCount: 20
  }
}
computed: mapState({
  msg() {  // 最初的
    return this.msg + 'world!';
  },
  // 使用mapState从store中引入state
  count(state) {
    return state.count;
  },
  name(state) {
    return state.firstName + ' ' + state.lastName;
  },
  mixCount(state) { // 结合store和组件状态进行计算
    return state.count + this.localCount;
  },
})
```
如果你使用了展开运算符...，那么computed属性不需要改造，按正常写法写
```js
computed: { // 使用展开的话可以按这种方式写，否则要使用另外一种方式，不然要报错
  msg() {
    return this.$store.state.msg;
  },
  // 这里返回一个状态count，
  // 返回多个你可以这样写...mapState(['count', 'firstName', 'lastName'])
  ...mapState(['count'])
},
```
### 四、Getter
getter就是对状态进行处理的提取出来的公共部分，当状态要进行筛选这些操作时，我们可以通过getter处理过后再返回给组件使用，比如我们在state部分定义了一个list数组：
```js
export default new Vuex.Store({
  state: {
    list: [1, 2, 3, 4, 5, 6, 7, 8]
  },
});
```
我们想要筛选出数组里面的偶数然后再在组件里面使用，那么筛选的这个工作可以放在getter里面来完成。
```js
export default new Vuex.Store({
  state: {
    list: [1, 2, 3, 4, 5, 6, 7, 8]
  },
  getters: { //  这个主要是对状态的处理，相当于把状态处理的方法抽成公共部分来管理了
    modifyArr(state) { // 一般化getter
      return state.list.filter((item, index, arr) => {
        return item % 2 == 0;
      })
    },
    getLength(state, getter) { // 方法里面传getter，调用modifyArr来计算长度
      return getter.modifyArr.length;
    }
});
```
之后再在其他组件的computed里面去调用getter来获取想要的状态
```js
computed: {
    list() {
      return this.$store.getters.modifyArr;
    },
}
```
mapGetters
mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性，当我们想在组件里面引入多个getter时，可以使用mapGetter：
```js
import {mapGetters} from 'vuex';
```
比如像刚才在在上面定义的modifyArr，getLength。我们想引入这个两个并获取其值：
```js
computed: {
  ...mapGetter(['modifyArr', 'getLength'])
}
```
你当然可以为其指定别名，不一定非得用store里面getters定义的名字：
```js
computed: {
  mapGetter({
    arr: 'modifyArr',   // 把 `this.arr` 映射为 `this.$store.getters.modifyArr`,下面同理
    length: 'getLength'
  })
}
```
如果你的computed属性包含其他计算方法，那你就只能使用展开运算符的写法，这里跟mapState有点区别，其他计算属性如果写在mapGetter里面会报错，说不存在的getter，所以用以下的写法：
```js
computed: {
  msg() {
    return this.num * 10;
  },
  ...mapGetters([
    'modifyArr',
    'getLength'
  ])
}
```
或者指定别名
```js
computed: { 
  msg() {
    return this.num * 10;
  },
  ...mapGetters({
    getList: 'modifyArr',
    length: 'getLength'
  })
}
```
然后再模板里面调用：
```html
<template>
  <div>
    <h2>mapGetters的使用演示</h2>
    <p>你的数字：{{ msg }}</p>
    <p>你的数组长度为：{{ length }}</p>
    <ul>
      <li v-for="(item, index) in getList" :key="index">{{ item }}</li>
    </ul>
  </div>
</template>
```
### 五、Mutation
当我们需要修改store里面的状态时，我们不是在组件里面直接去修改它们，而是通过mutation里面的方法来进行修改，这样有利于追踪状态的改变。
比如state里面有一个count变量，我们点击加减按钮来控制它的值：
```js
mutations: {
  add(state) {
    state.count++;
  },
  reduce(state) {
    state.count--;
  }
},
```
在其他组件里面，我们通过定义methods并绑定时间来触发改变，这里需要使用commit：
```js
methods: {
  add() {
    this.$store.commit('add');
  },
  reduce() {
    this.$store.commit('reduce');
  }
}
```
提交载荷
这个就是在commit时提交额外的参数，比如我传了额外的值加到count上面：
```js
mutations: {
  loadAdd(state, payload) {  // 提交载荷，额外参数
    state.count += payload;
  }
},
```
然后再组件里面使用：
```js
methods: {
  loadAdd() {
    this.$store.commit('loadAdd', 100); // 传递额外参数
  }
}
```
再这里官方文档建议载荷（也就是那个额外参数）最好使用对象来传，这样可以包含多个字段并且记录的 mutation 会更易读，像这样：
```js
this.$store.commit('loadAdd', {
  extraCount: 100
}); // 传递额外参数
```
调用commit时我们也可以把所有参数写在一个对象里面：
```js
this.$store.commit( {
  type: 'addLoad'
  extraCount: 100
}); // 传递额外参数
```
Mutation 需遵守 Vue 的响应规则
这个主要是说你再开发过程中需要向state里面添加额外数据时，需要遵循响应准则。
这里我直接照搬官方文档的说明：
Vuex 中的 mutation 也需要与使用 Vue 一样遵守一些注意事项：


最好提前在你的 store 中初始化好所有所需属性。


当需要在对象上添加新属性时，你应该使用 Vue.set(obj, 'newProp', 123), 或者以新对象替换老对象。例如，利用 stage-3 的对象展开运算符


我们可以这样写：
```js
state.obj = { ...state.obj, newProp: 123 }
```
还是举个栗子：
我在mutation里面声明了一个方法
```js
addNewState(state, payload) { // 我打算再这儿添加新的属性到state
  // Vue.set(state, 'newProp', '添加一个新值！'); // 这是一种写法
  // 这种写法用新对象替换老对象
  // state= {...state, newProp: '添加一个新值！'} // 这个玩意儿不管用了，用下面的replaceState()方法
  this.replaceState({...state, newProp: '添加一个新值！'})
}
```
然后再组件里面去调用，定义一个method：
```js
addNewProp() {
  this.$store.commit('addNewState', {});
}
```
这样再执行了这个方法后，会及时更新到state，再组件的computed属性里面定义：
```js
newMsg() {
  return this.$store.state.newProp || '还没添加新值';
}
```
在模板里面即时展示，并且不会影响其他状态：
```html
<p>添加的新值：{{ newMsg }}</p>
<div><button @click="addNewProp">添加新值</button></div>
```
Mutation 必须是同步函数
下面这种写法必须避免（直接官方例子加持）：
```js
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```
mapMutations
这个跟前面的那几个函数一个道理，都是为了简化调用，使用方法如下：
import {mapMutations} from 'vuex';
然后在组件的methods里面使用，这里使用官方代码：
```js
export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```
### 六、Action
Action 类似于 mutation，不同在于：

Action 提交的是 mutation，而不是直接变更状态。
Action 可以包含任意异步操作。
前面说过mutation只能包含同步事务，所以在处理异步事务就需要Action，通过Action控制了异步这一过程，之后再去调用mutation里面的方法来改变状态。
这里我直接贴代码来一目了然，首先我定义了一个状态product：
```js
state: {
  product: 'car'
}
然后再mutation中定义一个方法：
changeProduct(state, payload) {
  state.product = payload.change;
}
在action中定义：
actions: {
  changeProduct(context, payload) { // 这个context是一个与 store 实例具有相同方法和属性的对象
    // 调用mutation里的changeProduct方法
    // context.commit('changeProduct', {change: 'ship'});
    // 改成异步方式
    // setTimeout(() => {
    //   context.commit('changeProduct', {change: 'ship'});
    // }, 1500)
    // 使用载荷
    let temp = 'ship+' + payload.extraInfo; 
    setTimeout(() => {
      context.commit('changeProduct', {change: temp});
    }, 1500)
  }
}
```
在组件methods中定义事件触发分发：
```js
methods: {
  selectProduct() {
    // this.$store.dispatch('changeProduct')
    // 载荷方式分发
    // this.$store.dispatch('changeProduct', {
    //   extraInfo: 'sportcar'
    // })
    // 或者这种
    this.$store.dispatch({
      type: 'changeProduct',
      extraInfo: '->sportcar'
    })
  }
},
```
这样一个简易的action就完成了！
mapActions
这里就不再赘述了，看名字就知道跟前面几个叫map开头的辅助函数类似，用来映射action里面的方法，这里也直接贴官方代码了：
```js
import {mapActions} from 'vuex';
export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```
有时候我们想知道action里面异步执行后的状态然后再去修改其他信息，这个可以借助Promise来实现。这里在state里面声明一个状态：
```js
state: {
  userInfo: { // 这个变量用来测试组合变量
    name: 'lee',
    age: 23
  }
}
```
接着声明mutation:
```js
mutations: {
    // 以下用来测试组合action
    changeInfo(state, payload) {
      state.userInfo.name = 'lee haha';
    }
}
```
声明action：
```js
actions: {
  changeInfo(context, payload) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        context.commit('changeInfo');
        resolve();
      }, 2000)
    })
  }
}
```
这时我们在组件里面定义方法去派发这个action：
```js
data() {
  return {
    status: '信息还没修改！'
  }
}
methods: {
  modifyInfo() {
    this.$store.dispatch('changeInfo').then(() => {
      this.status = '信息修改成功';
    });
  }
}
```
模板展示：
```html
<template>
  <div>
    <h2>组合action</h2>
    <p>{{ status }}</p>
    <p>{{ info.name }}</p>
    <div><button @click="modifyInfo">修改信息</button></div>
  </div>
</template>
```
当我们点击修改信息后，就会派发action，当修改成功的时候会同步修改上面说的展示信息。
当然其他定义的action方法也可以互相使用，这里直接贴官方代码了：
```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  },
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```
### 七、Module
模块这部分正如其名，当所有状态集中在一个对象中时，会变的相当臃肿，这个时候就需要模块的管理办法。这里我还是用代码来说明，比如我在store里面定义了两个模块：
```js
// 定义的模块A
const moduleA = {
  state: {
    name: 'lee',
    age: 23,
  },
  mutations: {

  },
  getters: {

  },
  actions: {

  }
};

// 定义模块B
const moduleB = {
  state: {
    name: 'wang',
    age: 22
  },
  mutations: {

  },
  getters: {

  },
  actions: {

  }
}
```
然后再Vuex里面声明模块：
```js
export default new Vuex.Store({
  modules: {
    ma: moduleA,
    mb: moduleB
  },
  state: {
    ........... // 其他状态
  }
});
```
这样一来，如果我们想要在组件里面访问其他模块的状态，可以这样，比如这里我想调用B模块里的状态：
```js
computed: {
  msg() {
    return this.$store.mb; // 这里返回的是：{name: 'wang', age: 22}
  }
}
```
关于模块内部的局部状态，这里跟普通的store用法没有多大的区别，主要区别以下外部传进来的状态，比如对于模块内部的 action，局部状态通过 context.state 暴露出来，根节点状态则为 context.rootState，这里截取官方代码：
```js
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```
对于模块内部的 getter，根节点状态会作为第三个参数暴露出来：
```js
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```
那么对于getters、mutations、actions里面的方法我们像基本的store那样调用就可以了，不存在作用域限制，还是贴代码栗子吧，下面是我在store.js里面定义的模块B：
```js
const moduleB = {
  state: {
    name: 'wang',
    age: 22,
    desc: 'nope'
  },
  mutations: {
    modifyDesc(state, payload) {
      state.desc = payload.newMsg;
    }
  },
  getters: {

  },
  actions: {

  }
}
```
在组件里面，我定义了以下内容：
```html
<template>
  <div>
    <h2>7、module使用示例</h2>
    <div>
      <p>名字：{{ name }}</p>
      <p>描述：{{ desc }}</p>
      <button @click="handleClick">修改描述</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: this.$store.state.mb.name,
      // desc: this.$store.state.mb.desc 注意这个如果涉及到要在store里面会被改变的状态，一定要写在
      // computed属性里面，不然不能及时反馈到视图上
    }
  },
  computed: {
    desc() {
      return this.$store.state.mb.desc;
    }
  },
  methods: {
    handleClick() {
      this.$store.commit('modifyDesc', {newMsg: 'lao wang is beautiful!'});
    }
  },
}
</script>
```
这样，就可以调用mutation里面的方法了，getters和actions同理


命名空间模块
默认情况下，mutations、actions、getters这些都是注册在全局上面的，你可以直接调用，如果希望你的模块具有更高的封装度和复用性，你可以通过添加 namespaced: true 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。
首先我新建一个js文件用来声明模块C：
```js
/* 
* 这个文件用来声明模块C
*/

export const moduleC = {
  namespaced: true,
  state: {
    name: 'moduleC',
    desc: '这是模块C，用来测试命名空间的！',
    list: [1, 2, 3, 4]
  },
  getters: {
    filterList(state) {
      return state.list.filter((item, index, arrSelf) => {
        return item % 2 !== 0;
      });
    }
  },
  mutations: {
    modifyName(state, payload) {
      state.name = payload.newName;
    }
  },
  actions: {
    
  }
}
```
然后在store.js里面引入：
```js
import { moduleC } from './module_c.js';

export default new Vuex.Store({
  modules: {
    mc: moduleC
  },
});
```
要想这个模块成为带有命名空间的模块，在上面声明属性namespaced: true就可以了，那么里面的mutations、getters和actions里面的方法的调用就要多走一层路径，比如我在组件里面去调用mutations里面的方法（getters和actions同理）：
```js
methods: {
  modify() {
    // this.$store.commit('mc/modifyName', {
    //   newName: '命名空间模块C'
    // })
    this.$store.commit({
      type: 'mc/modifyName',
      newName: '命名空间模块C'
    })
  }
}
```
当然模块里面再嵌套模块也可以，路径要不要多走一层主要看你的namespaced: true有没有声明，这里贴一下官方的代码：
```js
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: { ... }, // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: { ... },
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: { ... },
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```
在带命名空间的模块内访问全局内容
如果想要在模块内部的getters、mutations和actions里面访问到全局的内容，这儿Vuex已经封装好了，你只需要多传几个参数即可。官方演示来一波，简单明了：
```js
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```
在模块里面使用辅助函数mapState、mapGetters、mapMutations和mapActions
由于存在命名空间，在组件里面采用上面的写法会出现问题，这里要想使用辅助函数来映射模块里面的东西需要指定空间名称来告诉辅助函数应该去哪儿找这些。
这儿我以上面我的C模块为例，首先对于mapSatate函数可以这样玩，我在全局的modules里面声明了mc，那我的空间名称就是mc：
```js
computed: {
  ...mapState('mc', ['name', 'desc']) // 这里模块里面要使用辅助函数的话要多传一个参数才行
}
```
然后在模版里面写name，desc即可，或者可以这样：
```js
computed: {
  ...mapState('mc', {
    name(state) {
      return state.name;
    },
    desc(state) {
      return state.desc;
    }
  })
},
```
对于actions、mutations和getters方式类似，主要是要指定空间名称，比如对于声明的mutations：
```js
methods: {
  ...mapMutations('mc', ['modifyName'])
}
```
如果你确实不想在每个辅助函数里写空间名称，Vuex也提供了其它办法，使用createNamespacedHelpers创建基于某个命名空间辅助函数，它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：
```js
import { createNamespacedHelpers } from 'vuex';
const { mapState, mapMutations } = createNamespacedHelpers('mc');
```
这样你在写辅助函数的时候就不需要单独指定空间名称了。
其它类似，恕我就不再赘述了！