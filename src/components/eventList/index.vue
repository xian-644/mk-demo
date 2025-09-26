<template>
<div class="event-list-wrapper">
  <div class="event-list-content">
    <div class="title">
      <img src="./imgs/title.png" alt="" srcset="">
    </div>

    <div class="event-list">
      <div class="left">
        <div class="sector"></div>
        <ul class="img-list">
          <li v-for="el in imgList" :key="el.key" :class="`img${el.key}`">
            <img :src="el.img" alt="" srcset="" class="ratio-img">
              <span class="key">{{ el.key }}</span>
              <span class="ratio">占比：{{ el.ratio }}%</span>
            </img>
          </li>
        </ul>
      </div>
      <div class="right">
        <ul class="name-list">
          <li v-for="el in eventList" :style="`color: ${ el.color }`">{{ el.name }}</li>
        </ul>
        <ul class="data-list">
          <li v-for="el in eventList" :class="el.data.class">
            <span class="time">{{ el.data.time }}</span>
            <span class="origin_to">{{ el.data.origin }} - - - -▶  {{ el.data.to }}</span>
            <span class="type">{{ el.data.type }}</span>
            <span class="level">{{ el.data.level }}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</div>  
</template>

<script setup>
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

const eventList = [
  {
    ratio: 20,
    color: '#FF6B6B',
    key: '01',
    name: 'xxx单位1',
    data: {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务）',
      level: '严重',
      class: 'level1'
    }
  },
  {
    ratio: 20,
    color: '#FFA548',
    key: '02',
    name: 'xxx单位2',
    data: {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务）',
      level: '高危',
      class: 'level2'
    }
  },
  {
    ratio: 20,
    color: '#8C67FE',
    key: '03',
    name: 'xxx单位3',
    data: {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务）',
      level: '严重',
      class: 'level1'
    }
  },
  {
    ratio: 20,
    color: '#2CC46C',
    key: '04',
    name: 'xxx单位4',
    data: {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务）',
      level: '高危',
      class: 'level2'
    }
  },
  {
    ratio: 20,
    color: '#47CDEE',
    key: '05',
    name: 'xxx单位5',
    data: {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务）',
      level: '严重',
      class: 'level1'
    }
  }
]


// 画扇形 
</script>

<style>


.event-list-wrapper {
  width: 100Vw;
  height: 100vh;
  background: #030A18;
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
  width: 160px;
  height: 160px;
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
  background: radial-gradient(circle, transparent 40%, currentColor 40%);
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
  width: 260px;
}

.data-list li .type{
  display: inline-block;
  width: 80px;
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

.img-list{
  list-style: none;
}
.img-list li{
  position: absolute;
  transform-origin: 50% 100%;
}



.img-list .img01{
  transform: translate(80px , -10px );
}

.img-list .img02{
  transform: translate(90px , 56px );
}

.img-list .img03{
  transform: translate(36px , 90px );
}

.img-list .img04{
  transform: translate(-8px , 58px );
}

.img-list .img05{
  transform: translate(-2px , -8px );
}
</style>