<template >
  <div>
    <table-item
      :index="(pageIndex-1)*pageNum+index"
      :item="item"
      :key="(pageIndex-1)*pageNum+index"
      :keys="keys"
      :keytokin="keytokin"
      :left="left"
      :slotMap="slotMap"
      :step="step"
      :width-array="widthArray"
      v-for="(item,index) in dataList"
    ></table-item>
    <el-pagination
      :current-page.sync="pageIndex"
      :page-size="pageNum"
      :total="pageTotal"
      @current-change="minPageChange"
      layout="total,prev, pager, next"
      v-if="pageTotal>pageNum"
    ></el-pagination>
  </div>

  <!-- <div class="table-page" v-else> 
        <table-item  v-if="pageList.length>0 " v-for="(item,index) in pageList" :key="item.tableIndex" :index="item.tableIndex" 
            :width-array="widthArray"    :slotMap ="slotMap"
            :step="step" :left="left" :keys="keys"  :pageIndex="pageIndex"
            :item ="item"  >
        </table-item>

        <el-pagination
        :current-page.sync="pageIndex"
        :page-size="pageNum"
        @current-change="init"
        layout="total,prev, pager, next"
        :total="dataList.length">
        </el-pagination>
  </div>-->
</template>

<script>
import { Pagination } from 'element-ui'
import tableItem from './table-item.js'
import { mapGetters } from 'vuex'
import Vue from 'vue'
Vue.use(Pagination)

export default {
  name: 'tableBody',
  computed: {
    ...mapGetters(['pageNum', 'childrenNum'])
  },
  created() {
    // console.log('pageNum', this.pageTotal, this.pageNum)
  },
  props: {
    slotMap: {
      type: Object,
      default: function() {
        return {}
      }
    },
    left: {
      type: Number,
      default: 5
    },
    step: {
      required: true
    },
    id: {
      type: String,
      default: 'id'
    },
    //根节点展示的父节点id
    rootKey: {
      type: String,
      default: ''
    },
    //父节点字段名称
    parentKey: {
      type: String,
      default: 'pid'
    },
    dataList: {
      type: Array,
      default: function() {
        return []
      }
    },
    pageTotal: {
      type: Number
    },
    keytokin: {
      type: String
    },
    widthArray: {
      type: Array,
      default: function() {
        return []
      }
    },
    keys: {
      type: Array,
      default: function() {
        return []
      }
    }
  },
  components: { tableItem },
  watch: {
    pageTotal: function() {
      // console.log('page', this.keytokin, arguments)
      this.pageIndex = 1
      this.minPageChange()
    }
  },
  data() {
    return {
      pageIndex: 1,
      childList: [],
      pageList: [],
      update: true,
      start: ''
    }
  },
  methods: {
    minPageChange() {
      // console.log('setPageChange',this.keys,this.pageIndex)
      this.$store.commit('setPageChange', {
        keys: this.keys,
        pageIndex: this.pageIndex,
        keytokin: this.keytokin
      })
    },
    init(num) {
      var page = this.pageNum
      this.start = new Date().getTime()
      if (this.dataList.length > page) {
        this.$nextTick(() => {
          var index = num == null ? this.pageIndex : num
          var arr = []
          var start = (index - 1) * page
          for (var i = 0; i < page; i++) {
            if (start + i < this.dataList.length) {
              var itmes = this.dataList[start + i]
              itmes.tableIndex = i + start
              arr.push(itmes)
            }
          }
          this.$set(this, 'pageList', arr)
        })
      }
    },
    cloneObj(obj) {
      var newObj = {}
      if (obj instanceof Array) {
        newObj = []
      }
      for (var key in obj) {
        var val = obj[key]
        newObj[key] =
          val !== null && typeof val === 'object' ? this.cloneObj(val) : val
      }
      return newObj
    }
  },
  mounted() {
    // console.log('table -body init ',this.keys)
    // this.init();
  }
}
</script>

<style>
</style>
