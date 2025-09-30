<template>
  <div class="task-progress-container">
    <img class="title" src="./imgs/title.png" alt="">
    <ul class="task-content">
      <li v-for="el in taskData" :key="el.level" :class="el.level">
        <div class="left">
          <img :src="getImg(el.level)" alt="">
          <span>{{ el.label }}</span>
        </div>
        <div class="center">
          <img :src="getImgBg(el.level)" alt="" srcset="">
          <div class="content">
            <div>
              <div class="num levelColor">{{ el.pending }}</div>
              <div class="label">待处置</div>
            </div>
            <div>
              <div class="num">{{ el.completed }}</div>
              <div class="label">已处置</div>
            </div>
          </div>
        </div>
        <div class="right">
          <img class="positive" src="./imgs/positive.png" alt="" srcset="">
          <img class="negative" src="./imgs/negative.png" alt="" srcset="">
          <span class="num">{{ el.progress }}</span>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import anime from 'animejs/lib/anime.es.js'
onMounted(() => {
  // 执行进入动画
  const animeTask = anime.timeline({
    // 全局 easing（可被每个 add 覆盖）
    easing: 'easeOutQuad'
  });

  animeTask
    .add({
      targets: '.task-content li',
      opacity: [0, 1],
      translateY: [20, 0],
      delay: anime.stagger(100, { start: 200 }),
      duration: 500,
      easing: 'easeOutQuad'
    })

})

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
    pending: 12,
    completed: 104,
    progress: 59,
  },
  {
    level: 'level3',
    label: '中危',
    pending: 12,
    completed: 104,
    progress: 59,
  },
  {
    level: 'level4',
    label: '低危',
    pending: 12,
    completed: 104,
    progress: 59,
  }
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


  .task-content{
    list-style: none;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;

    li{
      display: flex;
      align-items: center;
      margin-bottom: 12px;
    }
  }

  .left{
    width: 53px;
    height: 51px;
    position: relative;
    span{
      top: 0px;
      font-size: 10px;
      display: block;
      text-align: center;
      color: #fff;
      width: 100%;
      position: absolute;
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
    img{
      position: absolute;
      width: 50px;
      height: 50px;
    }

    .num{
      color: #7be7ff;
      font-size: 12px;
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