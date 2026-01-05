## 项目结构

- 安装
```shell
npm install -g @vue/cli
```

- 新建项目
```shell
vue create my-project
```

```shell
npm init vue@latest
```

- 项目结构
```
my-project/
├── node_modules/    # 依赖包
├── public/          # 静态文件
│   ├── index.html   # 主HTML文件
│   └── favicon.ico  # 图标
├── src/             # 源代码
│   ├── assets/      # 静态资源
│   ├── components/  # 组件
│   ├── App.vue      # 根组件
│   └── main.js      # 入口文件
├── package.json     # 项目配置
└── README.md        # 项目说明
```
- 关键文件
	- `vite.config.js`:项目配置文件
	- `package.json`:项目包文件
	- `main.js`:入口文件，创建应用实例并挂载
	- `app.vue`：根组件
	- `index.html`: 单页入口，提供id为app的挂载点

## 变量创建

- Composition API

- setup在Beforecreate自动执行，this指向undefined
```vue
<script setup> //setup 语法糖
import { ref, reactive } from 'vue'

// 变量声明
const message = ref('Hello Vue!')
const count = ref(0)
const user = reactive({
  name: 'John',
  age: 30
})
const todos = ref(['Learn Vue', 'Build something'])

// 自动暴露给模板，无需 return
</script>
```

-  Options API
```js
export default {
  data() {
    return {
      // 在这里声明变量
      message: 'Hello Vue!',
      count: 0,
      user: {
        name: 'John',
        age: 30
      },
      todos: ['Learn Vue', 'Build something']
    }
  }
}
``` 

- 在模板中使用变量
```vue
<template>
  <div>
    <p>{{ message }}</p>
    <p>Count: {{ count }}</p>
    <p>User: {{ user.name }}, {{ user.age }} years old</p>
    <ul>
      <li v-for="todo in todos" :key="todo">{{ todo }}</li>
    </ul>
  </div>
</template>
```

## ref&reactive

- reactive
	- 接受一个对象参数，返回一个响应式的对象
```vue
<script setup>
import { reactive } from 'vue'

const state = reactive({ count: 0 })
</script>


<template>
<button @click="state.count++">
  {{ state.count }}
</button>
</template>
```


- ref
	- `ref()` 接收参数，并将其包裹在一个带有 `.value` 属性的 ref 对象中返回
	- 在模板中使用 ref 时，我们**不**需要附加 `.value`。
```vue
<script>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
<button @click="count++">
  {{ count }}
</button>
</template>

```

## 计算属性computed

- 计算属性中不应该有副作用
- 避免直接修改计算属性的值

```vue
<script setup>
import {computed} from 'vue';
const var = computed(()=>{
	return ...//基于响应式数据做计算之后的值
})
</script>
```

## watch

- 侦听数据变化，在变化时执行回调函数
- 参数：`immediate`立即执行，`deep`深度侦听
- watch接受的对象不用加value
```vue
<script setup>
import {watch,ref} from 'vue'
const count = ref(0)

watch(count,(newValue,oldValue)=> {
	console.log(`${oldValue}变为了${newValue}`)
})

</script>
```
- 多个数据源侦听
```vue
<script setup>
import { ref,watch } from 'vue';
const count = ref(0)
const add = () => {
  count.value++;
}
const name = ref('cp')
const change = ()=> {
  name.value = 'pc'
}
watch(
  [count,name],
  ([newcount,newname],
    [oldcount,oldname]
  )=>{
    console.log('count或者name变化',[newcount,newname],[oldcount,oldname])
  }
)
</script>
```
- 立即实行: 在侦听器创建时就执行一次
```vue
<script setup>

import { computed,ref,watch } from 'vue';
const count = ref(0)
const add = () => {
  count.value++;
}

//补充第三个参数，对象类型
watch(count,(newv,oldv)=>{
  console.log(oldv,"->",newv)
},{
  immediate:true  //立即执行
})

</script>
```

- 深度监听
	- 修改对象的属性时需要深度侦听才可以触发侦听器
