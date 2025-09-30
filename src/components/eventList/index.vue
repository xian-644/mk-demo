<template>
<div class="event-list-wrapper">
  <div class="event-list-content">
    <div class="title">
      <img src="./imgs/title.png" alt="" srcset="">
    </div>

    <div class="event-list">
      <div class="left">
        <div class="sector"></div>
        <ul class="img-list" id="anime-left">
          <li v-for="el in imgList" :key="el.key" :class="`img${el.key}`">
            <img :src="el.img" alt="" srcset="" class="ratio-img">

            </img>
          </li>

          <li v-for="el in eventList" :key="el.key" :class="`text${el.key}  img_text`">
            <span class="key">{{ el.key }}</span>
            <span class="ratio">占比 <br />{{ el.ratio }}%</span>

            </img>
          </li>
        </ul>
                   

      </div>
      <div class="right">
        <ul class="name-list">
          <li :class="{'activeName': el.key == currentKey}" v-for="el in eventList" :style="`color: ${ el.color }`">{{ el.name }}</li>
        </ul>
        <ul class="data-list">
          <li v-for="el in currentList" :class="el.class">
           <div class="hidden">
            <span class="time">{{ el.time }}</span>
            <span class="origin_to">{{ el.origin }} - - - -▶  {{ el.to }}</span>
            <span class="type">{{ el.type }}</span>
            <span class="level">{{ el.level }}</span>
           </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</div>  
</template>

<script setup>
import anime from 'animejs/lib/anime.es.js'
import { onMounted, ref, onUnmounted } from 'vue'

const props = defineProps({
  eventList: {
    type: Array,
    default: () => []
  },
})

onUnmounted(() => {
  animeTimer && clearInterval(animeTimer);
})

onMounted(() => {
  const leftDom = document.querySelector('#anime-left')
  // 左侧动画
  const animeTask = anime.timeline({
    // 全局 easing（可被每个 add 覆盖）
    easing: 'easeOutQuad'
  }).add({
    targets: leftDom,
    opacity: [0, 1],
    duration: 1000
    //  淡入
  })
  .add({
    // 快速旋转
    targets: leftDom,
    transformOrigin: '50%, 50%', // ← 控制旋转中心点
    rotate: '3turn', // = 360deg
    duration: 1200,

  }).add({
    // 文字淡入
    targets: leftDom.querySelectorAll('.img_text'),
    opacity: [0, 1],
    easing: 'easeOutQuad',
    delay: anime.stagger(1000, { start: 0 }) // 每个子元素延迟 100ms
  }).add({
    // 单位淡入
    targets: document.querySelectorAll('.name-list li'),
    opacity: [0, 1],
    duration: 1000,
    easing: 'easeOutQuad',
    delay: anime.stagger(1000, { start: 0 }), // 每个子元素延迟 100ms
     complete: function () {
     // 前摇结束，开始下一阶段，给list 赋值
      handelListPush()
    }
  }, '-=5000')
  .add({
    // 表格淡出
    targets: document.querySelectorAll('.data-list li'),
    opacity: [0, 1],
    easing: 'easeOutQuad',
    duration: 1000,
    complete: function () {
      // css 动画完成， 开始执行js 动画
      handelJSAnimation()
    }
  })
})

const currentList = ref([])
const currentKey = ref('');
const currentActive = ref(0);
const handelListPush = () => {
  const currentData = props.eventList[currentActive.value];
  currentKey.value = currentData.key;
  currentList.value = currentData.data;
  console.log(currentList.value, 333333)
}

let animeTimer = ref(null);
const handelJSAnimation = () => {
  animeData()
  animeTimer = setInterval(() => {
    animeData()
  }, 2000)
}

const animeData = () => {
  handelListPush();


  document.querySelectorAll('.img-list li img').forEach((item, index) => {
    item.classList.remove('scaleLarger');
  });
  
  const imgDom = document.querySelector(`.img${currentKey.value} img`);
  imgDom.classList.add('scaleLarger');

  // 表格数据一条一条的展示
  anime({
    targets: document.querySelectorAll('.hidden'),
    opacity: [0, 1],
    easing: 'easeOutQuad',
    duration: 1000,
    delay: anime.stagger(300, { start: 0 }), // 每个子元素延迟 100ms
  })
  
  

  currentActive.value = currentActive.value + 1;
  if(currentActive.value >= props.eventList.length){
    currentActive.value = 0;
  }
}

import logo01 from './imgs/01.png'
import logo02 from './imgs/02.png'
import logo03 from './imgs/03.png'
import logo04 from './imgs/04.png'
import logo05 from './imgs/05.png'

const imgList = [
  { img: logo01, key: '01' },
  { img: logo02,  key: '02' },
  { img: logo03,  key: '03' },
  { img: logo04,  key: '04' },
  { img: logo05,  key: '05' },
]




// 画扇形 
</script>

<style scoped lang="less">


.event-list-wrapper {
  width: 100Vw;
  height: 100vh;
  background: #030A18;

  --ration: 72deg;
}

