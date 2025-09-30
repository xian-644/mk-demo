<template>
  <!-- <div id="app"> -->
    <TaskProgress />
    <!-- <EventList :eventList="eventList" /> -->
  <!-- </div> -->
</template>


<script setup>
import TaskProgress from './components/taskProgress/TaskProgress.vue'
import EventList from './components/eventList/index.vue'
const eventList = [
  {
    ratio: 20,
    color: '#FF6B6B',
    key: '01',
    name: 'xxx单位1',
    data: [{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位1）',
      level: '严重',
      class: 'level1'
    },{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位1）',
      level: '高危',
      class: 'level2'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位1）',
      level: '中危',
      class: 'level3'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位1）',
      level: '低危',
      class: 'level4'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位1）',
      level: '严重',
      class: 'level1'
    }],
  },
  {
    ratio: 20,
    color: '#FFA548',
    key: '02',
    name: 'xxx单位2',
    data: [{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位2）',
      level: '严重',
      class: 'level1'
    },{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位2））',
      level: '高危',
      class: 'level2'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位2））',
     
      level: '低危',
      class: 'level4'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位2））',
      level: '中危',
      class: 'level3'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位2））',
      level: '严重',
      class: 'level1'
    }],
  },
  {
    ratio: 20,
    color: '#8C67FE',
    key: '03',
    name: 'xxx单位3',
    data: [{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位3）',
      level: '严重',
      class: 'level1'
    },{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位3）',
      level: '高危',
      class: 'level2'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位3）',
      level: '低位',
      class: 'level4'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位3）',
      level: '中危',
      class: 'level3'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位3）',
      level: '严重',
      class: 'level1'
    }],
  },
  {
    ratio: 20,
    color: '#2CC46C',
    key: '04',
    name: 'xxx单位4',
    data: [{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位4）',
      level: '严重',
      class: 'level1'
    },{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位4）',
      level: '高危',
      class: 'level2'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位4）',
      level: '中危',
      class: 'level3'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位4）',
      level: '低危',
      class: 'level4'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位4）',
      level: '严重',
      class: 'level1'
    }],
  },
  {
    ratio: 20,
    color: '#47CDEE',
    key: '05',
    name: 'xxx单位5',
    data: [{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位5）',
      level: '严重',
      class: 'level1'
    },{
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位5）',
      level: '高危',
      class: 'level2'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位5）',
      level: '中危',
      class: 'level3'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位5）',
      level: '低危',
      class: 'level4'
    }, {
      time: '2021-09-01',
      type: 'SQL注入',
      origin: '123.25.6.78（上海）',
      to: '123.25.35.36（OA服务 单位5）',
      level: '严重',
      class: 'level1'
    }],
  }
]
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  width: 100%;
  min-height: 100vh;
  background: #000;
}
</style>