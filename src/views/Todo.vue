<template>
  <div>
    <header class="header">{{projectName}}</header>
    <div class="content">
      <!-- 输入框左边按钮 -->
      <span
        class="icon-down el-icon-arrow-down"
        v-show="todoLists.length>0"
        @click="selectAllTodos"
      ></span>
      <!-- 任务输入框 -->
      <input
        type="text"
        class="todos_add"
        placeholder="What needs to be done?"
        @keyup.enter="addTodo($event.target)"
        ref="currentInput"
      />
      <!-- 任务列表 -->
      <ul class="content_todoLists">
        <li
          v-for="(list,index) in todoLists"
          class="content_todoList"
          :key="{index}"
          @mouseover="list.isActive = true"
          @mouseleave="list.isActive=false"
          v-show="defaultShow || (whichShow?list.isChecked:!list.isChecked)"
        >
          <!-- 任务框左边复选按钮 -->
          <input type="checkbox" class="checkBox" v-model="list.isChecked" />
          <!-- 双击编辑列表 -->
          <div
            class="content_todoList_main"
            @dblclick="toEdit(list)"
            v-show="!list.isEditing"
            :class="{deleted:list.isChecked}"
          >{{list.value}}</div>
          <!-- 菜单显示 -->
          <input
            type="text"
            class="content_todoList_main main_input"
            v-model="list.value"
            v-show="list.isEditing"
            v-todo-focus="list.value"
            @blur="unEdit(list)"
          />
          <!-- 任务右边清除按钮 -->
          <span
            class="el-icon-close content_todoList_delete"
            :class="{show: list.isActive}"
            @click="deleteTodo(index)"
          ></span>
        </li>
      </ul>
      <!-- 脚步功能按钮 -->
      <div class="data" v-show="todoLists.length>0">
        <!-- 任务个数-->
        <div class="data_times" v-show="times === 0">
          <span>{{times}}</span>item left
        </div>
        <div class="data_times" v-show="times > 0">
          <span>{{`${times} `}}</span>items left
        </div>
        <!-- 三个功能 -->
        <div class="data_status">
          <a
            href="#"
            rel="external nofollow"
            :class="{active:index === dataStatusIndex}"
            v-for="(item,index) in dataStatus"
            @click="switchStatus(index)"
            :key="index"
          >{{item}}</a>
        </div>
        <!-- 全部清除 -->
        <div
          class="data_clearTodos"
          @click="clearTodos"
          v-show="times < todoLists.length"
        >
          <a href="#" rel="external nofollow">clear completed</a>
        </div>
        <div
          class="data_clearTodos"
          @click="clearTodos"
          v-show="times === todoLists.length"
        >
          <a href="#" rel="external nofollow"></a>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data: () => ({
    projectName: 'todos',
    todoLists: [],
    dataStatus: ['All', 'Active', 'Completed'],
    dataStatusIndex: 0,
    whichShow: true,
    defaultShow: true,
    selectAll: false
  }),
  computed: {
    // 使用计算属性计算待办todos的次数
    times() {
      const todoArr = this.todoLists;
      let times = 0;
      for (let i = 0; i < todoArr.length; i += 1) {
        if (todoArr[i].isChecked === false) {
          times += 1;
        }
      }
      return times;
    }
  },
  methods: {
    // 使添加的todo可编辑
    toEdit(obj) {
      obj.isEditing = true;
    },
    // 使添加的todo不可编辑
    unEdit(obj) {
      obj.isEditing = false;
    },
    addTodo(e) {
      // 添加todo
      const val = e.value;
      // 如果输入内容为空则立即返回
      if (val === '') {
        return;
      }
      // 使用concat这个api添加todo
      this.todoLists = this.todoLists.concat({
        value: val, // 输入内容
        isEditing: false, // 是否在编辑状态
        isActive: false, // 删除X图标是否激活
        isChecked: false // 是否已完成
      });
      // 按下enter添加todo之后把输入框value清零
      this.$refs.currentInput.value = '';
      // 使用localStorage以JSON格式存储数据
      window.localStorage.setItem('content', JSON.stringify(this.todoLists));
    },
    // 删除todo
    deleteTodo(index) {
      this.todoLists.splice(index, 1);
      // 以json格式存储数据
      window.localStorage.setItem('content', JSON.stringify(this.todoLists));
    },
    // 试下下方三个状态切换，略麻烦
    switchStatus(index) {
      this.dataStatusIndex = index;
      if (this.dataStatus[index] === 'Active') {
        this.defaultShow = false;
        this.whichShow = false;
      } else if (this.dataStatus[index] === 'Completed') {
        this.defaultShow = false;
        this.whichShow = true;
      } else if (this.dataStatus[index] === 'All') {
        this.defaultShow = true;
      }
    },
    // 清空已完成的todoLists
    clearTodos() {
      this.todoLists = this.todoLists.filter(todo => todo.isChecked === false);
      // 以json格式存储数据
      window.localStorage.setItem('content', JSON.stringify(this.todoLists));
    },
    // 设置所有todo为已完成
    selectAllTodos() {
      this.selectAll = !this.selectAll;
      this.todoLists = this.todoLists.map(todo => ({
        ...todo,
        isChecked: this.selectAll
      }));
    }
  },
  directives: {
    // 自定义focus指令
    todoFocus(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  },
  created() {
    const myStorage = window.localStorage.getItem('content');
    // 因为todoLists初始值是null,使用或运算符，如果为null设为空数组
    this.todoLists = JSON.parse(myStorage) || [];
  }
};
</script>
<style src="./Todo.css"></style>
