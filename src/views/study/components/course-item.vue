<template>
  <div class="box">
    <template v-if="currenStatus === 3">
      <template v-for="item in list">
        <div class="item" v-if="item.course && item.course.id" :key="item.id">
          <div class="left-item">
            <thumb-bar
              :value="item.course.thumb"
              :border="4"
              :width="160"
              :height="120"
            ></thumb-bar>
          </div>
          <div class="right-item">
            <div class="item-title">{{ item.course.title }}</div>
            <div class="item-info">
              <div class="item-text">
                已学课时：{{ item.learned_count }}课时/共{{
                  item.course.videos_count
                }}课时
              </div>
              <div class="item-text" v-if="item.created_at">
                收藏时间：{{ item.created_at | dateFormat }}
              </div>
            </div>
          </div>
          <div class="button continue" @click="goDetail(item.course_id)">
            详情
          </div>
        </div>
      </template>
    </template>
    <template v-else-if="currenStatus === 2">
      <template v-for="item in list">
        <div class="item" v-if="item.course && item.course.id" :key="item.id">
          <div class="left-item">
            <thumb-bar
              :value="item.course.thumb"
              :border="4"
              :width="160"
              :height="120"
            ></thumb-bar>
            <div class="icon">已订阅</div>
          </div>
          <div class="right-item">
            <div class="item-title">{{ item.course.title }}</div>
            <div class="item-info">
              <div class="item-text">
                已学课时：{{ item.learned_count }}课时/共{{
                  item.course.videos_count
                }}课时
              </div>
              <div class="item-progress">
                学习进度：{{ item.watch_record.progress || 0 }}%
              </div>
            </div>
          </div>
          <div class="button continue" @click="goDetail(item.course_id)">
            课程目录
          </div>
        </div>
      </template>
    </template>
    <template v-else>
      <template v-for="item in list">
        <div class="item" v-if="item.course && item.course.id" :key="item.id">
          <div class="left-item">
            <thumb-bar
              :value="item.course.thumb"
              :border="4"
              :width="160"
              :height="120"
            ></thumb-bar>
            <div class="icon" v-if="item.is_subscribe === 1">已订阅</div>
          </div>
          <div class="right-item">
            <div class="item-title">{{ item.course.title }}</div>
            <div class="item-info">
              <div class="item-text">
                已学课时：{{ item.learned_count }}课时/共{{
                  item.course.videos_count
                }}课时
              </div>
              <div class="item-progress">学习进度：{{ item.progress }}%</div>
            </div>
          </div>
          <div
            class="button completed"
            v-if="item.is_watched === 1"
            @click="goPlay(item)"
          >
            学习完成
          </div>
          <div class="button continue" v-else @click="goPlay(item)">
            继续学习
          </div>
        </div>
      </template>
    </template>
  </div>
</template>
<script>
export default {
  props: ["list", "currenStatus"],
  methods: {
    goPlay(item) {
      if (item.last_view_video.length !== 0) {
        let vid = item.last_view_video.video_id;
        this.$router.push({
          name: "coursesVideo",
          query: {
            id: vid,
          },
        });
      } else {
        this.goDetail(item.course_id);
      }
    },
    goDetail(id) {
      this.$router.push({
        name: "coursesDetail",
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
