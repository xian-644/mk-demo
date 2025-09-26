<template>
  <div class="task-progress-container">
    <!-- 标题 -->
    <div class="title-bar">
      <div class="title-icon"></div>
      <div class="title-text">实时任务处置进展</div>
    </div>
    
    <!-- 数据卡片列表 -->
    <div class="cards-container">
      <div 
        v-for="(item, index) in taskData" 
        :key="index"
        class="task-card"
        :class="item.type"
      >
      <img style="position: absolute; left: 0" :src="item.img" alt="">
        <!-- 左侧图标 -->
        <div class="card-title">{{ item.label }}</div>
        
        <!-- 中间数据区域 -->
        <div class="card-content">
          <div class="data-section">
            <div class="data-number">{{ item.pending }}</div>
            <div class="data-label">待处置</div>
          </div>
          <div class="data-section">
            <div class="data-number">{{ item.completed }}</div>
            <div class="data-label">已处置</div>
          </div>
        </div>
        
        <!-- 右侧进度圆环 -->
        <div class="progress-wrapper">
          <div class="progress-circle">
            <svg width="100" height="100" viewBox="0 0 100 100">
              <!-- 外圈刻度 -->
              <g class="tick-marks">
                <circle v-for="i in 100" :key="i"
                  :cx="50 + 42 * Math.cos((i - 1) * 3.6 * Math.PI / 180)"
                  :cy="50 + 42 * Math.sin((i - 1) * 3.6 * Math.PI / 180)"
                  r="0.5"
                  :fill="i % 10 === 0 ? '#4a9eff' : 'rgba(255,255,255,0.2)'"
                />
              </g>
              <!-- 背景圆环 -->
              <circle
                cx="50"
                cy="50"
                r="35"
                fill="none"
                stroke="rgba(255,255,255,0.1)"
                stroke-width="1"
              />
              <!-- 进度圆环 -->
              <circle
                cx="50"
                cy="50"
                r="35"
                fill="none"
                :stroke="item.progressColor"
                stroke-width="2"
                stroke-linecap="round"
                :stroke-dasharray="circumference"
                :stroke-dashoffset="circumference - (item.progress / 100) * circumference"
                transform="rotate(-90 50 50)"
                class="progress-ring"
              />
            </svg>
            <!-- 进度百分比 -->
            <div class="progress-text">
              <span class="progress-number">{{ item.progress }}</span>
              <span class="progress-unit">%</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import criticalImg from './imgs/严重.png' 
import highImg from './imgs/高危.png'
import mediumImg from './imgs/中危.png'
import lowImg from './imgs/低危.png'


// 圆环周长
const circumference = computed(() => 2 * Math.PI * 35)

// 任务数据
const taskData = ref([
  {
    type: 'critical',
    label: '严重',
    pending: 12,
    completed: 104,
    progress: 59,
    progressColor: '#00ffff',
    img: criticalImg
  },
  {
    type: 'high',
    label: '高危',
    pending: 12,
    completed: 104,
    progress: 59,
    progressColor: '#00ffff',
    img: highImg
  },
  {
    type: 'medium',
    label: '中危',
    pending: 12,
    completed: 104,
    progress: 59,
    progressColor: '#00ffff',
    img: mediumImg
  },
  {
    type: 'low',
    label: '低危',
    pending: 12,
    completed: 104,
    progress: 59,
    progressColor: '#00ffff',
    img: lowImg
  }
])
</script>

<style scoped>

.card-title{
  width: 86px;
  height: 16px;
  font-family: PingFangSC, PingFang SC;
  font-weight: 600;
  font-size: 16px;
  color: #FFFFFF;
  line-height: 16px;
  font-style: normal;
  z-index: 2;
  text-align: center;
  margin-right: 46px;
}

.task-progress-container {
  background: #000;
  color: #fff;
  padding: 30px;
  font-family: 'Microsoft YaHei', Arial, sans-serif;
  min-height: 100vh;
  max-width: 800px;
  margin: 0 auto;
}

