## 开始
```vue
<script lang='ts'>
// 引入注册
import Scroll from '...'
export default {
  components: {
    Scroll
  },
  setup() {
    ...

    return {}
  },
  methods: {
    clickTarget(wvwnt: MouseEvent) {
      ...
    },
  },
},
</script>

<template>
  <!-- datas: 列表数据（数组） -->
  <!-- itemSize: 单项高度（数字） -->
  <!-- clickTarget: 点击事件 -->
  <Scroll class="scroll" :datas="datas" :itemSize="itemSize" @clickTarget="clickTarget" v-slot="slotProps">
    <!-- 插槽传入列表项 -->
    <!-- slotProps.visibleData: 处理后的列表数据 -->

    <!-- <div v-for="(item, index) in slotProps.visibleData" :key="index">{{ index }}</div> -->
  </Scroll>
</template>
```
