---
---

# vue3.5更新特性

1. 响应式props支持解构
2. watch的deep选项支持传入数字
3. useTemplateRef函数
4. 新增useId函数
5. 新增onWatcherCleanup函数

<div class="pt-24">
 
</div>

---

# 响应式props支持解构
这种写法将失去响应式
```ts{all}{lines: true}
<script setup lang="ts">
  import { watch } from 'vue'
  const { msg } = defineProps<{msg: string}>()

  watch(()=> msg,(newValue,oldValue)=> {
    console.log(newValue)
  })

  const newMsg = computed(()=> {
    return 'newMsg--' + msg
  })
  </script>
```
---

# 响应式props支持解构

<div class="grid grid-cols-2 gap-x-3">
<div v-click>
方式一：

```ts{all}{lines: true}
import { watch,computed } from 'vue'
const props = defineProps<{msg: string}>()

watch(()=> props.msg,(newValue,oldValue)=> {
  console.log(newValue)
})

const newMsg = computed(()=> {
  return 'newMsg--' + props.msg
})
```
使用props.xx的方式保持响应式

</div>
<div v-click.after>
方式二：

```ts{all}{lines: true}
import { toRef,toRefs,watch,computed } from 'vue'
const props = defineProps<{msg: string}>()

const msg1 = toRef(props,'msg')
const {msg: msg2} = toRefs(props)
watch(msg1,(newValue,oldValue)=> {
  console.log(newValue)
})

const newMsg = computed(()=> {
  return 'newMsg--' + msg2.value
})
```
使用toRef、toRefs两个API将props属性转成Ref的形式，保持响应式

</div>

</div>

---

# 响应式props支持解构
vue3.5新特性
<div v-click>

```ts{all}{lines: true}
import { watch,computed } from 'vue'
const { msg } = defineProps<{msg: string}>()

watch(()=> msg,(newValue,oldValue)=> {
  console.log(newValue)
})

const newMsg = computed(()=> {
  return 'newMsg--' + msg
})
```
</div>

<div v-click.after>
原理，vue编译后的代码

```ts{all}{lines: true}
watch(()=> __props.msg,(newValue,oldValue)=> {
  console.log(newValue)
})
const newMsg = computed(()=> {
  return 'newMsg--' + __props.msg
})
```
</div>

---

# watch的deep选项支持传入数字

以前 watch deep 属性只支持传boolean，不能控制对象属性的监听层数
<div v-click>

```ts{all}{lines: true}
import { watch,onMounted,reactive } from 'vue'
type Store = {
  name: string,
  customer: number,
  books: {bookName: string,no: number}[]
}
const store = reactive<Store>({name: '书店',customer: 5,books: [{bookName: 'vue',no: 1}]})

watch(()=> store,(newValue,oldValue)=> {
  alert(JSON.stringify(store))
},{ deep: true})

onMounted(()=> {
  //store.name = '书店--1'
  //store.customer = 10
  //store.books.push({bookName: 'css', no: 2})
  //store.books[0].bookName = 'javascript1'
})
```
</div>

---

# watch的deep选项支持传入数字

vue3.5 watch deep 属性支持传数字，表示监听对象属性的变化的层数

<div>

```ts{all}{lines: true}
import { watch,onMounted,reactive } from 'vue'
type Store = {
  name: string,
  customer: number,
  books: {bookName: string,no: number}[]
}
const store = reactive<Store>({name: '书店',customer: 5,books: [{bookName: 'vue',no: 1}]})

watch(()=> store,(newValue,oldValue)=> {
  alert(JSON.stringify(store))
},{ deep: 1})

onMounted(()=> {
  //store.name = '书店--1'
  //store.customer = 10
  //store.books.push({bookName: 'css', no: 2})
  //store.books[0].bookName = 'javascript1'
})
```
</div>

---

# useTemplateRef函数

以前获取模板中的元素或者组件

<div v-click>

```ts{all}{lines: true}
<template>
  <div>
    <span ref="testRef">test</span>
    <DemoView ref="demoRef"></DemoView>
  </div>
</template>
<script lang="ts" setup>
import { ref,onMounted } from 'vue'
import DemoView from './DemoView.vue'
const testRef = ref<HTMLElement>()
const demoRef = ref<InstanceType<typeof DemoView>>()

onMounted(()=> {
  console.log(testRef.value)
  console.log(DemoView.value)
})
</script>
```


