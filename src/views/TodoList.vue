<template>
  <div class="todoList">
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
  </div>
</template>
<script>
export default {
  data: () => ({
    todoLists: [],
    whichShow: true,
    defaultShow: true,
  }),
  methods: {
    // 使添加的todo可编辑
    toEdit(obj) {
      obj.isEditing = true;
    },
    // 使添加的todo不可编辑
    unEdit(obj) {
      obj.isEditing = false;
    },
    // 删除todo
    deleteTodo(index) {
      this.todoLists.splice(index, 1);
      // 以json格式存储数据
      window.localStorage.setItem('content', JSON.stringify(this.todoLists));
    },
    // 清空已完成的todoLists
    clearTodos() {
      this.todoLists = this.todoLists.filter(todo => todo.isChecked === false);
      // 以json格式存储数据
      window.localStorage.setItem('content', JSON.stringify(this.todoLists));
    },
  }
};
</script>
