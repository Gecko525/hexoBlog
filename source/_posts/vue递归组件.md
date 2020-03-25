---
title: vue递归组件
date: 2019-01-05
tags: vue
typora-root-url: ..
---

现有一嵌套结构数据，嵌套层级未知，问如何将数据中所有的name列出？

<!--more-->

```json
[
    {
        name: 'zhangsan',
        children: [
            {
                name: 'lisi',
                children: [...]
            }
        ]
    },
    ...
]
```

首先想到的肯定是递归，何为递归？

> 函数Func(Type a,……)直接或间接调用函数本身，则该函数称为递归函数

第一种思路是先将数据递归取出name，再用v-for遍历渲染。

```js
function getName(data) {
    let names = [];
    data.forEach((d) => {
        names.push(d.name);
        if (d.children && d.children.length > 0) {
            names = names.concat(getName(d.children));
        }
    });
    return names;
}
// ['zhangsan','lisi','wangwu','siliu','qita','sghs','kkig','ssgd']
```

这种思路需要自己实现递归函数，并将其存储在一个新的数据中。

第二种是直接利用vue的递归组件实现，即在组件中使用自己

```vue
// ShowNames.vue
<template>
  <div>
    <ul>
      <li v-for="(item, index) in names" :key="index">
        {{item.name}}
        <show-names v-if="item.children && item.children.length > 0" :names="item.children"></show-names>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "ShowNames",
  props: ["names"]
};
</script>
```

结果如下
![result](/images/vue递归组件/result.png)