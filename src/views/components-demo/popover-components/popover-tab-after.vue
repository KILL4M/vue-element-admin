<template>
  <div>
    <el-form>
      <el-row>
        <el-col :span="12">
          <el-form-item label="分配用户">
            <div class="transferbox">
              <p>
                一 flex布局
                父元素上
                display: flex;
                justify-content: space-between;
                align-items: center; 默认是stretch
                二 子元素上 flex: 1;
                三 overflow-y: auto;
                四 ul
                // 去除ul距离左边的padding
                padding-inline-start: 0;
                // 去除ul中的li前面的黑点
                list-style: none;
                五 // width: fit-content;
              </p>
              <div class="conbox">
                <div class="titbox">
                  <el-input v-model="filterText" size="small" placeholder="搜索成员、部门" suffix-icon="el-icon-search" />
                </div>
                <div class="wordbox">
                  <el-tree
                    ref="userTree"
                    show-checkbox
                    class="filter-tree"
                    node-key="id"
                    :data="userOptions"
                    :props="defaultUserProps"
                    :filter-node-method="filterNode"
                    @check="getData"
                  />
                </div>
              </div>
              <div class="centerbox">
                <i class="el-icon-arrow-right" />
              </div>
              <div class="conbox">
                <div class="titbox">
                  <h2>已选择用户（{{ keyArr.length }}）</h2>
                </div>
                <div class="wordbox">
                  <ul>
                    <li v-for="item,index in keyArr" :key="index">
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
  name: 'RolesAdd',
  data() {
    return {
      roleForm: {
        roleName: '',
        roleStatus: '',
        roleDescription: '',
        permission: '',
        menuCheckStrictly: true
      },
      menuOptions: [],
      defaultProps: {
        children: 'children',
        label: 'title'
      },
      menuExpand: false,
      menuNodeAll: false,
      filterText: '',
      // userOptions: [],
      userOptions: [{
        id: 1,
        label: '一级 1',
        children: [{
          label: '二级 1-1',
          id: 12,
          children: [{
            id: 13,
            label: '三级 1-1-1',
            children: [{
              label: '四级 1-1',
              id: 12 }]
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
      keyArr: [],
      checkList: [],
      defaultUserProps: {
        children: 'children',
        label: 'label'
      }
    }
  },
  watch: {
    filterText(val) {
      this.$refs.userTree.filter(val)
    }
  },
  mounted() {
  },
  methods: {
    getData() {
      this.keyArr = []
      this.checkList = this.$refs.userTree.getCheckedNodes()
      if (this.checkList.length !== 0) {
        for (let i = 0; i < this.checkList.length; i++) {
          if (!this.checkList[i].children) {
            this.keyArr.push(this.checkList[i])
          }
        }
      } else {
        this.keyArr = []
      }
    },
    // 搜索用户树关键词
    filterNode(value, data) {
      if (!value) return true
      return data.label.indexOf(value) !== -1
    },
    removeData(data) {
      const checkList = this.keyArr
      for (let i = 0; i < checkList.length; i++) {
        if (checkList[i].label === data.label) {
          checkList.splice(i, 1)
        }
      }
      this.keyArr = checkList
      this.$refs.userTree.setCheckedNodes(this.keyArr)
    }
  }
}
</script>

<style lang="less" scoped>
.transferbox{
  display: flex;
  justify-content: space-between;
  // align-items: center;  把这个注释掉 他的值就是默认值stretch 就会自动填充撑满高度
  // align-items: stretch;
  // 本来设置了左右两边2个div：树div和用户div高度和宽度的值 固定了不变 有竖向的滚动条 如图<img src="https://markdown360-image-1311942041.cos.ap-nanjing.myqcloud.com/fbbe1c75134117f79a22ad2bfe91ebd.png" />
  // 我想让左右两边的div的高度不限制 2边的表格高度都动态撑开 动态相等 没有竖向的滚动条
  // 不知道怎么办 先自己尝试着改
  // 1）先把高度去掉 效果很奇怪 左边撑开了 但是右边没撑开 而且右边还垂直居中了 如图 <img src="https://markdown360-image-1311942041.cos.ap-nanjing.myqcloud.com/6c68e7a311710fb8a7a1580c7aa4a8c.png" />
  // 我需要右边也撑开而且左右的高度相等
  // 2）想用js改高度 百度了一圈用computed改 还没改对
  // 搞了1.5h 都不行
  // 然后问同事 同事说这个改样式就可以实现了
  // 第一步 让所以左边树div的高度=右边用户div的高度=父元素的高度 左右两边的div的高度不限制 2边的表格高度都动态撑开 动态相等 没有竖向的滚动条
  // 去掉父元素里的flex样式align-items: center; + 去掉子元素里写死的height高度样式 height: 220px; height: 155px; + 去掉子元素里写的overflow-y: auto;（高度超过父元素时有竖向的滚动条) 就可以实现父元素中的所有div的高度动态相同的效果 也就是左边的树div和右边的用户列表div的高度动态相同的效果
  // 原理说明：
  // 要想让里面的所有子元素的高度可以动态变化 并且高度相等并且子元素的高度都等于该父元素的高度时（高度充满） 就去掉align-items: center 表示使用align-items属性的默认值：stretch 默认值的效果就是如果不限制子元素的高度 那么子元素的高度自适应并且子元素的高度=父元素的高度 所以子元素高度相等
  // 1）左边的树展开时 就会把左边的树的div高度撑开 左边的树的div高度撑开了 就把父元素的高度撑开 父元素的高度撑开了 就把右边的子元素用户div高度撑开 所以左边树div的高度=右边用户div的高度=父元素的高度
  // 2）同理 右边的用户数量太多时 就会把右边的用户div高度撑开 右边的用户div高度撑开了 就会把父元素的高度撑开 父元素的高度撑开了 就会把左边的子元素树的div高度撑开 所以左边树div的高度=右边用户地址的高度=父元素的高度
  // 第一步效果如图 <img src="https://markdown360-image-1311942041.cos.ap-nanjing.myqcloud.com/20220711202606.png" />
  // 还是很奇怪：中间空了很大一块 而且中间的那个i标签还到最上面了 没有垂直居中
//   因为左右两边的div的宽度写死了 外面又是col span=12 占了屏幕的一半 又因为父元素有justify-content: space-between;这个属性 所以中间就空了很大一块 而且中间的那个i标签失去了父元素的align-items: center属性所以就不垂直居中了 就到最上面了
  // 第二步 给子元素左右2边的div设置flex: 1; 就可以水平上充满了
  // 然后把中间的顶上去的箭头用div包起来 写和左右两边的div同级的样式 这个div要让里面的子元素：一个i标签垂直居中 就需要设置这个div的样式是display: flex;+align-items: center;+margin: 0 20px; 距离左右的div一个间距
  // 第二步效果如图  <img src="https://markdown360-image-1311942041.cos.ap-nanjing.myqcloud.com/20220711203211.png" />
  // 其实这个时候 因为左右2个div子元素有flex：1 所以其实左右两个子元素是均分的col 12 - 左边的表单label的宽度 - 中间的div的宽度; 这时左右2个子元素的写死的宽度其实已经不生效了 可以看到写的width: 270px;但是实际上是318px
  // 所以我觉得这样不好 万一水平上的树子元素太多 水平上有进度条也很不好 所以我决定让左右的子元素div也自适应 并且不要col 12 让他们自适应就行了
  // 第三步 想让左右的div子元素的宽度也是动态自适应 并且左右的div的宽度相等
  // 把左右的地址的width宽度值改成fit-content（根据内容撑开） + 去掉外面的col 只保留row就可以了
  // 子元素左右2边的div设置width: fit-content;
    // 第三步效果如图：<img src="https://markdown360-image-1311942041.cos.ap-nanjing.myqcloud.com/20220711204631.png" />
    // 感觉效果不好 太窄了 算了 还是用第二步的吧 不需要width了
  .conbox{
    flex: 1;
    // height: 220px;
    // 只去掉高度效果就很奇怪
    border: 1px solid #e9eaed;
    border-radius: 4px;
    background: #fdfdfd;
    // width: 270px; 原来的
    // width: fit-content;
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
      // height: 155px;
      // overflow-y: auto;
      margin-top: 10px;
      .el-tree-node__label {
        font-size: 12px;
      }
      ul{
        // 去除ul距离左边的padding
        padding-inline-start: 0;
        // 去除ul中的li前面的黑点
        list-style: none;
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
    .overflow{
      overflow-y: auto;
    }
  }
  .centerbox{
    display: flex;
    align-items: center;
    margin: 0 20px;
  }
}
</style>
