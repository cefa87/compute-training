<script setup lang="ts">
import {computed, ref, watch, watchEffect} from "vue";
import {MenuOutline} from "@vicons/ionicons5";

// Clearinput
const clearInput = () => {
  btnKey.value = ''
  anwser.value = ''
}

// 键盘输入
const btnKey = ref("");
const btnTrans = (key: any) => {
  btnKey.value += key;
  anwser.value = btnKey.value;
  // console.log(btnKey.value);
};
// 运算参数
const number = ref(20)
const range = ref(20)
const as = ref(true)
const md = ref(false)
const decimal = ref(false)
//开始按钮
const msgBarStatus = ref(true)
const start = () => {
  if (as.value || md.value) {
    msgBarStatus.value = !msgBarStatus.value
    init()
    clearInput()
  }
}
// 初始化题目
const init = () => {
  let operator = []
  if (md.value && as.value) {
    operator.push('+', '-', '×', '÷')
  } else if (as.value) {
    operator.push('+', '-')
  } else if (md.value) {
    operator.push('×', '÷')
  }
  // console.log(operator)
  for (let i = 0; i < number.value; i++) {
    let x, y
    let flag = 1
    let op = operator[Math.floor(Math.random() * operator.length)]
    // console.log(op)
    while (flag) {
      x = Math.floor(Math.random() * (range.value))
      y = Math.floor(Math.random() * (range.value))
      if (op === '-' && x <= y) {
        continue
      }
      if (op === '÷' && x % y != 0 || y == 0) {
        continue
      }
      flag = 0
    }
    data.value.push(
        {
          'x': x,
          'y': y,
          'operator': op
        }
    )
  }
}
// data
const data: any = ref([])
const dataFlag = ref(0)
// answer
const anwser = ref('')
// checkAnswer
const checkAnswer = () => {
  let op = data.value[dataFlag.value].operator
  let x = data.value[dataFlag.value].x
  let y = data.value[dataFlag.value].y
  let a = Number(anwser.value)
  // clearInput()
  // answerShow.value=true
  autoNext()
  if (op === '+' && a == x + y) {
    return true
  } else if (op === '-' && a == x - y) {
    return true
  } else if (op === '×' && a == x * y) {
    return true
  } else if (op === '÷' && a == x / y) {
    return true
  } else {
    error.value++
    return false
  }
}
// error
const error = ref(0)
// autoNext
const autoNext = () => {
  setTimeout(() => {
    answerShow.value = false
    dataFlag.value++
    clearInput()
  }, 1000)
}
// 答案显示
const answerShow = ref(false)
const answerShowStatus = ref(false)
const answerColor = computed(() => {
  return answerShowStatus.value ? '#18a058' : '#d03050'
})
// 监听答案
watchEffect((onInvalidate) => {
  const k = btnKey.value
  const timer = setTimeout(() => {
    if (anwser.value !== '') {
      answerShowStatus.value = checkAnswer()
      answerShow.value = true
      console.log(k)
    }
  }, 1000)
  onInvalidate(() => clearTimeout(timer))
})
// 结果显示
const resultShowStatus = ref(false)
// 监听dataFlag
watch(dataFlag, (newVal) => {
  if (newVal == data.value.length) {
    resultShowStatus.value = true
  }
  // console.log('old:', oldVal, 'new', newVal)
})
// angin
const angin=()=>{
  location.reload()
}
</script>

<template>
  <n-modal-provider>
    <div class="main">
      <div class="header">
        <div class="left">
          <div v-show="data[dataFlag]">
            {{ dataFlag + 1 }}/{{ number }}
          </div>
        </div>
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
                <div class="number">
                  <div class="numberSlider">
                    <n-slider v-model:value="number" :step="10" :min="20" :max="100"/>
                  </div>
                  <span>{{ number }}题</span>
                </div>
                <n-input style="width: 80px;margin:5px 5px 0 0" size="small" v-model:value="range"
                         placeholder="输入数字"/>
                <span>以内的运算</span>
              </div>
              <div class="optionsBtn">
                <n-checkbox v-model:checked="as">加减</n-checkbox>
                <n-checkbox v-model:checked="md">乘除</n-checkbox>
                <n-checkbox v-model:checked="decimal" disabled>小数</n-checkbox>
              </div>
              <div class="startBtn">
                <n-button style="width: 100%" strong secondary type="success" @click="start()">开始</n-button>
              </div>
            </div>
          </n-popover>
        </div>
      </div>
      <div class="content">
        <div class="question" v-if="data[dataFlag]">
          {{ data[dataFlag].x }}{{ data[dataFlag].operator }}{{ data[dataFlag].y }}=
          <span :style="answerShow?`color:${answerColor}`:''">{{ anwser }}</span>
        </div>
        <div class="question" v-if="!data[dataFlag]">1+1=?</div>
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

    <n-modal v-model:show="resultShowStatus" :mask-closable="false">
      <n-card
          style="width: 500px"
          title="结果"
          :bordered="false"
          size="huge"
          role="dialog"
          aria-modal="true"
      >
        <div class="resultContent">
          <div class="result">
            <n-progress type="dashboard" gap-position="bottom"
                        :percentage="Math.floor((number-error)/number*100)"
            />
            <p>正确率</p>
          </div>
        </div>
        <div class="resultBtn">
          <n-button strong secondary type="primary" @click="angin()">再来一次</n-button>
        </div>
      </n-card>
    </n-modal>
  </n-modal-provider>
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

.number {
  display: flex;
  justify-content: space-between;
}

.numberSlider {
  width: 75%;
}

.number span {
  text-align: center;
  line-height: 18px;
  font-size: 14px;
}

.startBtn {
  margin-top: 5px;
}

.optionsBtn {
  margin-top: 5px;
  text-align: center;
}

.content {
  height: 50%;
  margin: 0 auto;
}

.content .question {
  margin-top: 100px;
  text-align: center;
  font-size: 40px;
  font-weight: bold;
}

.content .numberBtn {
  width: 380px;
  margin: 80px auto 0;
}

.btn {
  height: 118px;
  border-radius: 8px;
  background-color: rgba(0, 128, 0, 0.12);
  text-align: center;
  line-height: 118px;
  font-size: 30px;
  transition: all 0.3s ease-in;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 2px 5px -1px, rgba(0, 0, 0, 0.3) 0px 1px 3px -1px;
}

.btn:active {
  background-color: rgba(0, 128, 0, 0.32);
  transition: all 0.3s ease-out;
}

.resultContent {
  display: flex;
  justify-content: center;
}

.result p {
  margin-top: -20px;
  font-size: 16px;
  text-align: center;
  font-weight: bold;
}
.resultBtn {
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
</style>
