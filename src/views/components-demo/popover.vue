<template>
  <div class="tab-container">
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="原来的代码效果" name="before">
        <PopoverTabBefore />
      </el-tab-pane>
      <el-tab-pane label="现在改后的代码效果" name="after">
        <PopoverTabAfter />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import PopoverTabBefore from './popover-components/popover-tab-before'
import PopoverTabAfter from './popover-components/popover-tab-after'

export default {
  name: 'PopoverDemo',
  components: { PopoverTabBefore, PopoverTabAfter },
  data() {
    return {
      activeName: 'before',
      items: [{ message: 'Foo' }, { message: 'Bar' }]
    }
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event)
    }
  }
}
</script>

<style scoped>
.tab-container {
  margin:30px;
}
.editor-content {
  margin-top: 20px;
}
.test-content {
  display: flex;
  /* height: 100px; */
}
.left {
  flex: 1;
  border: 5px solid red;
}
.right {
  flex: 1;
  border: 5px solid black;
}
/* 左右2个矩形 平分父元素 父元素设置flex 子元素设置fle:1*/
</style>

