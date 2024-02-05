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
const showAmei = ref(false)
const catHandReady = ref(0) // 0 原始位置 1 伸出 2 向回收 
const catHandSet = ref(false)
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

  setTimeout(() => {
    showAmei.value = true
  }, 2500);
  setTimeout(() => {
    catHandReady.value = 1
  }, 3000);
  setTimeout(() => {
    catHandReady.value = 2
  }, 4950);
  wheel.value.addEventListener('transitionend', wheelStop);
}
// 轉動結束
const winItemIndex = ref(0);
const wheelStop = () => {
  catHandSet.value = true
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
  <div class="tw-mx-auto tw-justify-center tw-items-center tw-flex tw-w-[100vw] tw-h-[100vh] tw-relative"
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
      <!-- 阿妹 -->
      <div
        class="tw-w-[26rem] tw-h-[26rem] tw-absolute tw-transition-all tw-duration-500 tw-ease-linear tw-scale-110"
        :class="{
          'tw-top-[-9.5rem] tw-left-[1rem] tw-opacity-100 tw-scale-125' : showAmei ,
          'tw-top-24 tw-left-0 tw-opacity-50 tw-scale-110' : !showAmei
        }"
      >
        <img src="src/assets/ameiHead.png" class="tw-w-full tw-absolute" alt="cat">
      </div>
      <!-- 貓掌 -->
      <div
        class="tw-w-[7rem] tw-h-[6rem] tw-absolute tw-transition-all tw-duration-500 tw-ease-linear tw-rotate-[-15deg] tw-scale-110"
        :class="{
          'tw-top-12 tw-left-[17.5rem] tw-opacity-50' : catHandReady === 0,
          'tw-top-[-7rem] tw-left-[17.5rem] tw-opacity-100' : catHandReady === 1 ,
          'tw-top-[1rem] tw-left-[16.5rem] tw-opacity-100' : catHandReady === 2 ,
        }"
      >
        <img src="src/assets/catHandOne.png" class="tw-w-full tw-absolute" alt="cat">
      </div>
      <!-- 貓手 -->
      <div
        class="tw-w-[7rem] tw-h-[6rem] tw-absolute tw-transition-all tw-duration-500 tw-ease-linear tw-rotate-[10deg] tw-z-10 tw-scale-100 tw-top-[-1rem] tw-left-[18rem] "
        :class="{
          'tw-opacity-100' : catHandSet ,
          'tw-opacity-0' : !catHandSet
        }"
      >
        <img src="src/assets/catHandTwo.png" class="tw-w-full tw-absolute" alt="cat">
      </div>
      <!--  -->
      <div id="wheel" ref="wheel"
        class="tw-overflow-hidden tw-rounded-full tw-absolute tw-w-[40rem] tw-h-[40rem] tw-left-0 tw-top-0 tw-flex tw-justify-center tw-items-center tw-border-4 tw-border-rose-200 tw-bg-white tw-mt-12"
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
          <span class="tw-font-bold tw-text-4xl tw-text-gray-700 tw-transition-all tw-duration-500"
            :class="{'tw-text-8xl tw-text-orange-500': i.isWin}"
          >
            {{i.title}}
          </span>
        </div>
      </div>
      <div @click="start()" id="start" 
        class="tw-absolute tw-cursor-pointer tw-border-gray-400 tw-w-20 tw-z-40 tw-top-[50%]"
      >
        <img class="tw-w-full tw-h-full" src="src/assets/arrow.png" alt="arrow">
      </div>
    </div>
  </div>
</template>