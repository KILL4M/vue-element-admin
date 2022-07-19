<template>
  <div>
    <el-form>
      <el-row>
        <el-col :span="12">
          <el-form-item label="分配用户">
            <div class="tranferbox">
              <div class="conbox">
                <div class="titbox">
                  <el-input v-model="filterText" size="mini" placeholder="搜索成员、部门" suffix-icon="el-icon-search" />
                </div>
                <div class="wordbox">
                  <el-tree
                    ref="tree"
                    show-checkbox
                    class="filter-tree"
                    node-key="id"
                    :data="data"
                    :props="defaultProps"
                    :filter-node-method="filterNode"
                    @check="getData"
                  />
                </div>
              </div>
                &emsp;
              <i class="el-icon-arrow-right" />&emsp;
              <div class="conbox">
                <div class="titbox">
                  <h2>已选择用户（{{ keyarr.length }}）</h2>
                </div>
                <div class="wordbox">
                  <ul>
                    <li v-for="(item,index) in keyarr" :key="index">
                      <div class="inli">
                        <i class="el-icon-s-custom" />
                        <span>{{ item.label }}</span>
                        <i class="el-icon-close" @click="removeData(item)" />
                      </div>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
  </div>
</template>
<script>
export default {
  data() {
    return {
      input3: '',
      checkList: [],
      keyarr: [],
      filterText: '',
      data: [{
        id: 1,
        label: '一级 1',
        children: [{
          label: '二级 1-1',
          id: 12,
          children: [{
            id: 13,
            label: '三级 1-1-1'
          }]
        }]
      }, {
        id: 2,
        label: '一级 2',
        children: [{
          label: '二级 2-1',
          id: 21,
          children: [{
            id: 22,
            label: '三级 2-1-1'
          }]
        }, {
          id: 23,
          label: '二级 2-2',
          children: [{
            id: 24,
            label: '三级 2-2-1'
          }]
        }]
      }, {
        label: '一级 3',
        id: 3
      }],
      defaultProps: {
        children: 'children',
        label: 'label'
      }
    }
  },
  watch: {
    filterText(val) {
      this.$refs.tree.filter(val)
    }
  },
  methods: {
    // 关键词搜索
    filterNode(value, data) {
      if (!value) return true
      return data.label.indexOf(value) !== -1
    },
    getData() {
      this.keyarr = []
      this.checkList = this.$refs.tree.getCheckedNodes()
      //   console.log(this.checkList)
      if (this.checkList.length !== 0) {
        for (var i = 0; i < this.checkList.length; i++) {
          if (!this.checkList[i].children) {
            this.keyarr.push(this.checkList[i])
            //   console.log(this.checkList[i])
          }
        }
      } else {
        this.keyarr = []
      }
    },
    setCheckedNodes(arr) {
      this.$refs.tree.setCheckedNodes(arr)
    },
    removeData(data) {
      const checklist = this.keyarr
      //   console.log(this.keyarr)
      for (var i = 0; i < checklist.length; i++) {
        if (checklist[i].label === data.label) {
          checklist.splice(i, 1)
        }
      }
      //   console.log(this.keyarr)
      this.keyarr = checklist
      this.setCheckedNodes(this.keyarr)
    }
  }
}
</script>
<style lang="less" scoped>
.tranferbox{
  display: flex;
  justify-content: space-between;
  align-items: center;
  .conbox{
    height: 220px;
    border: 1px solid #e9eaed;
    border-radius: 2px;
    background: #fdfdfd;
    width: 270px;
    padding: 10px;
    .titbox{
      height: 29px;
      line-height: 29px;
      h2{
        font-size:14px
      }
    }
    .wordbox{
      font-size:12px;
      height: 155px;
      margin-top: 10px;
      overflow-y: auto;
      .el-tree-node__label {
        font-size: 12px;
      }
      ul{
        li{
          .inli{
            display: flex;
            align-items: baseline;
          }
          span{
            font-size:12px;
            display:block;
            width:70px;
            padding-left:10px
          }
          .el-icon-s-custom{
            color:#93a9d3
          }
          .el-icon-close{
            color:#808080;
            cursor: pointer;
          }
        }
      }
    }
  }
}

</style>