[ref获取标签](https://play.vuejs.org/#eNqFU+9vmzAQ/Vcsf4FKEWjr9qUjSPsRaZ3WbeqifbI0ITiYO2NbtqGZEP/7zjgQWrWpoiT47t27d+fHQN9rnfQd0Cua2dJw7YgoZLNl1FlGiQXX6ZxJ3mplHBmIkjeqkw6qjYGajKQ2qiUREkQL6BO06heH+2MuSeeA7xO9Y7JU0jriwLpb5NgSZMo+72++7gS0IF0eX8yY6lh5wl1juJAl7P9pyBz+qHppmE+V/rOojOOLbU4GJgnxjEpAIlQTH3snfSE6wJqH2VXXBTHiN0vDhnAfeHDQalE4wBPJKt77f5JZXUgv1C8wNGE0909YjKkAmhscgat+CM7S+Twxp4E6S1f96AZvBxXXvEnurJJ4edOIjJaq1VyA+a4dx4kYvQrD+1whhLr/MsWc6WAzx8s/UP59In5nDz7G6A8DFkwPjC45V5gGXEjvfn6DAz4vyVZVnUD0meQt4LY7rzHAPnSyQtkr3KT2enIUl83e7g4OpJ2H8kI9cpzwjKKzPp4Z/ST3Mnkz1eGN4hbXznzhDVi9A4+dj5ZGzuBYWbQQrBpHM3vkHVZBzSXsDlpZiAcPO2+pk6cIGYbAO6JsTDxjid89GL8gHOQyeZu8ek3H/5shUZc=)


</div>

---

# useTemplateRef函数

以前获取模板中的元素或者组件

<div v-click>

```ts{all}{lines: true}
<template>
  <div>
    <span ref="testRef">test</span>
    <DemoView ref="demoRef"></DemoView>
  </div>
</template>
<script lang="ts" setup>
import { onMounted,useTemplateRef } from 'vue'
import DemoView from './DemoView.vue'
const testRef = useTemplateRef<HTMLElement>('testRef')
const demoRef = useTemplateRef<InstanceType<typeof DemoView>>('demoRef')

onMounted(()=> {
  console.log(testRef.value)
  console.log(DemoView.value)
})
</script>
```


[ref获取标签](https://play.vuejs.org/#eNqFU11vnDAQ/CuWXyDSCZSmfUk5pH6c1FRNW6WnPlmKECyExNiWbchFiP+eNQbCSclFpwPsnZ2dXY97+kWpqGuBXtLE5LpWlvBMVFtGrWGUGLCtSpmoGyW1JT2R4lq2wkKx0VBuWgN7aBTPLNxASQZSatmQAPmCJec7NPJ/DY9TLIrnDVc2+MxELoWxxIKxjmNLjkmTH/vrXzsODQibhsEEC87mvGJiezX3ChGZyGH/pCCx+JDloidFtlWyY3S/pcEwPNumpGeCEFdJcoi4rMJJQNRlvAXMOY6uCBfEgP8k9sPFUeLCThJxRZKi7tybJEZlguBU3ex9EUZT93WO2RjzqLnChFwVRHQSz+uROvbcSbwqSDd4sii5rKvo3kiBBz/2yGguG1Vz0H+UrbElRi999y6WcS4ff457VrewmffzO8gfXtm/Nwe3x+hfDQZ0B4wuMZvpCqwP7/79hgN+L8FGFi1H9IngDeC4W6fRw762okDZK9yo9mq0Xy2qvdkdLAgzN+WEOuQw4hlFG3470fqL3Ivo45iHR4pTXNv4nduzuj94akfXBP2PnN7KImsAPYyQMJjZnS3R42UtYHdQ0kDYO9hpT72YipC+97wDysbAG5a47UC7AWEjF9Gn6PwDHZ4Bl05oQg==)


</div>

---

# 新增useId函数
useId() 生成的每个 ID 在每个应用内都是唯一的。它可以用于为表单元素和无障碍属性生成 ID。在同一个组件中多次调用会生成不同的 ID；同一个组件的多个实例调用 useId() 也会生成不同的 ID。
```ts{all}{lines: true}
<script setup>
import { useId } from 'vue'

const id = useId()
console.log(id)
</script>

<template>
  <form>
    <label :for="id">Name:</label>
    <input :id="id" type="text" />
  </form>
</template>

```

---

# 新增onWatcherCleanup函数
<div class="grid grid-cols-2 gap-x-3">
<div>
取消请求

```ts{all}{lines: true}
import { watch, onWatcherCleanup } from 'vue'
let oldCancel = null;
watch(id, (newId) => {
  if(oldCancel){
    oldCancel()
  }
  const { response, cancel } = doAsyncWork(newId)
  oldCancel = cancel
  // 如果 `id` 变化，则调用 `cancel`，
  // 如果之前的请求未完成，则取消该请求
  onWatcherCleanup(cancel)
})

```
</div>
<div v-click>
处理异步

```ts{all}{lines: true}
import { watch, onWatcherCleanup } from 'vue'
let intervalId = 0
watch(id, (newId) => {
  if(intervalId){
    clearTimeout(intervalId)
  }
  intervalId = setTimeout(()=>{ 
    console.log('500s后处理')
  }，500)
  
  // 如果 `id` 变化，则调用 `cancel`，
  // 如果之前的请求未完成，则取消该请求
})

```

</div>
</div>

---

# 新增onWatcherCleanup函数

```ts{all}{lines:true}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const { response, cancel } = doAsyncWork(newId)
  // 如果 `id` 变化，则调用 `cancel`，
  // 如果之前的请求未完成，则取消该请求
  onWatcherCleanup(cancel)
})

```


```ts{all}{lines:true}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const intervalId = setTimeout(()=>{ 
    console.log('500s后处理')
  }，500)
  // 如果 `id` 变化，则调用 `cancel`，
  // 如果之前的请求未完成，则取消该请求
  onWatcherCleanup(()=> {
    clearTimeout(intervalId)
  })
})

```