<template>
  <div>
    <div class="basic-container">
      <div class="search-container">
        <el-row>
          <el-col :span="24">
            <div style="position: relative;">
              <el-input
                class="search_input"
                v-model="title"
                @focus="show = true"
                @blur="show = false"
                @keyup.enter.native="handleSearch()"
                placeholder="搜索文章标题、内容" >
                <i slot="prefix" class="el-input__icon el-icon-search"></i>
              </el-input>
              <span class="cancel_button" :style="{opacity: show ? 1 : 0, cursor: show ? 'pointer' : 'default'}" @click="handleCancel()">取消</span>
            </div>
          </el-col>
        </el-row>
      </div>
    </div>
    <div class="basic-container" v-show="hotShow && page === 1">
      <div class="article-item"
        v-for="item of archivesList"
        :key="item.id">
        <img :src="item.image" class="article-image" style="width: 470px;height: 300px;" />
        <div class="flex1 article_info">
          <div class="article_title">
            {{item.title}}
          </div>
          <div class="article_date">{{item.create_date}}</div>
          <div class="article_detail" style="height: 104px;margin-bottom: 47px;">
            {{item.description}}
          </div>
          <div class="article_btn" @click="handleGoDetail(item)">查看详情</div>
        </div>
      </div>
    </div>
    <div style="margin: 60px 0; border-bottom: 1px solid #EEEEEE;" v-show="archivesList.length > 0 && hotShow && page === 1"></div>
    <div class="basic-container">
      <div class="article-list_container">
        <div class="article-item"
          v-for="item of list"
          :key="item.id">
          <img :src="item.image" class="article-image" />
          <div class="flex1 article_info">
            <div class="article_title">
              {{item.title}}
            </div>
            <div class="article_date">{{item.create_date}}</div>
            <div class="article_detail">
              {{item.description}}
            </div>
            <div class="article_btn" @click="handleGoDetail(item)">查看详情</div>
          </div>
        </div>
      </div>
    </div>
    <div class="pages-container">
      <el-pagination
        background
        @current-change="getArticleList"
        :current-page.sync="page"
        :page-size="size"
        layout="pager"
        :total="nums">
      </el-pagination>
    </div>
  </div>
</template>

<script>
import Api from '@/utils/api'

export default {
  data () {
    return {
      archivesList: [], // 人气文章
      list: [],
      page: 1,
      nums: 0,
      page_count: 0,
      model: 1,
      title: '',
      size: 6,
      hotShow: true,
      show: false
    }
  },
  methods: {
    handleBlur () {
      console.log('7777')
      this.cancelShow = false
    },
    // 取消
    handleCancel () {
      this.hotShow = true
      this.title = ''
      this.page = 1
      this.list = []
      this.getArticleList()
    },

    // 人气单品
    getRecommendList () {
      let params = {
        model: 1 // 1: 新闻 2: 产品
      }
      Api.recommendList(params)
        .then(res => {
          let { code, msg, data } = res
          if (code === 1) {
            this.archivesList = data.archivesList
          } else {
            this.$message.error(msg)
          }
        })
    },

    handleSearch () {
      this.page = 1
      this.list = []
      this.hotShow = false
      this.getArticleList()
    },

    // 获取文章列表
    getArticleList () {
      let params = {
        page: this.page,
        model: 1,
        title: this.title,
        size: this.size
      }
      Api.list(params)
        .then(res => {
          let { code, msg, data } = res
          if (code === 1) {
            this.list = data.archivesList
            this.page_count = data.page_count // 总页数
            this.nums = data.nums // 总个数
          } else {
            this.$message.error(msg)
          }
        })
    },

    // 跳转到详情
    handleGoDetail (item) {
      this.$router.push({
        path: '/article/detail',
        query: {
          id: item.id
        }
      })
    },

    // 跳转到文章列表
    handleGoList () {
      this.$router.push({
        path: '/article/list'
      })
    }
  },
  created () {
    this.getArticleList()
    this.getRecommendList()
  }
}
</script>
