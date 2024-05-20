<script setup lang="ts">
import {ref} from "vue";
import {MenuOutline} from "@vicons/ionicons5";
const btnKey = ref("");
const btnTrans = (key: any) => {
  btnKey.value += key;
  console.log(btnKey.value);
};
const number = ref(20)
const as = ref(true)
const md = ref(false)
const decimal = ref(false)
const msgBarStatus = ref(true)
const start = () => {
  if (as.value || md.value) {
    msgBarStatus.value = !msgBarStatus.value
  }
}
const init = () => {
  
}
</script>

<template>
  <div class="main">
    <div class="header">
      <div class="left">daka</div>
      <div class="right">
        <n-popover trigger="click" :show="msgBarStatus">
          <template #trigger>
            <n-button text @click="msgBarStatus=!msgBarStatus">
              <template #icon>
                <n-icon size="30">
                  <MenuOutline/>
                </n-icon>
              </template>
            </n-button>
          </template>
          <div>
            <div class="input">
              <n-input style="width: 80px;margin-right: 5px" size="small" v-model:value="number"
                       placeholder="输入数字"/>
              <span>以内(包含)的</span>
            </div>
            <div class="optionsBtn">
              <n-checkbox v-model:checked="as">加减</n-checkbox>
              <n-checkbox v-model:checked="md">乘除</n-checkbox>
              <n-checkbox v-model:checked="decimal">小数</n-checkbox>
            </div>
            <div class="startBtn">
              <n-button style="width: 100%" strong secondary type="success" @click="start()">开始</n-button>
            </div>
          </div>
        </n-popover>
      </div>
    </div>
    <div class="content">
      <div class="question">1+2=</div>
      <div class="numberBtn">
        <n-grid x-gap="12" y-gap="8" :cols="3">
          <n-gi>
            <div class="btn" @click="btnTrans('7')">7</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('8')">8</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('9')">9</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('4')">4</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('5')">5</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('6')">6</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('1')">1</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('2')">2</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('3')">3</div>
          </n-gi>
          <n-gi :span="2">
            <div class="btn" @click="btnTrans('0')">0</div>
          </n-gi>
          <n-gi>
            <div class="btn" @click="btnTrans('.')">.</div>
          </n-gi>
        </n-grid>
      </div>
    </div>
  </div>
</template>

<style scoped>
.main {
  position: absolute;
  width: 100%;
  height: 100%;
}

.header {
  width: 100%;
  height: 50px;
  display: flex;
  justify-content: space-between;
}

.header .left {
  margin: 10px;
}

.header .right {
  margin: 10px;
}

.header ion-icon {
  font-size: 30px;
}

.header ion-icon:active {
  color: #079d4b;
}

.startBtn {
  margin-top: 5px;
}

.optionsBtn {
  margin-top: 5px;
  text-align: center;
}

.content {
  width: 100%;
  height: 50%;
  margin: 0 auto;
}

.content .question {
  width: 100%;
  margin-top: 100px;
  text-align: center;
  font-size: 40px;
  font-weight: bold;
}

.content .numberBtn {
  margin: 80px 20px 0;
}

.btn {
  height: 118px;
  border-radius: 8px;
  background-color: rgba(0, 128, 0, 0.12);
  text-align: center;
  line-height: 118px;
  font-size: 30px;
  transition: all 0.5s ease-in;
}

.btn:active {
  background-color: rgba(0, 128, 0, 0.558);
  transition: all 0.5s ease-out;
}
</style>
