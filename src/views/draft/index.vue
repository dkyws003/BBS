<template>
  <div class="page-container">
    <div class="left" />
    <div class="main">
      <div class="head">
        <p class="name">我的草稿</p>
        <a class="give-up-all" @click="handleClick('give-up-all')">舍弃全部草稿</a>
      </div>
      <ul class="draft-list">
        <li v-for="(item, index) in draftList" :key="index" class="item">
          <div class="info">
            <el-tag
              :type="item.dataType === 2 ? 'success' : 'info'"
              size="small"
              class="tag"
            >
              {{ getTag(item.dataType) }}
            </el-tag>
            <router-link :to="`/write?type=write&id=${item.id}`" class="title">{{ item.title }}</router-link>
          </div>
          <div class="more">
            <div class="more-left">
              <span class="save-time">保存于{{ $fn.timeView(item.create_time) }}</span>
              ·
              <router-link :to="`/write?type=write&id=${item.id}`" class="update">编辑</router-link>
            </div>
            <a class="give-up" @click="handleClick('give-up', item)">舍弃</a>
          </div>
        </li>
      </ul>
    </div>
    <div class="right">
      <Notices />
    </div>
  </div>
</template>

<script>
import Notices from '../components/Notices'
import { getDraftApi, giveUpDraftApi, giveUpAllDraftApi } from '@/api/draft'
export default {
  components: {
    Notices
  },
  data () {
    return {
      draftList: []
    }
  },
  computed: {
    getTag () {
      return (dataType) => {
        switch (dataType) {
          case 1:
            return '问题'
          case 2:
            return '文章'
        }
      }
    }
  },
  mounted () {
    this.getDraft()
  },
  methods: {
    getDraft () {
      getDraftApi().then(res => {
        if (res.success) {
          this.draftList = res.content
        } else {
          this.$message({
            showClose: true,
            message: res.message,
            type: res.success ? 'success' : 'error',
            duration: 3500
          })
        }
      })
    },
    handleClick (type, data) {
      switch (type) {
        case 'give-up':
          this.$confirm(`你要舍弃草稿「${data.title}」么??`, '舍弃草稿', {
            confirmButtonText: '舍弃',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            giveUpDraftApi({ dataType: data.dataType, id: data.id }).then(res => {
              if (res.success) {
                this.getDraft()
              }
              this.$message({
                showClose: true,
                message: res.message,
                type: res.success ? 'success' : 'error',
                duration: 3500
              })
            })
          }).catch(e => {
          })
          break
        case 'give-up-all':
          this.$confirm(`你确定要舍弃全部草稿么？`, '舍弃全部草稿', {
            confirmButtonText: '舍弃',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            giveUpAllDraftApi().then(res => {
              if (res.success) {
                this.getDraft()
              }
              this.$message({
                showClose: true,
                message: res.message,
                type: res.success ? 'success' : 'error',
                duration: 3500
              })
            })
          }).catch(e => {
          })
          break
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  @import '@/common/style/base.scss';
  .page-container{
    display: flex;
    .left{
      width: 16.66667%;
      padding: 0 15px;
    }
    .main{
      flex: 1;
      padding: 0 15px;
      .head{
        margin-bottom: 10px;
        height: 40px;
        border-bottom: 1px solid rgb(220, 220, 220);
        .name{
          display: inline-block;
          font-size: 24px;
        }
        .give-up-all{
          margin-left: 10px;
          cursor: pointer;
          padding: 3px 10px;
          color: #333;
          background: #fff;
          border: 1px solid #ccc;
          border-radius: 5px;
          box-shadow: 0 1px 1px rgba(0,0,0,0.05);
          transition: all .2s linear;
          &:hover, &:focus{
            background: #e6e6e6;
            border-radius: #adadad;
          }
          &:active{
            outline: 0;
          }
        }
      }
      .draft-list{
        .item{
          margin: 0;
          border-bottom: 1px solid #eee;
          padding: 12px 0;
          &:last-child{
            border: none;
          }
          .info{
            display: flex;
            align-items: center;
            margin-bottom: 7px;
            .tag, .title{
              display: inline-block;
            }
            .title{
              padding-left: 5px;
              font-size: 15px;
              color: $theme;
            }
          }
          .more{
            display: flex;
            align-items: center;
            .more-left{
              flex: 1;
              display: flex;
            }
          }
          .title, .update, .give-up{
            cursor: pointer;
            &:hover{
              color: #DC143C;
              text-decoration: underline;
            }
          }
        }
      }
    }
    .right{
      padding: 0 15px;
      width: 25%;
    }
  }
</style>
