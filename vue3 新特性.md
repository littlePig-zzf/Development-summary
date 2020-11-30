#### vue3 新特性

1、双向绑定 由difineProperty 改成了proxy

2、组合composition API



##### 为什么要改成proxy?

defineproperty是通过监听对象中已存在的属性进行双向绑定的，这样对象需要绑定新的属性时，会出现无法监听的情况。需要使用$set 。手动绑定新增的属性进行监听。

```javascript
function observe(obj, callback) {
    const newObj = {}
    Object.keys(obj).forEach(key => {
    	Object.defineProperty(newObj, key, {
            get() {
                return obj[key]
            },
            set(newValue) {
                obj[key] = newValue
                callback(key, newValue)
                return true
            }
        })       
    })
    return newObj
}

let data = {
    list: []
}
const arr = observe(data, (key, value)=>{
    console.log(`${key}被修改为${value}`)
})

const obj = observe({
	name: 'zzf',
    age: '20'
}, (key, value)=>{
    console.log(`${key}被修改为${value}`)
})

obj.name = '女孩'
obj.age = '30'

obj.user = 'zzf'

```

而proxy则是监听整个对象进行双向绑定的，即使你新增属性也完全可以监听。

```javascript
function observe(obj, callback) {
	return new Proxy(obj, {
        get(target, key) {
            return target[key]
        },
        set(target, key, value) {
            target[key] = value
            callback(key, value)
        }
    })
}

// 绑定数据
const arr = observe([1,2,3,4], (key, value)=>{
    console.log(`${key}被修改为${value}`)
})

arr[1] = 0

// 绑定对象
const obj = observe({
	name: 'zzf',
    age: '20'
}, (key, value)=>{
    console.log(`${key}被修改为${value}`)
})


obj.name = '男孩'
obj.age = '35'

obj.user = 'zzf'

```

##### 组合composition API主要有：

所有composition API 都必须在setup函数中使用。主要有一下的API：

详细API了解https://composition-api.vuejs.org/api.html。

Setup

生命周期

reactive

shallowReactive

ref

watchEffect



#### **很多人在说，vue3越来越像react了，我们来看看它们的相似之处。**

**Setup**是使用composition api的入口，我们定义变量，函数都要在这里面进行。它有两个参数，分别是：props（可以获取外部组件传进来的属性值，相当于vue2中组件定义的props的值，都可以通过这个来获取），context（一个上下文对象，可以访问到`attr、emit、slots`），其中emit是与父组件通信的调用方法。



##### **生命周期为以下几个阶段：**(顺便列了一下vue2、React 类组件的生命周期进行对比)

| Vue2          | Vue3            | React  class component     |
| ------------- | --------------- | -------------------------- |
| beforeCreate  | setup           | class Component constuctor |
| created       | setup           | constuctor                 |
| beforeMount   | onBeforeMount   | componentWillMount         |
| mounted       | onMounted       | componentDidMount          |
| beforeUpdate  | onBeforeUpdate  | componentWillUpdate        |
| updated       | onUpdated       | componentDidUpdate         |
| beforeDestory | onBeforeUnmount | componentWillUnmount       |
| destoryed     | onUnmounted     | --                         |

**Vue3中，使用对应的生命周期钩子必须要先import进来才能使用。**

```
<template>
  <div id="app"></div>
</template>

<script>
// 1. 从 vue 中引入 多个生命周期函数
import {onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, unMounted} from 'vue'
export default {
  name: 'App',
  setup() {
      onBeforeMount(() => {
          // 在挂载前执行某些代码
      })
      onMounted(() => {
      	// 在挂载后执行某些代码
      })
      onBeforeUpdate(() => {
          // 在更新前前执行某些代码
      })
      onUpdated(() => {
          // 在更新后执行某些代码
      })
      onBeforeUnmount(() => {
          // 在组件销毁前执行某些代码
      })
      unMounted(() => {
          // 在组件销毁后执行某些代码
      })
      return {}

  }

}
</script>
```



**reactive**：创建一个响应式对象，用过vue2的同学都知道`defineProperty`对多层级的数组和对象监听的缺陷。而Reactive刚好解决这个问题。再也不用使用`$set`了。相当于react的state。



