<template>
  <div class="container">
    <div class="long-list" ref="scrollDom">
      <div
        class="top-padding"
        :style="{
          height: `${topPaddingHeight}px`,
        }"
      ></div>
      <div
        v-for="(data, index) in renderDataList"
        :key="index"
        class="list-item"
      >
        {{ `${data.text} - ${index}` }}
      </div>
      <div
        class="bottom-padding"
        :style="{
          height: `${bottomPaddingHeight}px`,
        }"
      ></div>
    </div>
  </div>
</template>

<script lang="ts">
import { onMounted, ref, defineComponent, computed, watch } from "vue";
import { dataSource } from "@/shared/dataConstant";
export default defineComponent({
  name: "VirtualScroll",
  setup() {
    const scrollDom = ref<HTMLElement | null>(null);
    const listItemHeight = 50; // 列表元素高度
    const longListViewHeight = 300; // 长列表可视高度
    const countOfBufferItem = 2; // 缓冲结点数
    const startIndex = ref(0);
    const endIndex = ref(Math.floor(longListViewHeight / listItemHeight));
    const renderDataList = ref<{ text: string }[]>(
      dataSource.slice(startIndex.value, endIndex.value)
    ); // 实际渲染的数据列表
    onMounted(() => {
      let isHandling = false;
      scrollDom.value &&
        scrollDom.value.addEventListener("scroll", () => {
          if (isHandling) return;
          isHandling = true;
          setTimeout(() => {
            startIndex.value = Math.floor(
              (scrollDom.value as HTMLElement).scrollTop / listItemHeight
            );
            startIndex.value = startIndex.value - countOfBufferItem >= 0 ? startIndex.value - countOfBufferItem : 0;
            endIndex.value = Math.floor(
              ((scrollDom.value as HTMLElement).scrollTop +
                (scrollDom.value as HTMLElement).clientHeight) /
                listItemHeight
            );
            endIndex.value = endIndex.value + countOfBufferItem < dataSource.length ? endIndex.value + countOfBufferItem : dataSource.length - 1;
            isHandling = false;
          }, 500);
        });
    });
    watch(
      () => startIndex.value,
      () => {
        renderDataList.value = dataSource.slice(
          startIndex.value,
          endIndex.value
        );
      }
    );
    const topPaddingHeight = computed(() => {
      return startIndex.value * listItemHeight;
    });
    const bottomPaddingHeight = computed(() => {
      return (dataSource.length - endIndex.value - 1) * listItemHeight;
    });
    return {
      dataSource,
      scrollDom,
      renderDataList,
      startIndex,
      endIndex,
      listItemHeight,
      topPaddingHeight,
      bottomPaddingHeight,
    };
  },
});
</script>

<style scoped lang="less">
.long-list {
  overflow: auto;
  width: 150px;
  height: 300px;
  border: 5px solid red;
}
.list-item {
  width: 100%;
  height: 50px;
  background-image: linear-gradient(0deg, green, blue);
}
</style>
