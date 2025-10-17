<template>
  <div class="task-progress-container">
    <img class="title" src="./imgs/title.png" alt="">
    <ul class="task-content">
      <li v-for="(el, index) in taskData" :key="el.level" :class="el.level">
        <div class="left">
          <img :src="getImg(el.level)" alt="">
          <span>{{ el.label }}</span>
        </div>
        <div class="center">
          <img :src="getImgBg(el.level)" alt="" srcset="">
          <div class="content">
            <div>
              <div class="num levelColor">{{ displayData[index].pending }}</div>
              <div class="label">待处置</div>
            </div>
            <div>
              <div class="num">{{ displayData[index].completed }}</div>
              <div class="label">已处置</div>
            </div>
          </div>
        </div>
        <div class="right">
          <img class="positive" src="./imgs/positive.png" alt="" srcset="">
          <img class="negative" src="./imgs/negative.png" alt="" srcset="">
          <span class="num">{{ displayData[index].progress }}</span>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import anime from 'animejs/lib/anime.es.js'

onMounted(() => {
  // 启动循环控制器
  startNextAnimationCycle()
})

// 创建圆形阴影效果的辅助函数
const createCircularShadow = (element, direction, opacity) => {
  // 移除已存在的阴影
  removeCircularShadow(element)
  
  const shadow = document.createElement('div')
  shadow.className = 'collision-shadow'
  shadow.style.cssText = `
    position: absolute;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background: radial-gradient(circle, rgba(123, 231, 255, ${opacity}) 0%, rgba(87, 225, 255, ${opacity * 0.6}) 30%, rgba(0, 255, 110, ${opacity * 0.3}) 60%, transparent 80%);
    pointer-events: none;
    z-index: 5;
    top: 50%;
    transform: translateY(-50%);
    ${direction === 'right' ? 'left: 100%;' : 'right: 100%;'}
    ${direction === 'right' ? 'margin-left: -5px;' : 'margin-right: -5px;'}
  `
  
  element.style.position = 'relative'
  element.appendChild(shadow)
}

const updateCircularShadow = (element, direction, opacity) => {
  const shadow = element.querySelector('.collision-shadow')
  if (shadow) {
    shadow.style.background = `radial-gradient(circle, rgba(123, 231, 255, ${opacity}) 0%, rgba(87, 225, 255, ${opacity * 0.6}) 30%, rgba(0, 255, 110, ${opacity * 0.3}) 60%, transparent 80%)`
  }
}

const removeCircularShadow = (element) => {
  const shadow = element.querySelector('.collision-shadow')
  if (shadow) {
    shadow.remove()
  }
}

