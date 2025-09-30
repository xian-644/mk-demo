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
      duration: 10,
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
    
    // 3. left和right碰撞动画 - 先设置初始透明度，然后移动并显示，最后碰撞时添加阴影
    const collisionDistance = 80
    
    // 初始设置left和right为透明
    anime.set([leftEl, rightEl], { opacity: 0 })
    
    // 创建自定义的碰撞回弹动画时间线
    const collisionTimeline = anime.timeline({
      easing: 'linear'
    })
    
    collisionTimeline
      .add({
        targets: [leftEl, rightEl],
        opacity: [0, 1], // 透明度从0到1
        translateX: function(el) {
          return el === leftEl ? collisionDistance : -collisionDistance
        },
        duration: 800,
        easing: 'easeInOutQuart'
      })
      .add({
        targets: [leftEl, rightEl],
        translateX: function(el) {
          return el === leftEl ? collisionDistance * 0.3 : -collisionDistance * 0.3
        },
        duration: 120,
        easing: 'easeOutCubic',
        begin: function() {
          // 碰撞开始时添加圆形阴影效果，使用伪元素创建圆形光晕
          createCircularShadow(leftEl, 'right', 0.8)
          createCircularShadow(rightEl, 'left', 0.8)
        },
        update: function(anim) {
          // 回弹阶段逐渐减少阴影
          const progress = anim.progress / 100
          const opacity = (1 - progress * 0.5) * 0.6
          
          updateCircularShadow(leftEl, 'right', opacity)
          updateCircularShadow(rightEl, 'left', opacity)
        }
      }, '-=50')
      .add({
        targets: [leftEl, rightEl],
        translateX: 0,
        duration: 380,
        easing: 'easeOutQuint',
        update: function(anim) {
          // 恢复阶段逐渐减少阴影直到消失
          const progress = anim.progress / 100
          const opacity = (1 - progress) * 0.4
          
          if (opacity > 0.05) {
            updateCircularShadow(leftEl, 'right', opacity)
            updateCircularShadow(rightEl, 'left', opacity)
          } else {
            removeCircularShadow(leftEl)
            removeCircularShadow(rightEl)
          }
        },
        complete: function() {
          // 动画完成后确保清除阴影
          removeCircularShadow(leftEl)
          removeCircularShadow(rightEl)
        }
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
    const targetPending = props.taskData[i].pending
    const targetCompleted = props.taskData[i].completed
    const targetProgress = props.taskData[i].progress
    
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
    // await new Promise(resolve => setTimeout(resolve, 300))
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
      
      // 其他li元素 - 只包含还未执行过动画的元素（索引大于当前i的）
      const otherLis = Array.from(listItems).filter((_, index) => index > i)
      
      // 1. 放大到中间，其他未执行动画的元素半透明
      const scaleAnimation = anime({
        targets: currentLi,
        scale: 1.5,
        translateX: moveX,
        translateY: moveY,
        duration: 800,
        easing: 'easeOutCubic'
      })
      
      const fadeOthersAnimation = otherLis.length > 0 ? anime({
        targets: otherLis,
        opacity: 0.3,
        duration: 600,
        easing: 'easeOutQuad'
      }) : Promise.resolve()
      
      await Promise.all([scaleAnimation.finished, fadeOthersAnimation])
      
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
      
      // 4. 当前元素回到原位并隐藏（不可见）
      await anime({
        targets: currentLi,
        opacity: 0,
        scale: 1,
        translateX: 0,
        translateY: 0,
        duration: 600,
        easing: 'easeInOutQuart'
      }).finished
      
      // 设置为 visibility: hidden 确保完全不可见
      currentLi.style.visibility = 'hidden'
      
      // 5. 如果还有未执行的元素，恢复它们的透明度为1
      if (otherLis.length > 0) {
        await anime({
          targets: otherLis,
          opacity: 1,
          duration: 500,
          easing: 'easeOutQuad'
        }).finished
      }
      
      // 移除动画类
      currentLi.classList.remove('animating')
      
      // 重置旋转角度
      anime.set([positiveImg, negativeImg], { rotate: 0 })
      
      // 短暂延迟后开始下一个
      await new Promise(resolve => setTimeout(resolve, 500))
    }
    
    // 所有元素都完成循环后，全部隐藏
    await anime({
      targets: listItems,
      opacity: 0,
      duration: 0,
      easing: 'easeInOutQuart'
    }).finished
    
    // 等待一段时间，然后重新开始完整动画
    await new Promise(resolve => setTimeout(resolve, 0))
    
    // 重置所有元素的visibility和opacity，准备重新开始动画
    listItems.forEach(li => {
      li.style.visibility = 'visible'
      li.style.opacity = '0'
    })
    
    // 重置所有显示数据为0，准备重新开始动画
    displayData.value.forEach(item => {
      item.pending = 0
      item.completed = 0
      item.progress = 0
    })
    
    // 重新执行完整的动画序列
    await executeComplexAnimation()
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