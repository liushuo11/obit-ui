<template>
  <div class="table-tree">
    <div class="tab-row table-tree-header" v-if="widthArray.length>0">
      <div :style="{'width':widthArray[0]+'%'}" class="row-item">{{title}}</div>
      <div :key="index" :style="{'width':widthArray[index+1]+'%'}" class="row-item" v-for="(item,index) in titleArray">{{item}}</div>
    </div>
    <table-body
      :dataList="dataList"
      :id="id"
      :keys="keys"
      :keytokin="keytokin"
      :left="left"
      :pageTotal="dataList.length"
      :parentKey="parentKey"
      :pid="rootKey"
      :slotMap="$scopedSlots"
      :step="left"
      :width-array="widthArray"
      class="tabody"
      v-if="open"
    ></table-body>
  </div>
</template>

<script>
import store from './store'
import { mapGetters } from 'vuex'
import tableBody from './table-body'
export default {
  components: { tableBody },
  store,
  computed: {
    ...mapGetters(['pageChange', 'plugNum'])
  },
  watch: {
    pageChange: {
      handler: function(newVal) {
        if (newVal.keys.keytokin == this.keytokin) {
          this.$emit('pagechange', newVal.keys)
        }
      },
      deep: true
    }
  },
  props: {
    //key展示的标题
    title: {
      type: String,
      default: ''
    },
    //唯一id
    id: {
      type: String,
      default: 'id'
    },
    //根节点展示的父节点id
    rootKey: {
      type: String,
      default: ''
    },
    left: {
      type: Number,
      default: 5
    },
    page: {
      type: Number,
      default: 10
    },
    //展示的名称
    name: {
      type: String,
      default: 'name'
    },
    //父节点字段名称
    parentKey: {
      type: String,
      default: 'pid'
    },
    childrenNum: {
      type: String,
      default: 'childrenNum'
    },
    childenname: {
      type: String,
      default: 'children'
    },
    //是否有默认展开配置 需要则去查找
    expanded: {
      type: String,
      default: ''
    },
    widths: {
      type: Array,
      default: function() {
        return []
      }
    },
    closed: {
      type: String,
      default: ''
    },
    dataList: {
      type: Array,
      default: function() {
        return []
      }
    }
  },
  data() {
    return {
      widthArray: [],
      titleArray: [],
      open: false,
      keytokin: '',
      keys: []
    }
  },
  methods: {
    initConfig() {
      this.$store.commit('setPageNum', this.page)
      this.$store.commit('setLeft', this.left)
      this.$store.commit('setName', this.name)
      this.$store.commit('setChildenname', this.childenname)
      this.$store.commit('setWidths', this.widths)
      this.$store.commit('setExpanded', this.expanded)
      this.$store.commit('setClosed', this.closed)
      this.$store.commit('setChildrenNum', this.childrenNum)
      this.open = true
    }
  },
  mounted() {
    this.keytokin = 'tabletree-' + this.plugNum
    this.$store.commit('setPlugNum')
    var slots = this.$scopedSlots
    this.titleArray = Object.keys(slots)
    this.$nextTick(() => {
      if (this.widths.length == 0) {
        var num = this.titleArray.length + 1
        for (var i = 0; i < num; i++) {
          this.widthArray.push(100 / num)
        }
      } else {
        this.widthArray = this.widths
      }
      this.initConfig()
    })
  }
}
</script>

<style lang="scss">
@import './assets/iconfont.css';
.icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
  cursor: pointer;
}
.table-tree {
  table {
    width: 100%;
  }
  .tab-row {
    background: #f5f7fa;
    overflow: hidden;
    width: 100%;
    .row-item {
      float: left;
      height: 47px;
    }
  }
  .ishidden {
    display: none;
  }
  .table-tree-header {
    color: #909399;
    font-weight: 500;
    .row-item {
      white-space: nowrap;
      overflow: hidden;
      user-select: none;
      padding: 12px 0;
      min-width: 0;
      box-sizing: border-box;
      text-overflow: ellipsis;
      vertical-align: middle;
      position: relative;
      text-align: center;
      border-bottom: 1px solid #ebeef5;
      border-right: 1px solid #ebeef5;
      border-top: 1px solid #ebeef5;
    }
    th:first-child {
      border-left: 1px solid #ebeef5;
    }
  }
  .tabody {
    .el-pagination {
      text-align: center;
    }
    .row-item {
      border-bottom: 1px solid #ebeef5;
      border-right: 1px solid #ebeef5;
      padding: 12px 0;
      min-width: 0;
      box-sizing: border-box;
      text-overflow: ellipsis;
      vertical-align: middle;
      position: relative;
      text-align: center;
    }
    .tab-row {
      background: #f5f7fa;
    }
    .last-child {
      background: #ffffff;
    }
    .row-item:first-child {
      border-left: 1px solid #ebeef5;
      cursor: pointer;
      text-align: left;
    }
  }
}
</style>