const executeComplexAnimation = async () => {
  const listItems = document.querySelectorAll('.task-content li')
  
  // 动作1：一开始全部隐藏
  // 注意：父级 li 保持可见，只隐藏子元素，否则子元素淡入也不会显示
  anime.set(listItems, { opacity: 1 })
  
  // 动作1：四个图标同时淡入
  const leftElements = Array.from(listItems).map(li => li.querySelector('.left'))
  // 先隐藏左侧图标，避免初始闪现
  anime.set(leftElements, { opacity: 0 })
  // 提前隐藏右侧，避免初始露出
  const rightElementsInit = Array.from(listItems).map(li => li.querySelector('.right'))
  anime.set(rightElementsInit, { opacity: 0 })
  // 提前隐藏中间图片与内容，确保动作1期间只有左侧显示
  const centerImages = Array.from(listItems).map(li => li.querySelector('.center img'))
  const contentElements = Array.from(listItems).map(li => li.querySelector('.content'))
  anime.set(centerImages, { opacity: 0 })
  anime.set(contentElements, { opacity: 0 })

  await anime({
    targets: leftElements,
    opacity: [0, 1],
    duration: 1500,
    easing: 'easeOutQuad'
  }).finished
  // 动作1后暂停500ms
  await new Promise(resolve => setTimeout(resolve, 500))
  
  // 动作2：四个图片框从上至下依次淡入；第3个开始时，触发内容从上至下依次淡入，并逐项开启数字增长
  // 注：centerImages 与 contentElements 已在动作1前声明并设为隐藏
  const imageTimeline = anime.timeline({ easing: 'easeOutQuad' })
  
  for (let i = 0; i < centerImages.length; i++) {
    // 每行图片逐项淡入（每项300ms，间隔500ms）
    imageTimeline.add({
      targets: centerImages[i],
      opacity: [0, 1],
      duration: 300,
      easing: 'easeOutQuad'
    }, i * 500)
    
    // 当第3个图片（索引2）开始时，插入内容逐项淡入（每项300ms，间隔500ms）
    if (i === 2) {
      for (let j = 0; j < contentElements.length; j++) {
        imageTimeline.add({
          targets: contentElements[j],
          opacity: [0, 1],
          duration: 300,
          easing: 'easeOutQuad',
          begin: function() {
            // 内容开始出现时，逐项启动该行的 pending/completed 数字增长
            animateContentNumbers(j)
          }
        }, (i * 500) + (j * 500) + 50)
      }
    }
  }
  
  await imageTimeline.finished
  
  // 动作3：右侧内容按从上到下依次淡入，同时旋转并增长“进度”数字
  const rightElements = Array.from(listItems).map(li => li.querySelector('.right'))
  const positiveImages = Array.from(listItems).map(li => li.querySelector('.positive'))
  const negativeImages = Array.from(listItems).map(li => li.querySelector('.negative'))
  anime.set(rightElements, { opacity: 0 })
  
  for (let i = 0; i < rightElements.length; i++) {
    await anime({
      targets: rightElements[i],
      opacity: [0, 1],
      duration: 400,
      easing: 'easeOutQuad',
      begin: function() {
        // 仅旋转当前行
        startRotationAnimations([positiveImages[i]], [negativeImages[i]])
        // 启动该行的 progress 数字增长
        animateProgressNumber(i)
      }
    }).finished
    // 行与行之间的节奏间隔
    await new Promise(r => setTimeout(r, 300))
  }
  
  // 动作4：四个“待处置”数字按从上到下依次放大高亮，循环2次；每次放大后暂停1s再进行下一项
  const pendingNumbers = Array.from(listItems).map(li => li.querySelector('.content .levelColor'))
  for (let cycle = 0; cycle < 2; cycle++) {
    for (let i = 0; i < pendingNumbers.length; i++) {
      // 放大并高亮
      await anime({
        targets: pendingNumbers[i],
        scale: [1, 1.67],
        duration: 250,
        easing: 'easeOutBack',
        update: function(anim) {
          const progress = anim.progress / 100
          const brightness = 1 + progress * 0.5
          pendingNumbers[i].style.filter = `brightness(${brightness})`
        },
        complete: function() {
          pendingNumbers[i].style.filter = 'brightness(1)'
        }
      }).finished
      // 每项放大后暂停1s
      await new Promise(r => setTimeout(r, 1000))
      // 再还原
      await anime({
        targets: pendingNumbers[i],
        scale: 1,
        duration: 200,
        easing: 'easeInOutQuad'
      }).finished
    }
    // 两轮之间可略微停顿
    if (cycle < 1) {
      await new Promise(r => setTimeout(r, 250))
    }
  }
  
  // 短暂停留后结束本轮，交由循环控制器进入下一轮
  await new Promise(resolve => setTimeout(resolve, 1000))
}

/**
 * 数字动态增长（内容区：pending/completed）
 */
const animateContentNumbers = (i) => {
  const temp = { pending: 0, completed: 0 }
  const targetPending = props.taskData[i]?.pending || 0
  const targetCompleted = props.taskData[i]?.completed || 0
  anime({
    targets: temp,
    pending: targetPending,
    completed: targetCompleted,
    duration: 1200,
    easing: 'easeOutQuart',
    update: function() {
      if (displayData.value[i]) {
        displayData.value[i].pending = Math.round(temp.pending)
        displayData.value[i].completed = Math.round(temp.completed)
      }
    }
  })
}

/**
 * 数字动态增长（右侧：progress）
 */