**ref**：ref跟reactive都是创建一个响应式对象，二者差不多。ref其实都是使用reactive定义了一个对象，并把该值赋值给这个对象的value属性。就相当于：`ref(obj|string|number|boolean) = reactive({value: obj|string|number|boolean})`

**需要注意：**

在setup中调用ref定义的变量，需要通过`.value`来进行获取，修改的操作。在template中获取值，就无需使用`.value`来获取。因为编译的时候会自动识别是否是ref还是reactive。具体怎么识别，感兴趣自行研究。

#### **既然ref和reactive功能差不多，那怎么判断何时使用ref、reactive呢？**

1. 基本类型值（`String` 、`Nmuber` 、`Boolean` 等）或单值对象（类似像 `{count: 3}` 这样只有一个属性值的对象）使用 `ref`
2. 引用类型值（`Object` 、`Array`）使用 `reactive`



**toRef**：将某个对象中的某一个属性转为响应式数据，该方法有两个参数，分别为：对象、对象属性。例如：`const obj = {count: 3}; toRef(obj, 'count')`



#### **那么这个toRef和上面说的ref到底有什么区别呢？**

```
<template>
    <p>{{ state1 }}</p>
    <button @click="add1">增加</button>

	<p>{{ state2 }}</p>
    <button @click="add2">增加</button>
</template>

<script>
import {ref, toRef} from 'vue'
export default {
    setup() {
        const obj = {count: 3}
        const state1 = ref(obj.count)
        const state2 = toRef(obj, 'count')

        function add1() {
            state1.value ++
            console.log('原始值：', obj);
            console.log('响应式数据对象：', state1);
        }

        function add2() {
            state2.value ++
            console.log('原始值：', obj);
            console.log('响应式数据对象：', state2);
        }

        return {state1, state2, add1, add2}
    }
}
</script>
```

从上面这个例子，我们可以得出他们之间的区别是什么了。

##### **总结一下：**

1. `ref` 是对传入数据的拷贝；`toRef` 是对传入数据的引用
2. `ref` 的值改变会更新视图，不影响初始值；`toRef` 的值改变不会更新视图，改变初始值



#### **这里对toRef引发一个思考，toRef和定义一个变量有什么不一样？**



**toRefs**：是将一个对象中的全部属性转为响应式对象。参数只有一个，即需转换的对象obj。

#### **引发思考：**toRefs与reactive 有什么区别？区别仅仅只是参数的限制吗？



#### **那么react的ref又是怎么样去获取的呢？**

我们知道react有两种写法，一种是函数式组件、一种是class类组件。函数式组件通过使用useRef来定义一个ref对象，而class类组件是通过createRef来创建的，然后在组件上使用ref属性，将创建的ref对象赋值给它。



你可能会问，函数式组件不能使用createRef去创建，然后获取到组件吗？当你在函数式组件中使用的时候，不会报错，但是你会获取不到值。



拿函数式组件举例，我们在自定义组件中使用useRef，会发现获取的是null，此时，我们要去到自定义组件的文件外层，加上一个forwardRef（）方法包裹。此时你再去获取，可以获取到dom了，但是你会发现组件中的函数都获取不到。

这时，就要使用useImperativeHandle（）这个方法将需要暴露出去的函数放进去。这样就可以获取到这个方法了。这个方法有两个参数，第一个参数是ref，第二个参数是函数，里面定义需要暴露的函数。



**注意：forwardRef（）、useImperativeHandle（）这两个方法都是函数式组件中获取ref需要调用的。在class类组件中无需调用。**

```
父组件：index.jsx
const demoRef = useRef()
<demo ref={demoRef}/>

子组件：demo.jsx
React.forwardRef((prop, ref)=>{
  useImperativeHandle(ref, () => ({
    onSearch: () => {...},
  }));
})
```







#### **下面讲几个用于性能优化的api：**

shallowReactive，乍一看应该是和reactive差不多的，shallow 意思是浅的意思，那么从字面意思就是：浅层的监听对象。

**shallowReactive**：如果一个对象有多层级，那么这个方法只会监听第一层的属性，改变了则立即更新视图，否则即使其他层级的数据改变了，也不会进行视图更新。



**shallowRef**：`shallowReactive` 是监听对象第一层的数据变化用于驱动视图更新，那么 `shallowRef` 则是监听 `.value` 的值的变化来更新视图的。

