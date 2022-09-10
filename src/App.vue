<template>
  <div id="app">
    <my-page
    @current-change="handleCurrentChange"
    @size-change="handleSizeChange"
    :current-page="currentPage"
    :page-size="pageSize"
    :page-sizes="[3, 5, 10, 15, 20]"
    :total="total">
    </my-page>
  </div>
</template>

<script>
import MyPage from '@/components/MyPage.vue'

export default {
  name: 'App',
  components: {
    MyPage
  },
  data () {
    return {
      currentPage: 1,
      pageSize: 3,
      total: 40
    }
  },
  methods: {
    // 当前页变化
    handleCurrentChange (currentPage) {
      this.currentPage = currentPage
    },
    // 每页数量变化
    handleSizeChange (pageSize) {
      this.pageSize = pageSize
    }
  }
}
</script>

<style lang="scss">

</style>