const animateProgressNumber = (i) => {
  const temp = { progress: 0 }
  const targetProgress = props.taskData[i]?.progress || 0
  anime({
    targets: temp,
    progress: targetProgress,
    duration: 1200,
    easing: 'easeOutQuart',
    update: function() {
      if (displayData.value[i]) {
        displayData.value[i].progress = Math.round(temp.progress)
      }
    }
  })
}

// 旋转动画
const startRotationAnimations = (positiveImages, negativeImages) => {
  // 正图片旋转动画
  anime({
    targets: positiveImages,
    rotate: '+=1080deg',
    duration: 1500,
    easing: 'easeInOutQuart'
  })
  
  // 负图片旋转动画
  anime({
    targets: negativeImages,
    rotate: '-=1080deg',
    duration: 1500,
    easing: 'easeInOutQuart'
  })
}

// 下轮动画循环
const startNextAnimationCycle = async () => {
  const listItems = document.querySelectorAll('.task-content li')

  while (true) {
    // 执行一整轮动画（动作1-4）
    await executeComplexAnimation()

    // 动作5：本轮淡出
    await anime({
      targets: listItems,
      opacity: 0,
      duration: 500,
      easing: 'easeOutQuad'
    }).finished

    // 短暂停留
    await new Promise(resolve => setTimeout(resolve, 500))

    // 重置数字为0
    displayData.value.forEach(item => {
      item.pending = 0
      item.completed = 0
      item.progress = 0
    })

    // 重置所有transform与可见性，准备下一轮
    anime.set(listItems, {
      opacity: 1,
      scale: 1,
      translateX: 0,
      translateY: 0
    })
  }
}



const getImg = (level) => {
  return new URL(`./imgs/${level}.png`, import.meta.url).href
}

const getImgBg = (level) => {
  return new URL(`./imgs/${level}-bg.png`, import.meta.url).href
}


const props = defineProps({
  taskData: {
    type: Array,
    required: true
  }
})


// 显示数据（用于动画）
const displayData = ref([
  { pending: 0, completed: 0, progress: 0 },
  { pending: 0, completed: 0, progress: 0 },
  { pending: 0, completed: 0, progress: 0 },
  { pending: 0, completed: 0, progress: 0 }
])
</script>

<style scoped lang="less">

.title{
  width: 454px;
}
.task-progress-container{
  color: #fff;
  margin: 0 auto;
  width: 454px;
  height: auto;
  height: 312px;


  .task-content{
    list-style: none;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    position: relative;
    overflow: visible;

    li{
      display: flex;
      align-items: center;
      margin-bottom: 12px;
      position: relative;
      transform-origin: center center;
      z-index: 1;
      
      &.animating {
        z-index: 10;
      }
    }
  }

  .left{
    width: 53px;
    height: 51px;
    position: relative;
    transform-origin: center center;
    
    span{
      top: 0px;
      font-size: 10px;
      display: block;
      text-align: center;
      color: #fff;
      width: 100%;
      position: absolute;
      z-index: 2;
    }
    img{
      width: 53px;
      height: 51px;
    }
  }

  .center{
    margin-right: 12px;
    width: 302px;
    height: 51px;
    position: relative;
    transform-origin: center center;

    img{
      position: absolute;
      width: 302px;
      height: 51px;
    }

    .content{
      height: 100%;
      display: flex;
      justify-content: space-around;
      align-items: center;
      padding: 0 20px;
      text-align: center;
      position: relative;
      z-index: 2;
    }

    .num{
      font-size: 18px;
      font-weight: 600;
    }

    .label{
      font-size: 12px;
    }
  }

  .right{
    width: 50px;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    transform-origin: center center;
    
    img{
      position: absolute;
      width: 50px;
      height: 50px;
    }

    .num{
      color: #7be7ff;
      font-size: 12px;
      position: relative;
      z-index: 2;
      font-weight: bold;
    }
  }
}

.level1 .content .levelColor{
  color: #FF3548;
}

.level2 .content .levelColor{
  color: #F3923C;
}

.level3 .content .levelColor{
  color: #00FF6E;
}

.level4 .content .levelColor{
  color: #57E1FF;
}
</style>