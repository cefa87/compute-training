<script setup lang="ts">
import {computed, ref, watch, watchEffect} from "vue";
import {MenuOutline, PrintOutline} from "@vicons/ionicons5";
import PrintView from "./printView.vue";
import audio_error from './assets/audio_error.mp3'
import audio_success from './assets/audio_success.mp3'

// Clearinput
const clearInput = () => {
  btnKey.value = ''
  anwser.value = ''
}

// 时间
const startTime = ref()
const endTime = ref()
const timeShow = (i:number)=>{
  i=Math.floor(i/1000)
  const hours = Math.floor(i / 3600);
  const minutes = Math.floor((i % 3600) / 60);
  const remainingSeconds = i % 60;
  return `${hours}小时${minutes}分钟${remainingSeconds}秒`;
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
const range = ref('20')
const rangeOfNumber = computed(()=>{
  return parseInt(range.value);
})
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
    startTime.value = new Date()
    console.log(startTime.value)
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
      if (decimal.value && op !== '÷') {
        x = parseFloat((Math.random() * rangeOfNumber.value).toFixed(1))
        y = parseFloat((Math.random() * rangeOfNumber.value).toFixed(1))
      } else {
        x = Math.floor(Math.random() * rangeOfNumber.value)
        y = Math.floor(Math.random() * rangeOfNumber.value)
      }
      if (op === '-' && x <= y) {
        continue
      }
      if (!decimal.value && op === '÷' && x % y != 0 || y == 0) {
        continue
      }
      if (decimal.value && op === '÷' && !judgeDivisor(x, y) || y == 0) {
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

// 判断2个数能否除尽
const judgeDivisor = (m: any, n: any) => {
  var num: any = {};
  var i = 0;
  m = m % n;
  var result = "";
  while (m != 0 && !(m in num)) {
    num[m] = i++;
    result += parseInt(String(m * 10 / n));
    m = m * 10 % n;
  }
  return m == 0;
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
  if (decimal.value) {
    if (op === '+' && a == parseFloat((x + y).toFixed(1))) {
      return true
    } else if (op === '-' && a == parseFloat((x - y).toFixed(1))) {
      return true
    } else if (op === '×' && a == parseFloat((x * y).toFixed(8))) {
      return true
    } else if (op === '÷' && a == parseFloat((x / y).toFixed(8))) {
      return true
    } else {
      error.value++
      return false
    }
  } else {
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
// 音频提示
const audioError = new Audio(audio_error)
const audioSuccess = new Audio(audio_success)
// 监听答案
watchEffect((onInvalidate) => {
  const k = btnKey.value
  const timer = setTimeout(() => {
    if (anwser.value !== '') {
      answerShowStatus.value = checkAnswer()
      if (answerShowStatus.value) {
        audioSuccess.play()
      } else {
        audioError.play()
      }
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
  if (newVal >= data.value.length) {
    resultShowStatus.value = true
    endTime.value = new Date()
  }
  // console.log('old:', oldVal, 'new', newVal)
})
// reload
const reload = () => {
  location.reload()
}
// 打印状态
const printStatus = ref(false)
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
<!--          @click="msgBarStatus=!msgBarStatus"-->
          <n-button text >
            <template #icon>
              <n-icon size="30">
                <MenuOutline/>
              </n-icon>
            </template>
          </n-button>
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
          <div class="time">用时：{{timeShow(endTime-startTime)}}</div>
          <div class="result">
            <n-progress type="dashboard" gap-position="bottom"
                        :percentage="Math.floor((number-error)/number*100)"
            />
            <p>正确率</p>
          </div>
        </div>
        <div class="resultBtn">
          <n-button strong secondary type="primary" @click="reload()">再来一次</n-button>
        </div>
      </n-card>
    </n-modal>

    <n-modal v-model:show="printStatus">
      <n-card
          style="width: 1000px"
          title="打印预览"
          :bordered="false"
          size="huge"
          role="dialog"
          aria-modal="true"
      >
        <print-view :data="data"></print-view>
        <div class="printClosBtn">
          <n-button @click="reload()">关闭</n-button>
        </div>
      </n-card>
    </n-modal>

    <n-modal :mask-closable="false" v-model:show="msgBarStatus">
      <n-card
          style="width: 350px"
          title="开始"
          :bordered="false"
          size="huge"
          role="dialog"
          aria-modal="true"
      >
        <div>
          <div class="input">
            <div class="number">
              <div class="numberSlider">
                <n-slider v-model:value="number" :step="10" :min="10" :max="100"/>
              </div>
              <span>{{ number }}题</span>
            </div>
            <n-input style="width: 80px;margin:15px 15px 0 0" size="small" v-model:value="range"
                     placeholder="输入数字"/>
            <span>以内的运算</span>
          </div>
          <div class="optionsBtn">
            <n-checkbox v-model:checked="as">加减</n-checkbox>
            <n-checkbox v-model:checked="md">乘除</n-checkbox>
            <n-checkbox v-model:checked="decimal">小数</n-checkbox>
          </div>
          <div class="startBtn">
            <n-button style="width: 78%" strong secondary type="success" @click="start()">开始</n-button>
            <n-popover trigger="hover">
              <template #trigger>
                <n-button style="width: 20%" text @click="printStatus=!printStatus;init()">
                  <template #icon>
                    <n-icon size="24">
                      <PrintOutline/>
                    </n-icon>
                  </template>
                </n-button>
              </template>
              <span>打印题目</span>
            </n-popover>
          </div>
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
  width: 85%;
}

.number span {
  text-align: center;
  line-height: 18px;
  font-size: 14px;
}

.startBtn {
  margin-top: 15px;
  display: flex;
  justify-content: space-between;
}

.optionsBtn {
  margin-top: 15px;
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



.time{
  text-align: center;
  margin-bottom: 10px;
  font-weight: bold;
  font-size: 16px;
}

.result {
  text-align: center;
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

.printClosBtn {
  display: flex;
  justify-content: right;
}
</style>