```
<template>
	<p>{{ state.a }}</p>
	<p>{{ state.first.b }}</p>
	<p>{{ state.first.second.c }}</p>
	<button @click="change1">改变1</button>
	<button @click="change2">改变2</button>
</template>

<script>
import {shallowRef} from 'vue'
export default {
    setup() {
        const obj = {
            a: 1,
            first: {
                b: 2,
                second: {
                    c: 3
                }
            }
        }

        const state = shallowRef(obj)
        console.log(state);

        function change1() {
            // 直接将state.value重新赋值
            state.value = {
                a: 7,
                first: {
                    b: 8,
                    second: {
                        c: 9
                    }
                }
            }
        }

        function change2() {
            state.value.first.b = 8
            state.value.first.second.c = 9
            
            // 修改值视图更新
            triggerRef(state)
            console.log(state);
        }

        return {state, change1, change2}
    }
}
</script>
```

从这个例子，可以看出只有修改了`.value`才会更新视图，是不是觉得很麻烦，那每次修改里层的值都得重新赋值？

对于这种情况，我们可以使用`triggerRef`，就可以进行视图更新了。



#### 对比react优化

说到优化，我们知道vue框架本身就已经帮我们做了很多优化，我们使用者基本也不用再去优化了，已经是按需更新视图了。但react并不，用过react的朋友应该知道有一个钩子： `shouldComponentUpdate(nextProps, nextState)` ，这个方法就是专门用来做性能优化的。当然只适用与class类组件的写法。

使用这个方法，必须要有返回值，否则不会去更新视图，但还是会渲染。return true的时候才去更新视图，false则什么也不干。一般都会在这个方法里面进行判断，获取到最新的值跟上一次我们的this.state或者props 的值是否相等，相等则不更新，否则更新。



##### 那么对于hook写法，也就是函数式组件的朋友，没有`shouldComponentUpdate` 这个方法，怎么去优化的呢？

hook写法常用的几个方法：useState、useReducer、useRef、useEffect、useCallback、useMemo。后面三个都有两个参数，第一个参数是函数，第二个参数是依赖项。依赖哪个变量，那么就只有这个依赖项改变的时候，才会去重新更新依赖项的值。否则每次调用都是初始化的值。特别是使用useCallback要特别注意。所以我们可以了解，hook写法的性能优化是对于依赖项的处理。



##### **react是使用者手动根据业务逻辑来进行优化。**



#### eventBus

最后来说一个vue事件总线（eventBus），相信大部分vue的使用者都用过。

**可全局，多组件之间的通信。**

回顾下 vue2 中定义eventBus。

```
1、bus.js文件
import Vue from 'vue';
export defalut Vue;

2、组件中使用
import bus from './bus'

bus.emit('function')

bus.on('function', ()=>{})
```

**那在 vue3 中，我们怎么去定义一个eventBus呢？**



首先我们要在入口文件中，引入mitt库（一个功能事件的订阅发布器）绑定在window对象上，然后直接在组件中使用。

```
1、在main.js中引入mitt
import { createApp } from 'vue';
import mitt from 'mitt';
import App from './App';
import router from './router';

const app = createApp(App);
window.mitt = mitt();

app.use(router).mount('#app');

2、组件中直接使用
window.emit('function')
window.on('function', ()=>{})

```

当然，你也可以类似之前vue2这样定义bus.js文件：

```
1、定义一个bus.js文件
import mitt from 'mitt';
export default mitt();

2、组件中使用，先引入bus.js文件
import bus from './bus'

bus.emit('function')

bus.on('function', ()=>{})
```

两种方式看大家的编程习惯去选择。





#### **为什么使用typescript来改写vue内部？**

typescript flow 都是语言规范。

vue使用flow做静态类型检查，之所以选择flow，主要是因为Babel和Eslint 都有对应的Flow插件以支持语法，可以完全沿用现有的构建配置，非常小成本的改动就可以拥有静态类型检查的能力。



#### **Flow 的工作方式**

**类型推断**：通过变量的使用上下文来推断出变量类型，然后根据这些推断来检查类型。

**类型注释**：事先注释我们期待的类型，Flow会基于这些注释来判断。

待完善......



