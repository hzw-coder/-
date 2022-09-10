<template>
    <div class="page" v-if="maxPage>1">
        <span>共{{total}}条</span>
        <select @change="handleChange" v-model="selectValue" style="margin: 0 10px;">
            <option
            v-for="(item, index) in pageSizes"
            :value="item"
            :key="index"
            :label="item + '条/页'">
        </option>
        </select>
        <el-button @click="preHandle" size="small" :disabled="currentPage<=1">上一页</el-button>
        <span
        :class="['page-block',currentPage==item?'active':'',typeof item==='string'&&index===1?'elliLeft':'',typeof item==='string'&&index>1?'elliRight':'']"
        v-for="(item,index) in pageList"
        @click="gotoPage(item,index)"
        :key="index">
            {{item}}
        </span>
        <el-button style="margin-right: 10px;" :disabled="currentPage==maxPage" @click="nextHandle" size="small">下一页</el-button>
        前往<input @keyup.enter="handleKeyUp" v-model="goToPageValue" style="width: 30px;margin: 0 3px;" type="number">页
    </div>
</template>

<script>
export default {
    name: 'myPagination',
    data () {
        return {
            selectValue: '',
            goToPageValue: 1
        }
    },
    mounted () {
        this.selectValue = this.pageSizes[0]
    },
    props: {
        currentPage: {
            type: Number,
            dafault: 1
        },
        total: {
            type: Number,
            default: 0
        },
        pageSize: {
            type: Number,
            default: 5
        },
        pageSizes: {
            type: Array,
            default: () => []
        }
    },
    watch: {
        maxPage (newVal, oldVal) {
            if (this.currentPage > newVal) {
                // 切换每页数量时，如果当前页大于切换后的最大页码，则置为第一页
                this.$emit('current-change', 1)
                this.goToPageValue = 1
            }
        }
    },
    computed: {
        // 最大页码(总页数)
        maxPage () {
            return Math.ceil(this.total / this.pageSize)
        },
        // 存放页码的数组
        pageList () {
            if (this.maxPage <= 9) { // 总页数<=9时,无省略号
                return this.maxPage
            } else {
                // 有省略号
                if (this.currentPage <= 5) {
                    return [1, 2, 3, 4, 5, 6, '...', this.maxPage]
                } else if (this.currentPage >= this.maxPage - 5) {
                    return [1, '...', this.maxPage - 5, this.maxPage - 4, this.maxPage - 3, this.maxPage - 2, this.maxPage - 1, this.maxPage]
                } else {
                    return [1, '...', this.currentPage - 2, this.currentPage - 1, this.currentPage, this.currentPage + 1, this.currentPage + 2, '...', this.maxPage]
                }
            }
        }
    },
    methods: {
        // 下一页
        nextHandle () {
            if (this.currentPage < this.maxPage) {
                const curPage = this.currentPage + 1
                this.$emit('current-change', curPage)
                this.goToPageValue = curPage
            }
        },
        // 上一页
        preHandle () {
            if (this.currentPage > 0 && this.currentPage <= this.maxPage) {
                const curPage = this.currentPage - 1
                this.$emit('current-change', curPage)
                this.goToPageValue = curPage
            }
        },
        // 前往哪一页
        gotoPage (curPage, i) {
            if (typeof curPage === 'number') {
               this.$emit('current-change', curPage)
               this.goToPageValue = curPage
            } else {
                if (i === 1) { // 点击左边省略号
                    this.preHandle()
                } else { // 点击右边
                    this.nextHandle()
                }
            }
        },
        // pageSize改变
        handleChange () {
            this.$emit('size-change', this.selectValue)
        },
        // 回车事件
        handleKeyUp () {
            if (this.goToPageValue < 0) {
                this.$emit('current-change', 1)
                this.goToPageValue = 1
            } else if (this.goToPageValue > this.maxPage) {
                this.$emit('current-change', this.maxPage)
                this.goToPageValue = this.maxPage
            } else {
                this.$emit('current-change', Math.floor(this.goToPageValue))
                this.goToPageValue = Math.floor(this.goToPageValue)
            }
        }
    }
}
</script>

<style lang="scss" scoped>
.page{
    margin-top: 10px;
    margin-right: 10px;
    float: right;
    .page-block{
        display: inline-block;
        width: 68px;
        height: 32px;
        border: 1px solid #ddd;
        position: relative;
        text-align: center;
        line-height: 32px;
        background-color: #ddd;
        margin: 0 10px;
        cursor: pointer;
    }
    // 修改省略号
    .elliLeft:hover::before{
        content: '<<';
        display: block;
        width: 68px;
        height: 32px;
        color: #409EFF;
        background-color: #ddd;
        position: absolute;
        left: 0;
        right: 0;
        z-index: 99;
    }
    .elliRight:hover::before{
        content: '>>';
        display: block;
        width: 68px;
        height: 32px;
        color: #409EFF;
        background-color: #ddd;
        position: absolute;
        left: 0;
        right: 0;
        z-index: 99;
    }
    .active{
        color: #fff;
        background-color: #409EFF;
    }
}
// 去除上下箭头
input[type=number] {
    -moz-appearance:textfield;
}
input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

</style>
