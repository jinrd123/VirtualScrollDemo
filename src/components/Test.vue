<template>
  <div class="container" ref="container">
    <div
      class="top"
      :style="{
        height: `${topHeight}px`,
      }"
    ></div>
    <div class="content"></div>
    <div
      class="bottom"
      :style="{
        height: `${bottomHeight}px`,
      }"
    ></div>
  </div>
  <button @click="test">按钮</button>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";

export default defineComponent({
  setup() {
    const topHeight = ref(300);
    const bottomHeight = ref(300);
    const container = ref(null);
    const test = () => {
      topHeight.value += 50;
      bottomHeight.value -= 50;
    };
    return {
      topHeight,
      bottomHeight,
      test,
      container,
    };
  },
});
</script>

<style scoped lang="less">
.container {
  width: 200px;
  height: 600px;
  overflow: auto;
  border: 1px solid green;
  .top {
    width: 100%;
    border: 3px solid red;
  }
  .content {
    height: 1000px;
  }
  .bottom {
    width: 100%;
    border: 3px solid black;
  }
}
</style>