```vue
<script setup>

import { ref,watch } from 'vue';
const state = ref({count:0})
const add = () => {
  state.value.count++;
}

//修改count不执行
watch(state,()=>{
  console.log("state变化了")
})

// 深度监听，此时修改count会执行
watch(state,()=>{
  console.log("state变化了")
},{
  deep:true
})

</script>
```

- 精确监听：确定当某个对象的特定属性时才触发监听器，将第一个参数改成回调函数，返回要监听的那个属性
```vue
<script setup>

import { ref,watch } from 'vue';
const info = ref({
  name:'cp',
  age:18
})
const changename = ()=>{
  info.value.name = "hihihi"
}
const changeage = ()=> {
  info.value.age = 100
}

//只有当age属性变化时调用回调函数
watch(
  ()=> info.value.age ,
  ()=>{
    console.log("age变化了")
  })
</script>
```
## 组件

- 单文件组件: 一个vue文件就可以是一个单文件组件
- 使用组件时要进行导入
```vue
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

### props

- 组件之间传递参数通过prop完成
- 声明
```vue
<script setup>
const props = defineProps(['foo'])

//使用时要props.foo的形式使用
console.log(props.foo)

// 或者以对象创建，key 是 prop 的名称，而值则是该 prop 预期类型的构造函数
defineProps({
  title: String,
  likes: Number
})

</script>

<template>
//使用时要props.foo的形式使用
<p> {{props.foo}} </p>

</template>
```
- 父组件传递prop
```vue
<BlogPost title="My journey with Vue" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :title="post.title" />

<!-- 根据一个更复杂表达式的值动态传入 -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```
- 所有的 props 都遵循着**单向绑定**原则，props 因父组件的更新而变化，自然地将新的状态向下流往子组件，而不会逆向传递。
- 另外，每次父组件更新后，所有的子组件中的 props 都会被更新到最新值，这意味着你**不应该**在子组件中去更改一个 prop。


## 触发与监听事件

- 在组件的模板表达式中，可以直接使用 `$emit` 方法触发自定义事件
```vue
<template>
<!-- MyComponent -->
<button @click="$emit('someEvent')">Click Me</button>
</template>
```
- 父组件可以通过 `v-on` (缩写为 `@`) 来监听事件：
```vue
<MyComponent @some-event="callback" />
```

## 声明事件

```vue
<script setup>
defineEmits(['inFocus', 'submit'])
</script>
```
## 事件参数

- 有时候我们会需要在触发事件时附带一个特定的值。
- 给 `$emit` 提供一个额外的参数
```vue
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```
- 父组件传递参数的方法
- 内联箭头函数
```vue
<MyButton @increase-by="(n) => count += n" />
```
- 或者
```vue
<template>
<MyButton @increase-by="increaseCount" />
</template>

<script>
function increaseCount(n) {
  count.value += n
}
</script>
```

## 类名绑定

### 绑定对象

- 表示 `active` 是否存在取决于数据属性 `isActive` 的[真假值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)。
```vue
<div :class="{ active: isActive }"></div>
```
- 例子：
```vue
<script>
const isActive = ref(true)
const hasError = ref(false)
</script>

<template>
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>

</template>
```
则相当于
```vue
<div class="static active"></div> 
```

### 绑定数组

```vue
<script>
const activeClass = ref('active')
const errorClass = ref('text-danger')
</script>
<template>
<div :class="[activeClass, errorClass]"></div>
</template>
```
相当于
```vue
<div class="active text-danger"></div>
```
- 还可以条件渲染
```vue
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```


## 条件渲染

- `v-if` 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回真值时才被渲染
- 也可以使用 `v-else` 为 `v-if` 添加一个“else 区块”。
- `v-else-if` 提供的是相应于 `v-if` 的“else if 区块”。它可以连续多次重复使用：

```vue
<h1 v-if="awesome">Vue is awesome!</h1>
```

```vue
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

## 列表渲染

```vue
<li v-for="item in items">
  {{ item.message }}
</li>
```
- `index`为位置索引
```vue
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

## 表单输入绑定

- 将表单输入框的内容同步给 JavaScript 中相应的变量
```vue
<input v-model="text">
```
- 例子
```vue
<p>Message is: {{ message }}</p>
<input v-model="message" placeholder="edit me" />
```