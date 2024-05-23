<script setup lang="ts">
import {onMounted} from "vue";
import printJS from "print-js";

const {data}=defineProps(['data'])
onMounted(()=>{
  printJS({
    printable:'printDom',
    type:'html',
    targetStyles:['*'],
  })
})

</script>
<template>
  <div class="content" id="printDom">
    <div class="item" v-for="item in data">
      {{item.x}}{{item.operator}}{{item.y}}=
    </div>
  </div>
</template>

<style scoped>
.content {
  display: flex; /* 启用flex布局 */
  flex-direction: row;
  flex-wrap: wrap;
}

.item {
  width: 80px;
  text-align: center; /* 文本水平居中 */
  padding: 5px; /* 内边距 */
  margin: 5px 12px 5px 12px; /* 外边距 */
}
</style>