.title-bar {
  background: linear-gradient(135deg, #1a4480 0%, #2d5aa0 50%, #1a4480 100%);
  border: 1px solid #4a9eff;
  border-radius: 6px;
  padding: 15px 25px;
  margin-bottom: 40px;
  position: relative;
  display: flex;
  align-items: center;
  box-shadow: 0 0 15px rgba(74, 158, 255, 0.3);
}

.title-bar::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 4px;
  background: #4a9eff;
  border-radius: 4px 0 0 4px;
}

.title-icon {
  width: 20px;
  height: 20px;
  background: #4a9eff;
  margin-right: 15px;
  clip-path: polygon(0 0, 100% 50%, 0 100%);
}

.title-text {
  font-size: 20px;
  font-weight: bold;
  color: #fff;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
}

.cards-container {
  display: flex;
  flex-direction: column;
  gap: 25px;
}

.task-card {
  display: flex;
  align-items: center;
  padding: 25px 30px;
  border-radius: 15px;
  border: 2px solid;
  position: relative;
  overflow: hidden;
}

.task-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  opacity: 0.8;
  border-radius: 13px;
}



.card-icon {
  margin-right: 40px;
  position: relative;
  z-index: 2;
}

.icon-base {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 16px;
  position: relative;
  overflow: hidden;
}

.icon-base.critical {
  background: linear-gradient(135deg, #8B0000 0%, #CD5C5C 100%);
  border: 2px solid #8B0000;
}

.icon-base.high {
  background: linear-gradient(135deg, #B8860B 0%, #DAA520 100%);
  border: 2px solid #B8860B;
}

.icon-base.medium {
  background: linear-gradient(135deg, #228B22 0%, #32CD32 100%);
  border: 2px solid #228B22;
}

.icon-base.low {
  background: linear-gradient(135deg, #1E90FF 0%, #87CEEB 100%);
  border: 2px solid #1E90FF;
}

.icon-glow {
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  border-radius: 50%;
  opacity: 0.6;
  filter: blur(8px);
}

.icon-glow.critical {
  background: #8B0000;
}

.icon-glow.high {
  background: #B8860B;
}

.icon-glow.medium {
  background: #228B22;
}

.icon-glow.low {
  background: #1E90FF;
}

.icon-text {
  position: relative;
  z-index: 3;
  color: #fff;
  text-shadow: 0 0 5px rgba(0, 0, 0, 0.8);
}

.card-content {
  flex: 1;
  display: flex;
  gap: 80px;
  margin-right: 40px;
  position: relative;
  z-index: 2;
}

.data-section {
  text-align: center;
}

.data-number {
  font-size: 48px;
  font-weight: bold;
  margin-bottom: 8px;
  color: #fff;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
  font-family: 'Arial', sans-serif;
}

.data-label {
  font-size: 16px;
  color: #ccc;
  font-weight: normal;
}

.progress-wrapper {
  position: relative;
  z-index: 2;
}

.progress-circle {
  position: relative;
  width: 100px;
  height: 100px;
}

.progress-ring {
  transition: stroke-dashoffset 1s ease-in-out;
  filter: drop-shadow(0 0 5px currentColor);
}

.tick-marks {
  opacity: 0.8;
}

.progress-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
}

.progress-number {
  font-size: 24px;
  font-weight: bold;
  color: #00ffff;
  text-shadow: 0 0 10px #00ffff;
  font-family: 'Arial', sans-serif;
}

.progress-unit {
  font-size: 14px;
  color: #00ffff;
  margin-left: 2px;
  text-shadow: 0 0 5px #00ffff;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .task-card {
    flex-direction: column;
    text-align: center;
    gap: 20px;
    padding: 20px;
  }
  
  .card-content {
    justify-content: center;
    margin-right: 0;
    gap: 40px;
  }
  
  .card-icon {
    margin-right: 0;
  }
  
  .data-number {
    font-size: 36px;
  }
}
</style>