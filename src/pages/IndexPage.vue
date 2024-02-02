<script setup>
import { ref, computed, onMounted ,getCurrentInstance } from "vue";

const instance = getCurrentInstance()
const list = ref([
  { title: "好運龍來",isWin:false },
  { title: "和樂龍龍",isWin:false },
  { title: "龍情厚意",isWin:false },
  { title: "錢龍總來",isWin:false },
  { title: "龍意發財",isWin:false },
  { title: "龍總五吉",isWin:false },
  { title: "你龍我龍",isWin:false },
  { title: "天天龍有錢",isWin:false },
  { title: "龍化你的心",isWin:false },
  { title: "龍華富貴",isWin:false },
])

const bckBoxStyle = computed(() => {
  const rotate = (180 / list.value.length) - 90
  return `transform: rotate(${rotate}deg)`
})

const bckStyle = index => {
  const rotate = index * (360 / list.value.length)
  const skew = (360 / list.value.length) - 90
  return `transform: rotate(${rotate}deg) skew(${skew}deg)`
}

const calcRotation = (index) => {
  const totalItems = 10;
  const angle = (360 / totalItems) * index;
  return `transform: rotate(calc(${angle}deg + 54deg))`;
}

const textPosition = (index,isWin) => {
  const rotate = (-index * 360)/ list.value.length
  let style = `rotate(${rotate}deg) translateY(-17.5rem)`;
  if(isWin){
    style += `translateY(7rem)`;
  } 
  return {
    transform: style,
  }
}

const wheel = ref(null)
onMounted(() => {
  wheel.value = instance.refs.wheel
})
// 開始
const start = () => {
  let baseRotation = 15;
  let randomStop = Math.floor(Math.random() * 360);
  let totalRotation = baseRotation * 360 + randomStop;
  wheel.value.style.transition = 'transform 5s ease-out';
  wheel.value.style.transform = `rotate(${totalRotation}deg)`;

  wheel.value.addEventListener('transitionend', wheelStop);
}
// 轉動結束
const winItemIndex = ref(0);
const wheelStop = () => {
  const segmentAngle = 360 / list.value.length;
  const style = window.getComputedStyle(wheel.value);
  const matrix = new WebKitCSSMatrix(style.transform);
  let angle = Math.atan2(matrix.m21, matrix.m11);
  // 將弧度轉換為角度
  angle = angle * (180 / Math.PI);
  // 計算偏移
  const offset = -18 ;
  angle = (angle + offset) % 360;
  // 計算索引
  winItemIndex.value = Math.floor((360 - angle) / segmentAngle) % list.value.length;
  list.value[winItemIndex.value].isWin = true;
}
// 中獎項目
const winningItems = computed(() => {
  return list.value.filter(item => item.isWin);
});

</script>
<template>
  <div class="tw-mx-auto tw-justify-center tw-items-center tw-flex tw-w-[100vw] tw-h-[100vh]"
    :style="{
      backgroundImage: `url(src/assets/flowerBg.png)`,
      backgroundSize: 'cover',
      backgroundPosition: 'center center',
      backgroundRepeat: 'no-repeat',
    }"
  >
    <div 
      class="tw-select-none tw-flex tw-justify-center tw-items-center tw-h-[40rem] tw-w-[40rem] tw-relative"
    >
      <div id="wheel" ref="wheel"
        class="tw-overflow-hidden tw-rounded-full tw-absolute tw-w-[40rem] tw-h-[40rem] tw-left-0 tw-top-0 tw-flex tw-justify-center tw-items-center tw-border-4 tw-border-rose-200"
      >
        <!-- 分割線 -->
        <div v-for="index in 10" :key="index"
          class="tw-absolute tw-w-[2px] tw-h-[40rem] tw-bg-gray-500 tw-transform"
          :style='calcRotation(index)'
        >
        </div>
        <!-- 輪盤背景 -->
        <div 
          class="tw-rounded-full tw-absolute tw-top-0 tw-left-0 tw-w-full tw-h-full"   
          :style="[bckBoxStyle,{
            backgroundImage: `url(src/assets/wheelBG.png)`,
            backgroundSize: 'cover',
            backgroundPosition: 'center center',
            backgroundRepeat: 'no-repeat',
            opacity: 0.3,
          }]"
        >
          <div v-for="(i,index) in list" :key="index" :style="bckStyle(index)" ></div>
        </div>
        <!-- 輪盤文字 -->
        <div v-for="(i,index) in list" :key="index" 
          class="tw-absolute" :class="{'tw-z-40': i.isWin}"
          :style="textPosition(index,i.isWin)"
        >
          <span class="tw-font-bold tw-text-4xl tw-text-gray-700 tw-transition-all tw-duration-500 tw-ease-in-out"
            :class="{'tw-text-8xl tw-text-orange-500': i.isWin}"
          >
            {{i.title}}
          </span>
        </div>
      </div>
      <div @click="start()" id="start" 
        class="tw-absolute tw-cursor-pointer tw-border-gray-400 tw-w-20 tw-z-40 tw-top-[40%]"
      >
        <img class="tw-w-full tw-h-full" src="src/assets/arrow.png" alt="arrow">
      </div>
    </div>
  </div>
</template>
