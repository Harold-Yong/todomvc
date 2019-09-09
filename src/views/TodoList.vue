<template>
<div>
  <input
    type="checkbox"
    class="checkBox"
    :value="completed"
    @change="toggleChecked"
  />
  <input
    v-if="editing"
    type="text"
    class="content_todoList_main main_input"
    v-model="curValue"
    @blur="unEdit()"
  />
  <!-- v-todo-focus="list.value" -->
  <div
    v-else
    @dblclick="toEdit()"
    :class="['content_todoList_main', { deleted: completed }]"
  >
    {{ value }}
  </div>
</div>
</template>
<script>
/**
 * props: [value, completed, selected]
 * emit: change
 **/
export default {
  props: ["value", "completed"],
  data: () => ({
    editing: false,
    curValue: ''
  }),
  methods: {
    // 使添加的todo可编辑
    toEdit() {
      this.editing = true;
      this.curValue = this.value;
    },
    unEdit() {
      this.editing = false;
      this.$emit('change', { value: this.curValue, isChecked: this.completed });
    },
    toggleChecked(e) {
      const { value } = this;
      this.$emit('change', { value, isChecked: e.target.checked })
    }
  }
};
</script>

<style scoped>
/* 任务框左边复选按钮 */
.checkBox {
  width: 20px;
  height: 20px;
  margin-left: 10px;
}


/* 双击编辑列表 */
.content_todoList_main {
  display: inline-block;
  text-align: left;
  margin-left: 16px;
  font-size: 20px;
  padding: 6px 0;
}

.main_input {
  position: relative;
  z-index: 1;
}

.deleted {
  text-decoration-line: line-through;
  color: #bbb;
}
</style>