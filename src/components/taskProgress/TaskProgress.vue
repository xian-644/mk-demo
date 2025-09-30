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
  // 执行复杂的进入动画序列
  executeComplexAnimation()
})

const executeComplexAnimation = async () => {
  const listItems = document.querySelectorAll('.task-content li')
  const container = document.querySelector('.task-content')
  
  // 初始化所有元素为不可见
  anime.set(listItems, { opacity: 0 })
  
  // 为每个li元素执行动画序列
  for (let i = 0; i < listItems.length; i++) {
    const currentLi = listItems[i]
    const leftEl = currentLi.querySelector('.left')
    const centerEl = currentLi.querySelector('.center')
    const rightEl = currentLi.querySelector('.right')
    const numEl = rightEl.querySelector('.num')

    centerEl.style.opacity = 0;
    
    // 添加动画类
    currentLi.classList.add('animating')
    
    // 获取容器和元素的位置信息
    const containerRect = container.getBoundingClientRect()
    const liRect = currentLi.getBoundingClientRect()
    const containerCenterX = containerRect.width / 2
    const containerCenterY = containerRect.height / 2
    
    // 计算移动到中心的距离
    const moveX = containerCenterX - (currentLi.offsetLeft + currentLi.offsetWidth / 2)
    const moveY = containerCenterY - (currentLi.offsetTop + currentLi.offsetHeight / 2)
    
    // 1. 显示并放大1.5倍移动到中间，同时为其他元素添加蒙层
    // 区分已完成动画的li和未开始动画的li
    const completedLis = Array.from(listItems).filter((_, index) => index < i)  // 已完成的
    const pendingLis = Array.from(listItems).filter((_, index) => index > i)    // 未开始的
    
    // 同时执行当前元素放大和其他元素添加蒙层
    const currentAnimation = anime({
      targets: currentLi,
      opacity: [0, 1],
      scale: [1, 1.5],
      translateX: moveX,
      translateY: moveY,
      duration: 100,
      easing: 'easeOutCubic'
    })
    
    // 已完成的li设为半透明
    const completedMaskAnimation = completedLis.length > 0 ? anime({
      targets: completedLis,
      opacity: 0.3,
      duration: 600,
      easing: 'easeOutQuad'
    }) : Promise.resolve()
    
    // 未开始的li完全隐藏
    const pendingMaskAnimation = pendingLis.length > 0 ? anime({
      targets: pendingLis,
      opacity: 0,
      duration: 600,
      easing: 'easeOutQuad'
    }) : Promise.resolve()
    
    await Promise.all([currentAnimation.finished, completedMaskAnimation, pendingMaskAnimation])
    
    // // 2. 隐藏center元素
    // await anime({
    //   targets: centerEl,
    //   opacity: 0,
    //   scale: 0.8,
    //   duration: 300,
    //   easing: 'easeOutQuad'
    // }).finished
    
    // 3. left和right碰撞动画 - 使用更流畅的单一动画曲线
    const collisionDistance = 65
    
    // 创建自定义的碰撞回弹动画时间线
    const collisionTimeline = anime.timeline({
      easing: 'linear'
    })
    
    collisionTimeline
      .add({
        targets: [leftEl, rightEl],
        translateX: function(el) {
          return el === leftEl ? collisionDistance : -collisionDistance
        },
        duration: 450,
        easing: 'easeInOutQuart'
      })
      .add({
        targets: [leftEl, rightEl],
        translateX: function(el) {
          return el === leftEl ? collisionDistance * 0.3 : -collisionDistance * 0.3
        },
        duration: 120,
        easing: 'easeOutCubic'
      }, '-=50')
      .add({
        targets: [leftEl, rightEl],
        translateX: 0,
        duration: 380,
        easing: 'easeOutQuint'
      })
    
    await collisionTimeline.finished
    
    // 5. center从底部进入
    anime.set(centerEl, {
      translateY: 60,
      opacity: 0,
      scale: 0.8
    })
    
    await anime({
      targets: centerEl,
      translateY: 0,
      opacity: 1,
      scale: 1,
      duration: 600,
      easing: 'easeOutBack(1.7)'
    }).finished
    
    // 6. 所有数字同时动态变化动画 + 图片旋转
    const targetPending = taskData.value[i].pending
    const targetCompleted = taskData.value[i].completed
    const targetProgress = taskData.value[i].progress
    
    // 获取right区域的图片元素
    const positiveImg = rightEl.querySelector('.positive')
    const negativeImg = rightEl.querySelector('.negative')
    
    // 创建临时对象用于动画计算
    const tempNumbers = { pending: 0, completed: 0, progress: 0 }
    
    // 同时执行数字动画和图片旋转动画
    const numberAnimation = anime({
      targets: tempNumbers,
      pending: targetPending,
      completed: targetCompleted,
      progress: targetProgress,
      duration: 1200,
      easing: 'easeOutQuart',
      update: function() {
        // 将计算结果四舍五入后赋值给显示数据
        displayData.value[i].pending = Math.round(tempNumbers.pending)
        displayData.value[i].completed = Math.round(tempNumbers.completed)
        displayData.value[i].progress = Math.round(tempNumbers.progress)
      }
    })
    
    // 图片旋转动画 - 快速转动三圈
    const imageRotation = anime({
      targets: [positiveImg, negativeImg],
      rotate: function(el) {
        // positive图片正转三圈，negative图片反转三圈
        return el.classList.contains('positive') ? '1080deg' : '-1080deg'
      },
      duration: 1200,
      easing: 'easeInOutQuart'
    })
    
    await Promise.all([numberAnimation.finished, imageRotation.finished])
    
    // 等待一下让用户看到最终结果
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // 7. 恢复原始大小并移动到最终位置，同时调整其他元素的透明度
    const restoreAnimation = anime({
      targets: currentLi,
      scale: 1,
      translateX: 0,
      translateY: 0,
      duration: 700,
      easing: 'easeInOutQuart'
    })
    
    // 当前动画完成后，当前元素也变为已完成状态
    // 所有已完成的元素（包括当前元素）恢复完全可见
    const allCompletedLis = Array.from(listItems).filter((_, index) => index <= i)
    const stillPendingLis = Array.from(listItems).filter((_, index) => index > i)
    
    const restoreCompletedAnimation = anime({
      targets: allCompletedLis,
      opacity: 1,
      duration: 500,
      easing: 'easeOutQuad'
    })
    
    // 未开始的li保持隐藏状态
    const keepPendingHidden = stillPendingLis.length > 0 ? anime({
      targets: stillPendingLis,
      opacity: 0,
      duration: 500,
      easing: 'easeOutQuad'
    }) : Promise.resolve()
    
    await Promise.all([restoreAnimation.finished, restoreCompletedAnimation.finished, keepPendingHidden])
    
    // 移除动画类
    currentLi.classList.remove('animating')
    
    // 重置所有子元素的transform
    anime.set([leftEl, centerEl, rightEl], {
      translateX: 0,
      translateY: 0,
      scale: 1
    })
    
    // 短暂延迟后开始下一个动画
    await new Promise(resolve => setTimeout(resolve, 300))
  }
  
  // 所有li都展示完成后，开始循环动画
  await startCycleAnimation()
}

