<template>
  <nav>
    <ul class="pagination">
      <li
        v-if="totalPages > 1"
        :class="{'disabled': current == 1}"
        @click="setPrev(current - 1)"
      >
        <
      </li>
      <li 
        :class="{'active': current == item, 'isMore': item == '...'}"
        v-for="(item, index) of groupPages"
        :key="index"
        @click="setCurrent(item)"
      >
        {{item}}
      </li>
      <li
        v-if="totalPages > 1"
        :class="{'disabled': current == totalPages}"
        @click="setNext(current + 1)"
      >
        >
      </li>
    </ul>
    <div class="go_wrapper">
      <input type="text" class="go_page" v-model="goPage">
      <span class="go_btn" @click="handleGoBtnClick">Go</span>
    </div>
  </nav>
</template>

<script>
export default {
  name: 'Pagination',
  data () {
    return {
      current: 1,
      textArr: [],
      goPage: ''
    }
  },
  props: {
    totalPages: {
      type: Number,
      required: true,
      validator (num) {
        return /^[1-9]\d*$/.test(num) // 必须为正整数
      }
    }
  },
  mounted () {
    this.initTextArr()
  },
  watch: {
    totalPages(newVal, oldVal) {
      this.initTextArr()
    }
  },
  computed: {
    groupPages: { // 显示的页码文本
      get () {
        return this.textArr
      },
      set (newVal) {
        this.textArr = newVal
      }
    }
  },
  methods: {
    /**
     * 初始化页码文本
     */
    initTextArr () {
      let totalPages = this.totalPages
      if (totalPages > 7) {
        this.textArr = [1, 2, 3, 4, 5, '...', totalPages]
      } else {
        this.textArr = []
        for (let i = 1; i <= totalPages; i++) {
          this.textArr.push(i)
        }
      }
    },

    /**
     * 上一页
     */
    setPrev (val) {
      if (this.current != 1) {
        this.current = val
        this.$emit('setNewPageNum', val)
        this.changeGroupPages(val)
      }
    },

    /**
     * 选中该页
     */
    setCurrent (val) {
      if (val != '...') {
        this.current = val
        this.$emit('setNewPageNum', val)
        this.changeGroupPages(val)
      }
    },

    /**
     * 下一页
     */
    setNext (val) {
      if (this.current != this.totalPages) {
        this.current = val
        this.$emit('setNewPageNum', val)
        this.changeGroupPages(val)
      }
    },

    /**
     * 直接跳转
     */
    handleGoBtnClick () {
      let goPage = this.goPage
      goPage = parseInt(goPage)
      if (goPage <= this.totalPages && goPage >= 1) {
        this.current = goPage
        this.$emit('setNewPageNum', goPage)
        this.changeGroupPages(goPage)
      }
      this.goPage = ''
    },

    /**
     * 修改显示的页码文本
     */
    changeGroupPages (val) {
      let groupPages = this.groupPages
      let total = this.totalPages
      if (total < 8) {
        return
      }
      if (val - 3 <= 1) {
        this.groupPages = [1, 2, 3, 4, 5, '...', total]
      }
      if (val + 3 >= total) {
        this.groupPages = [1, '...', (total - 4), (total - 3), (total - 2), (total - 1), total]
      }
      if (val -3 > 1 && val + 3 < total) {
        this.groupPages = [1, '...', (val - 1), val, (val + 1), '...', total]
      }
    }
  }
}
</script>

<style lang="less" scoped>
.pagination {
  margin: 0;
  padding: 0;
  list-style: none;
  font-size: 0;
  display: inline-block;
  li {
    display: inline-block;
    width: 20px;
    height: 20px;
    line-height: 19px;
    background: #f1f2f4;
    color: #333333;
    font-size: 12px;
    text-align: center;
    margin-right: 4px;
    border-radius: 2px;
    cursor: pointer;
    &:last-child {
      margin-right: 0;
    }
    &.isMore {
      background: #fff;
      cursor: auto;
    }
    &.disabled {
      background: #f1f2f4;
      color: #d8d9dd;
      cursor: auto;
    }
    &.active {
      background: #4880f8;
      color: #ffffff;
      cursor: auto;
    }
  }
}
.go_wrapper {
  display: inline-block;
  font-size: 0;
  vertical-align: bottom;
  .go_page {
    width: 25px;
    height: 20px;
    border: 1px solid #d8d9dd;
    font-size: 12px;
    text-align: center;
    outline: none;
    padding: 0;
  }
  .go_btn {
    display: inline-block;
    width: 20px;
    height: 20px;
    line-height: 18px;
    background: #f1f2f4;
    font-size: 12px;
    color: #333333;
    border: 1px solid #d8d9dd;
    text-align: center;
    padding: 0;
    cursor: pointer;
    margin-left: -1px;
    vertical-align: bottom;
  }
}
</style>