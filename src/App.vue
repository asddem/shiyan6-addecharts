<template>
  <div id="app">
    <h1>My To-Do List</h1>
    <el-card class="box-card todo-list-card">
    <to-do-form @todo-added="addToDo"></to-do-form>
    <el-divider></el-divider>
    <ul>
      <li v-for="item in ToDoItems" :key="item.id">
        <el-collapse accordion>
          <el-collapse-item title="待办详情">
        <to-do-item
          :label="item.label"
          :done="item.done"
          :id="item.id"></to-do-item>
          />
            </el-collapse-item>
          </el-collapse>
      </li>
    </ul>
  </el-card>
  <!-- 添加一个用于放置Echarts图表的DOM元素，设置宽度和高度等样式 -->
  <div id="todo-chart" style="width: 600px; height: 400px; margin: 20px auto;"></div>
  </div>
</template>

<script>
import ToDoForm from "./components/ToDoForm"
import ToDoItem from "./components/ToDoItem.vue"
import uniqueId from "lodash.uniqueid";
import * as echarts from "echarts";


export default {
  name: "app",
  components: {
    ToDoItem,
    ToDoForm,
  },
  data() {
    return {
      ToDoItems: [
        { id: uniqueId("todo-"), label: "Learn Vue", done: false },
        {
          id: uniqueId("todo-"),
          label: "Create a Vue project with the CLI",
          done: true,
        },
        { id: uniqueId("todo-"), label: "Have fun", done: true },
        { id: uniqueId("todo-"), label: "Create a to-do list", done: false },
      ],
    };
  },
  methods: {
  addToDo(toDoLabel) {
    this.ToDoItems.push({id:uniqueId('todo-'), label: toDoLabel, done: false});
  },
  initChart() {
      // 基于准备好的DOM元素，初始化Echarts实例
      const chartDom = document.getElementById('todo-chart');
      const myChart = echarts.init(chartDom);
      // 准备数据，这里假设每个待办事项都有一个剩余天数属性
      const remainingDays = this.ToDoItems.map(item => {
        const rawRemainingDays = item.remainingDays || 0; // 假设如果没有设置剩余天数则默认为0
        const fixedValues = [1, 2, 3, 4, 5];
        return fixedValues.reduce((prev, current) => {
          return Math.abs(current - rawRemainingDays) < Math.abs(prev - rawRemainingDays)? current : prev;
        }, fixedValues[0]);
      });
      const todoLabels = this.ToDoItems.map(item => item.label);

      // 配置Echarts图表的选项
      const option = {
        title: {
          text: '待办事项剩余天数统计',
          left: 'center'
        },
        xAxis: {
          type: 'category',
          data: todoLabels
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            name: '剩余天数',
            type: 'bar',
            data: remainingDays
          }
        ]
      };

      // 使用刚指定的配置项和数据显示图表
      myChart.setOption(option);
    }
  },
  mounted() {
    // 在组件挂载完成后，初始化Echarts图表
    this.initChart();
  }
};


</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #050e17;
  margin-top: 60px;
  background-color: #d5e0eb;
}

/* 为整个待办事项列表卡片设置样式，添加边框和背景颜色 */
.todo-list-card {
  border: 1px solid #4a7ee5; /* 灰色边框，你可以根据喜好调整颜色 */
  background-color: #4c8be9; /* 淡灰色背景，可按需更改 */
  border-radius: 10px; /* 设置圆角，让卡片看起来更柔和 */
}

/* 为待办事项表单组件添加样式，改变输入框等的外观 */
.todo-list-card.to-do-form {
  margin-bottom: 20px;
}

.todo-list-card.to-do-form input {
  border: 1px solid #e4e7ed;
  border-radius: 5px;
  padding: 5px 10px;
}

/* 为每个待办事项项（包裹在el-collapse内）设置样式 */
.todo-list-card.el-collapse {
  margin-bottom: 15px;
  border: 1px solid #e4e7ed;
  border-radius: 5px;
  background-color: rgb(239, 138, 138);
}

/* 为待办事项项的标题栏（el-collapse-item的标题部分）设置样式 */
.todo-list-card.el-collapse-item__header {
  background-color: #f5f7fa;
  color: #303133;
  padding: 10px;
  font-weight: bold;
}

/* 当待办事项项展开时，设置其内容区域的样式 */
.todo-list-card.el-collapse-item__wrap {
  padding: 10px;
}
</style>