// 循环动画函数
const startCycleAnimation = async () => {
  const listItems = document.querySelectorAll('.task-content li')
  const container = document.querySelector('.task-content')
  
  // 无限循环
  while (true) {
    // 从第一个开始逐一执行循环动画
    for (let i = 0; i < listItems.length; i++) {
      const currentLi = listItems[i]
      const rightEl = currentLi.querySelector('.right')
      const positiveImg = rightEl.querySelector('.positive')
      const negativeImg = rightEl.querySelector('.negative')
      
      // 添加动画类
      currentLi.classList.add('animating')
      
      // 获取容器中心位置
      const containerRect = container.getBoundingClientRect()
      const containerCenterX = containerRect.width / 2
      const containerCenterY = containerRect.height / 2
      
      // 计算移动到中心的距离
      const moveX = containerCenterX - (currentLi.offsetLeft + currentLi.offsetWidth / 2)
      const moveY = containerCenterY - (currentLi.offsetTop + currentLi.offsetHeight / 2)
      
      // 其他li元素
      const otherLis = Array.from(listItems).filter((_, index) => index !== i)
      
      // 1. 放大到中间，其他元素半透明
      const scaleAnimation = anime({
        targets: currentLi,
        scale: 1.5,
        translateX: moveX,
        translateY: moveY,
        duration: 800,
        easing: 'easeOutCubic'
      })
      
      const fadeOthersAnimation = anime({
        targets: otherLis,
        opacity: 0.3,
        duration: 600,
        easing: 'easeOutQuad'
      })
      
      await Promise.all([scaleAnimation.finished, fadeOthersAnimation.finished])
      
      // 2. 图片转动三圈
      const rotationAnimation = anime({
        targets: [positiveImg, negativeImg],
        rotate: function(el) {
          return el.classList.contains('positive') ? '+=1080deg' : '-=1080deg'
        },
        duration: 2000,
        easing: 'easeInOutQuart'
      })
      
      // 3. 等待旋转完成
      await rotationAnimation.finished
      
      // 4. 淡出并恢复原位
      const fadeOutAnimation = anime({
        targets: currentLi,
        opacity: 0.8,
        scale: 1,
        translateX: 0,
        translateY: 0,
        duration: 600,
        easing: 'easeInOutQuart'
      })
      
      const restoreOthersAnimation = anime({
        targets: otherLis,
        opacity: 1,
        duration: 500,
        easing: 'easeOutQuad'
      })
      
      await Promise.all([fadeOutAnimation.finished, restoreOthersAnimation.finished])
      
      // 恢复当前元素完全可见
      await anime({
        targets: currentLi,
        opacity: 1,
        duration: 300,
        easing: 'easeOutQuad'
      }).finished
      
      // 移除动画类
      currentLi.classList.remove('animating')
      
      // 重置旋转角度
      anime.set([positiveImg, negativeImg], { rotate: 0 })
      
      // 短暂延迟后开始下一个
      await new Promise(resolve => setTimeout(resolve, 500))
    }
  }
}

const getImg = (level) => {
  return new URL(`./imgs/${level}.png`, import.meta.url).href
}

const getImgBg = (level) => {
  return new URL(`./imgs/${level}-bg.png`, import.meta.url).href
}

// 任务数据
const taskData = ref([
  {
    level: 'level1',
    label: '严重',
    pending: 12,
    completed: 104,
    progress: 59,
  },
  {
    level: 'level2',
    label: '高危',
    pending: 22,
    completed: 104,
    progress: 89,
  },
  {
    level: 'level3',
    label: '中危',
    pending: 9,
    completed: 32,
    progress: 29,
  },
  {
    level: 'level4',
    label: '低危',
    pending: 78,
    completed: 360,
    progress: 59,
  }
])

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
    width: 140px;
    height: 51px;
    position: relative;
    transform-origin: center center;

    img{
      position: absolute;
      width: 140px;
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