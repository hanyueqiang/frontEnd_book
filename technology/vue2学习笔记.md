## vue2 学习

#### 引入 Vue

[下载地址](https://cn.vuejs.org/v2/guide/installation.html)

```js
// 一种方式
<script src="vue.js" />
```

#### 创建 Vue 实例

Vue 是以数据驱动核心思想

```js
const app = new Vue({
  el: "#app",
  data() {
    return {
      title: "hello vue",
    };
  },
});
```

mvvm 思想三要素：响应式、模板引擎、渲染

#### 模板语法

注意`v-bind`可简写`:`

```vue
<template>
  <h2 :title="title">{{ title }}</h2>
</template>
```

#### 列表渲染 v-for

```vue
<template>
  <div v-for="item in courses" :key="item">
    <div>{{ item }}</div>
  </div>
</template>

<script>
const app = new Vue({
  el: "#app",
  data() {
    return {
      title: "hello vue",
      courses: ["vue", "react"],
      curse: "",
    };
  },
});
</script>
```

#### 双向绑定 v-model

```vue
<template>
  <input type="text" v-model="curse" @keydown.enter="addCourse" />
</template>
```

#### class 与 style 绑定

点击列表默认选中

```vue
// class
<template>
  <div
    v-for="item in courses"
    :key="item"
    :class="{ active: current === item }"
    @click="current = item"
  >
    <div>{{ item }}</div>
  </div>
</template>

// style
<template>
  <div
    v-for="item in courses"
    :key="item"
    :style="{ backgroundColor: current === item ? '#ccc' : 'transparent' }"
    @click="current = item"
  >
    <div>{{ item }}</div>
  </div>
</template>
```

#### 条件渲染 v-if

注意： v-if 不能与 v-for 放同一个标签，会有渲染优先级的问题

```vue
<template>
  <div v-if="courses.length === 0">没有任何内容</div>
  <div v-else>
    <div
      v-for="item in courses"
      :key="item"
      :style="{ backgroundColor: current === item ? '#ccc' : 'transparent' }"
      @click="current = item"
    >
      <div>{{ item }}</div>
    </div>
  </div>
</template>
```

#### 模板语法是如何实现的

Vue.js 允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据
在底层的实现上，Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，Vue 能够只能地计算出最少需要重新玄滩多少组件，并把 DOM 操作次数减到最少
其中 compiler-core 模块是 Vue 编译的核心模块，并且是平台无关的。而剩下的三个都是在

compiler-core 的基础上针对不同的平台作了适配处理。

Vue 的编译分为三个阶段，分别是：parse、transform、codegen。

其中 parse 阶段将模板字符串转化为语法抽象树 AST。transform 阶段则是对 AST 进行了一些转换处理。codegen 阶段根据 AST 生成对应的 render 函数字符串。

#### 计算属性和监听器

// 计算属性有缓存的概念，当值不变时不会触发渲染
注意： watch 函数初始不会执行,如要立即执行需设置 immediate 为 true

```js
const app = new Vue({
  el: "#app",
  data() {
    return {
      title: "hello vue",
      courses: ["vue", "react"],
      curse: "",
    };
  },
  computed: {
    total() {
      return this.courses.length + '门';
    }
  },
  watch: {
    courses(newValue, oldValue) {
      this.totalCount = newValue.length + '门';
    }
  },
});

// watch立即执行 deep是否需递归遍历元素
watch: {
  courses: {
    immediate: true,
    deep: true,
    handler(newValue, oldVal) {
      this.totalCount = newValue.length + '门';
    }
  }
}

```

#### 生命周期

created： 组件实例已创建，未挂载，dom 不存在
mounted: dom 已挂载,可访问 dom 元素

```js
beforeCreate;
created;(数据注入，来自父组件等，可以进行异步)
beforeMount;(未执行渲染、dom还未创建)
mounted;(挂载)

// 非初始化阶段 值变化触发
beforeUpdate;
updated;


beforeDestroy(解除事件绑定，取消监听、订阅)
destroyed
```

#### 组件化

```vue
<template>
  <course-list :courses="courses"></course-list>
  <course-add @add-course="addCourse"></course-add>
</template>

<script>
Vue.component("course-list", {
  data() {
    return {
      current: "",
    };
  },
  props: {
    courses: {
      type: Array,
      default: [],
    },
  },
  template: `
      <div>
        <div v-for="item in courses" :key="item" :style="{backgroundColor: current===item? '#ccc': 'transparent'}" @click="current=item">
      <div>{{ item }}</div>
        </div>
    </div>
      `,
});

Vue.component("course-add", {
  data() {
    return {
      curse: "",
    };
  },
  template: `
    <div>
      <input type="text" v-model="curse" @keydown.enter='addCourse'>
      <button @click="addCourse">添加</button>
    </div>
  `,
  methods: {
    addCourse() {
      // 派发事件通知父组件
      this.$emit("add-course", this.curse);
      this.curse = "";
    },
  },
});
</script>
```

#### 自定义组件化双向数据绑定

```vue
<template>
  <course-add @add-course="addCourse" v-model="curse"></course-add>
</template>

<script>
Vue.component("course-add", {
  props: ["value"],
  template: `
      <div><input type="text" :value="value" @input="onInput" @keydown.enter='addCourse'>
        <button @click="addCourse">添加</button>
        </div>
      `,
  methods: {
    addCourse() {
      // 派发事件通知父组件
      this.$emit("add-course");
    },
    onInput(e) {
      this.$emit("input", e.target.value);
    },
  },
});
</script>
```

#### 插槽 slot（内容分发）

```js
```
