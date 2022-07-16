<template>
  <div class="box">
    <template v-if="currenStatus === 1">
      <template v-for="item in list">
        <div class="item" v-if="item.book && item.book.id" :key="item.id">
          <div class="left-item">
            <thumb-bar
              :value="item.book.thumb"
              :border="4"
              :width="90"
              :height="120"
            ></thumb-bar>
            <div class="icon" v-if="item.is_subscribe === 1">已订阅</div>
          </div>
          <div class="right-item">
            <div class="item-title">{{ item.book.name }}</div>
            <div class="item-info">
              <div class="item-text" v-if="item.created_at">
                上次浏览：{{ item.created_at | changeTime }}
              </div>
            </div>
          </div>
          <div class="button continue" @click="goRead(item)">
            继续学习
          </div>
        </div>
      </template>
    </template>
    <template v-else-if="currenStatus === 2">
      <template v-for="item in list">
        <div class="item" v-if="item.book && item.book.id" :key="item.id">
          <div class="left-item">
            <thumb-bar
              :value="item.book.thumb"
              :border="4"
              :width="90"
              :height="120"
            ></thumb-bar>
            <div class="icon">已订阅</div>
          </div>
          <div class="right-item">
            <div class="item-title">{{ item.book.name }}</div>
            <div class="item-info">
              <div
                class="item-text"
                v-if="item.last_view.length === 0 && item.created_at"
              >
                订阅时间：{{ item.created_at | dateFormat }}
              </div>
              <div class="item-text" v-else>
                上次浏览：{{ item.last_view.created_at | changeTime }}
              </div>
            </div>
          </div>
          <div class="button continue" @click="goDetail(item.book_id)">
            章节目录
          </div>
        </div>
      </template>
    </template>
    <template v-else>
      <div class="item" v-for="item in list" :key="item.id">
        <div class="left-item">
          <thumb-bar
            :value="item.thumb"
            :border="4"
            :width="90"
            :height="120"
          ></thumb-bar>
          <div class="icon" v-if="currenStatus === 2">已订阅</div>
        </div>
        <div class="right-item">
          <div class="item-title">{{ item.title }}</div>
          <div class="item-info">
            <div class="item-text" v-if="item.created_at">
              收藏时间：{{ item.created_at | dateFormat }}
            </div>
          </div>
        </div>
        <div class="button continue" @click="goDetail(item.id)">
          详情
        </div>
      </div>
    </template>
  </div>
</template>
<script>
export default {
  props: ["list", "currenStatus"],
  methods: {
    goRead(item) {
      this.$router.push({
        name: "bookRead",
        query: {
          id: item.article_id,
        },
      });
    },
    goDetail(id) {
      this.$router.push({
        name: "bookDetail",
        query: {
          id: id,
          tab: this.currenStatus === 2 ? 3 : 2,
        },
      });
    },
  },
};
</script>
<style lang="less" scoped>
.box {
  width: 100%;
  height: 100%;
  background: #fff;
  .item {
    width: 100%;
    height: 120px;
    display: flex;
    flex-direction: row;
    margin-bottom: 30px;
    &:last-child {
      margin-bottom: 0px;
    }
    .left-item {
      width: 160px;
      height: 120px;
      border-radius: 4px;
      overflow: hidden;
      position: relative;
      margin-right: 30px;
      background: #f0f0f8;
      .icon {
        position: absolute;
        bottom: 0;
        right: 0;
        width: 66px;
        height: 30px;
        background: #ff4d4f;
        border-radius: 4px 0px 4px 0px;
        font-size: 14px;
        font-weight: 400;
        color: #ffffff;
        line-height: 14px;
        display: flex;
        align-items: center;
        justify-content: center;
      }
    }
    .right-item {
      flex: 1;
      height: 120px;
      margin-right: 30px;
      .item-title {
        width: 816px;
        height: auto;
        font-size: 16px;
        font-weight: 600;
        color: #333333;
        line-height: 16px;
        margin-top: 30px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
      .item-info {
        width: 816px;
        height: auto;
        display: flex;
        flex-direction: row;
        margin-top: 35px;
        .item-text {
          font-size: 14px;
          font-weight: 400;
          color: #666666;
          line-height: 14px;
          margin-right: 30px;
        }
        .item-progress {
          font-size: 14px;
          font-weight: 400;
          color: #ff4d4f;
          line-height: 14px;
          margin-right: 30px;
        }
      }
    }
    .button {
      width: 104px;
      height: 46px;
      border-radius: 4px;
      font-size: 16px;
      font-weight: 400;
      color: #3ca7fa;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-top: 37px;
      cursor: pointer;
      &:hover {
        opacity: 0.8;
      }
      &.completed {
        background: #f4fafe;
      }
      &.continue {
        border: 1px solid #3ca7fa;
      }
    }
  }
}
</style>
