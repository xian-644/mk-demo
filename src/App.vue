<template>
  <!-- <div id="app"> -->
    <TaskProgress :taskData="taskData" />
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
    name: '南充南充南充南充',
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
    name: '南充南充南充南充',
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
    name: '南充南充南充南充',
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
    name: '南充南充南充南充',
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
    name: '南充南充南充南充',
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


// 任务数据
const taskData = [
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