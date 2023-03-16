<template>
  <div 
    class="scroll-container"
    :style="{
      overflow: 'auto',
      height: `${viewPortHeight}px` // 列表视口高度（值自定义即可）
    }"
    ref="scrollContainer"
    @scroll="handleScroll"
  >
  <div 
    class="content-container"
    :style="{
      height: `${itemHeight * dataSource.length}px`,
      position: 'relative'
    }"
  >
    <div 
      class="content-item"
      v-for="(data, index) in renderDataList"
      :style="{
        position: 'absolute',
        top: '0',
        left: '0',
        transform: `translateY(${(startIndex + index) * itemHeight}px)`
      }"
    >
      {{ data.text }}
    </div>
  </div>
</div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from "vue";
import { dataSource } from "@/shared/dataConstant";

export default defineComponent({
  name: "VirtualScroll",
  setup() {
    const viewPortHeight = 525; // 滚动列表的可视高度
    const itemHeight = 50; // 一个列表项的高度
    const startIndex = ref(0);
    const endIndex = ref(Math.ceil(viewPortHeight / itemHeight) - 1);
    const scrollContainer = ref<HTMLElement | null>(null);

    let isHandling = false; // 节流辅助变量
    const countOfBufferItem = 2; // 缓冲结点数量
    const handleScroll = () => {
      if(isHandling) return;
      isHandling = true;
      setTimeout(() => {
        if(!scrollContainer.value) return
        const scrollTop = scrollContainer.value.scrollTop;
        startIndex.value = Math.floor(scrollTop / itemHeight);
        startIndex.value = startIndex.value - countOfBufferItem >= 0 ? startIndex.value - countOfBufferItem : 0; // 扩充渲染区间
        endIndex.value = Math.ceil((scrollTop + viewPortHeight) / itemHeight) - 1;
        endIndex.value = endIndex.value + countOfBufferItem >= dataSource.length - 1 ? dataSource.length - 1 : endIndex.value + countOfBufferItem; // 扩充渲染区间
        isHandling = false;
      }, 30)
    }

    const renderDataList = ref(dataSource.slice(startIndex.value, endIndex.value + 1));
    watch(() => startIndex.value, () => {
      renderDataList.value = dataSource.slice(startIndex.value, endIndex.value + 1);
    })
    watch(() => endIndex.value, () => {
      renderDataList.value = dataSource.slice(startIndex.value, endIndex.value + 1);
    })
    return {
      viewPortHeight,
      dataSource,
      itemHeight,
      scrollContainer,
      handleScroll,
      renderDataList,
      startIndex,
      endIndex
    }
  },
});
</script>

<style scoped lang="less">
.scroll-container {
  border: 2px solid red;
  width: 300px;
  .content-container {
    .content-item {
      height: 50px;
      background-image: linear-gradient(0deg, pink, blue);
      width: 100%;
    }
  }
}

</style>