.event-list-content {
  width: 750px;
  height: 400px;
  margin: 0 auto;
}

.event-list{
  height: 360px;
  padding: 0 20px 20px 20px;
  display: flex;
}

.left{
  width: 180px;
  height: 180px;
  margin-right: 30px;
  margin-top: 107px;
  padding: 10px;
  position: relative;
}

.sector {
  width: 140px;
  height: 140px;
  margin: 10px;
  border-radius: 50%;
  overflow: hidden;
  position: absolute;
}
.sector::before {
  content: '';
  display: block;
  width: 100%;
  height: 100%;
  color: #444D58;
  background: radial-gradient(circle, transparent 30%, currentColor 50%);
}




.right{
  height: 100%;
  width: 498px;
}

.name-list{
  display: flex;
  justify-content: space-between;
  list-style: square;
  font-size: 12px;
  line-height: 12px;
  height: 12px;
  margin-top: 20px;
  margin-bottom: 15px;
  margin-left: 20px;
}


.data-list{
  list-style: square;
  font-size: 12px;
  line-height: 12px;
  list-style: none;
}

.data-list li{
  margin-top: 14px;
  width: 498px;
  height: 46px;
  color: #fff;
  font-size: 12px;
  display: flex;
  align-items: center;
  padding: 0 10px ;
  font-size: 10px;
}

.data-list li .time{
  display: inline-block;
  width: 80px;
}

.data-list li .origin_to{
  display: inline-block;
  width: 280px;
}

.data-list li .type{
  display: inline-block;
  width: 60px;
}

.data-list li .level{
  display: inline-block;
  width: 50px;
  border: solid 1px #fff;
  text-align: center;
  font-size: 12px;
  line-height: 12px;
  padding: 4px 0;
  border-radius: 5px;
}

.data-list li.level1 .level{
  border-color: #F72041;
  color: #F72041;
}

.data-list li.level2 .level{
  border-color: #FF8F2D;
  color: #FF8F2D;
}



.data-list li.level1{
  background: rgba(255,0,0,0.11);
  box-shadow: inset 0px 0px 18px 0px rgba(255,0,0,0.28);
}

.data-list li.level2{
  background: rgba(255,143,45,0.11);
  box-shadow: inset 0px 0px 18px 0px rgba(255,143,45,0.28); 
}

.data-list li.level3 .level{
  border-color: rgb(41, 196, 108);
  color: rgb(41, 196, 108);
}

.data-list li.level3{
  background: rgba(41, 196, 108,0.11);
  box-shadow: inset 0px 0px 18px 0px rgba(41, 196, 108, 0.28);
}

.data-list li.level4 .level{
  border-color: rgb(26, 179, 213);
  color: rgb(26, 179, 213);
}

.data-list li.level4{
  background: rgba(26, 179, 213,0.11);
  box-shadow: inset 0px 0px 18px 0px rgba(26, 179, 213,0.28);
}

.img-list{
  list-style: none;
  opacity: 0;
  position: relative;
  width: 100%;
  height: 100%;
}
.img-list li{
  position: absolute;
  left: 37px;
  top: 80px;
  width: 86px;
  height: 86px;
  transform-origin: 50% 0px;
  /* transform: translateY(-60px); */
}


.img-list .img01{
  transform: rotate(calc(3 * var(--ration)));
  .key{
color: #fff;
}
.ratio{
  color: #fff;
}

}

.img-list .img02{

   transform: rotate(calc(4 * var(--ration)));
}

.img-list .img03{
  transform: rotate(calc(5 * var(--ration)));
}

.img-list .img04{

   transform: rotate(calc(6 * var(--ration)));
}

.img-list .img05{
  transform: rotate(calc(2 * var(--ration)));
}

.img_text .key{
  color: white;
  position: absolute;
  display: block;
  font-size: 14px;
}

.img_text .ratio{
  color: white;
  position: absolute;
  display: block;
  font-size: 12px;
  width: 80px;
}

.text01 .key{
  top: -36px;
  left: 50px;
}

.text01 .ratio{
  top: -70px;
  left: 60px;
}

.text02 .key{
  top: -4px;
  left: 60px;
}

.text02 .ratio{
  top: 0;
  left: 86px;
}

.text03 .key{
  top: 20px;
  left: 34px;
}

.text03 .ratio{
  top: 46px;
  left: 30px;
}

.text04 .key{
  top: 0;
  left: 6px;
}

.text04 .ratio{
  top: 0;
  left: -30px;
}

.text05 .key{
  top: -32px;
  left: 18px;
}

.text05 .ratio{
  top: -66px;
  left: -8px;
}


.hidden {
  opacity: 0;
}


.scaleLarger{
  transform: scale(1.2) ;
  z-index: 999;
  transition: all 0.3s ease-in-out;
}

.activeName{
  /* 放大  高亮*/
  transform: scale(1.2) ;
  z-index: 999;
  font-weight: 700;
  transition: all 0.3s ease-in-out;
}

</style>