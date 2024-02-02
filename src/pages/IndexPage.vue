<script setup>
import { ref, computed, onMounted ,getCurrentInstance } from "vue";

const instance = getCurrentInstance()
const list = ref([
  { title: "好運龍來" },
  { title: "和樂龍龍" },
  { title: "龍情厚意" },
  { title: "錢龍總來" },
  { title: "龍意發財" },
  { title: "龍總五吉" },
  { title: "你龍我龍" },
  { title: "天天龍有錢" },
  { title: "龍化你的心" },
  { title: "龍華富貴" },
])

const bckBoxStyle = computed(() => {
  const rotate = -90 + 180 / list.value.length
  return `transform: rotate(${rotate}deg)`
})

const bckStyle = index => {
  const rotate = (-index * 360) / list.value.length
  const skew = -90 + 360 / list.value.length
  return `transform: rotate(${rotate}deg) skew(${skew}deg)`
}

const calcRotation = (index) => {
  const totalItems = 10;
  const angle = (360 / totalItems) * index;
  return `transform: rotate(calc(${angle}deg + 54deg))`;
}

const jiangStyle = index => {
  const rotate1 = -index * 360/ list.value.length
  return `transform: rotate(${rotate1}deg) translateY(-17.5rem);`
}

const wheel = ref(null)
onMounted(() => {
  wheel.value = instance.refs.wheel
})
const start = () => {
  let baseRotation = 10;
  let randomStop = Math.floor(Math.random() * 360);
  let totalRotation = baseRotation * 360 + randomStop;
  wheel.value.style.transition = 'transform 5s ease-out';
  wheel.value.style.transform = `rotate(${totalRotation}deg)`;

  wheel.value.addEventListener('transitionend', wheelStop);
}

const wheelStop = () => {
  console.log('end')
}

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
        class="tw-overflow-hidden tw-rounded-full tw-absolute tw-w-[40rem] tw-h-[40rem] tw-left-0 tw-top-0 tw-flex tw-justify-center tw-items-center tw-border-4 tw-box-border tw-border-gray-500"
      >
        <div v-for="index in 10" :key="index"
          class="tw-absolute tw-w-[2px] tw-h-[40rem] tw-bg-gray-500 tw-transform"
          :style='calcRotation(index)'
        >
        </div>
        <div 
          class="tw-rounded-full tw-overflow-hidden tw-absolute tw-top-0 tw-left-0 tw-w-full tw-h-full "   :style="[bckBoxStyle,{
            backgroundImage: `url(src/assets/wheelBG.png)`,
            backgroundSize: 'cover',
            backgroundPosition: 'center center',
            backgroundRepeat: 'no-repeat',
            opacity: 0.3,
          }]"
        >
          <div v-for="(i,index) in list" :key="index" :style="bckStyle(index)" ></div>
        </div>
        <div class="tw-absolute" v-for="(i,index) in list" :key="index" :style="jiangStyle(index)">
          <span class="tw-font-bold tw-text-4xl tw-text-gray-700">{{i.title}}</span>
        </div>
      </div>
      <div @click="start()" id="start" class="tw-absolute tw-cursor-pointer tw-border-gray-400 tw-w-28 tw-z-50 tw-top-[35%]">
        <img class="tw-w-full tw-h-full" src="src/assets/arrow.png" alt="arrow">
      </div>
    </div>
  </div>
</template>
