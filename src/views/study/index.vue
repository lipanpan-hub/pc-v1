<template>
  <div class="content">
    <div class="contanier">
      <div class="top-box">
        <div class="top-title">
          <img class="icon" src="../../assets/img/study/icon-mystudy.png" />
          我的学习
        </div>
        <div class="top-tabs">
          <div
            class="tab-item"
            :class="{ active: current === tab.value }"
            v-for="(tab, index) in tabs"
            :key="index"
            @click="setCurrent(tab)"
          >
            {{ tab.name }}
          </div>
        </div>
        <div class="top-status">
          <div class="item-radio" v-for="(radio, index) in status" :key="index">
            <input
              type="radio"
              name="radios"
              :value="radio.value"
              v-model="currenStatus"
            />
            <label @click="currenStatus = radio.value">{{ radio.name }}</label>
          </div>
        </div>
      </div>
      <div class="list-box">
        <template v-if="loading">
          <skeleton-study></skeleton-study>
        </template>
        <template v-else-if="list.length > 0">
          <course-item
            v-if="current === 'vod'"
            :list="list"
            :currenStatus="currenStatus"
          ></course-item>
          <live-item
            v-else-if="current === 'live'"
            :list="list"
            :currenStatus="currenStatus"
          ></live-item>
          <topic-item
            v-else-if="current === 'topic'"
            :list="list"
            :currenStatus="currenStatus"
          ></topic-item>
          <book-item
            v-else-if="current === 'book'"
            :list="list"
            :currenStatus="currenStatus"
          ></book-item>
        </template>
        <none type="white" v-else></none>
        <div id="page" v-if="total > 10">
          <page-box
            :key="pagination.page"
            :page="pagination.page"
            :totals="total"
            @current-change="changepage"
            :pageSize="pagination.size"
            :tab="false"
          ></page-box>
        </div>
      </div>
    </div>
    <nav-footer></nav-footer>
  </div>
</template>
<script>
import { mapState, mapMutations } from "vuex";
import NavFooter from "../../components/footer.vue";
import PageBox from "../../components/page.vue";
import None from "../../components/none.vue";
import CourseItem from "./components/course-item.vue";
import LiveItem from "./components/live-item.vue";
import TopicItem from "./components/topic-item.vue";
import BookItem from "./components/book-item.vue";
import SkeletonStudy from "../../components/skeleton/skeletonStudy.vue";
export default {
  components: {
    NavFooter,
    PageBox,
    None,
    CourseItem,
    LiveItem,
    TopicItem,
    BookItem,
    SkeletonStudy,
  },
  data() {
    return {
      loading: false,
      list: [],
      total: null,
      pagination: {
        page: 1,
        size: 10,
      },
      current: "vod",
      currenStatus: 1,
      status: [
        {
          name: "在学",
          value: 1,
        },
        {
          name: "订阅",
          value: 2,
        },
        {
          name: "收藏",
          value: 3,
        },
      ],
    };
  },
  watch: {
    currenStatus() {
      this.resetData();
      this.getData();
    },
    current() {
      this.resetData();
      this.getData();
    },
  },
  computed: {
    ...mapState(["isLogin", "user", "configFunc"]),
    tabs() {
      let types = [
        {
          name: "录播课",
          value: "vod",
        },
      ];

      if (this.configFunc["live"]) {
        types.push({
          name: "直播课",
          value: "live",
        });
      }

      if (this.configFunc["book"]) {
        types.push({
          name: "电子书",
          value: "book",
        });
      }

      if (this.configFunc["topic"]) {
        types.push({
          name: "图文",
          value: "topic",
        });
      }

      return types;
    },
  },
  activated() {
    this.getData();
  },
  methods: {
    ...mapMutations(["showLoginDialog", "changeDialogType"]),
    goLogin() {
      this.changeDialogType(1);
      this.showLoginDialog();
    },
    resetData() {
      this.list = [];
      this.pagination.size = 10;
      this.pagination.page = 1;
    },
    changepage(item) {
      this.pagination.size = item.pageSize;
      this.pagination.page = item.currentPage;
      this.getData();
    },
    getData() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      if (this.currenStatus === 2) {
        this.getUserCourses();
      } else if (this.currenStatus === 3) {
        this.getLikeCourses();
      } else if (this.currenStatus === 1) {
        this.getViewStudy();
      }
    },
    getViewStudy() {
      if (this.current === "vod") {
        this.$api.Member.Learned.Courses(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "live") {
        this.$api.Member.Learned.Live(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "topic") {
        this.$api.Member.Learned.Topic(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "book") {
        this.$api.Member.Learned.Book(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      }
    },
    getUserCourses() {
      if (this.current === "vod") {
        this.$api.Member.NewCourses(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "live") {
        this.$api.Live.User(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "topic") {
        this.$api.Topic.UserBuyTopics(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data.data;
          this.total = res.data.data.total;
        });
      } else if (this.current === "book") {
        this.$api.Member.BookCourses(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      }
    },
    getLikeCourses() {
      let params = {};
      if (this.current === "vod") {
        this.$api.Member.Collects(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "live") {
        let filter = {
          type: "live",
        };
        Object.assign(params, filter);
        Object.assign(params, this.pagination);
        this.$api.TemplateOne.LikeCourses(params).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      } else if (this.current === "topic") {
        this.$api.Topic.LikeCourses(this.pagination).then((res) => {
          this.loading = false;
          this.list = res.data.data.data;
          this.total = res.data.data.total;
        });
      } else if (this.current === "book") {
        let filter = {
          type: "book",
        };
        Object.assign(params, filter);
        Object.assign(params, this.pagination);
        this.$api.TemplateOne.LikeCourses(params).then((res) => {
          this.loading = false;
          this.list = res.data.data;
          this.total = res.data.total;
        });
      }
    },
    setCurrent(tab) {
      this.current = tab.value;
    },
  },
};
</script>
<style lang="less" scoped>
.content {
  width: 100%;
  float: left;
  height: 100%;
  background: #f4fafe;
  background-image: url("../../assets/img/study/banner.png");
  background-attachment: fixed;
  background-repeat: no-repeat;
  .contanier {
    width: 1200px;
    min-height: 500px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    box-sizing: border-box;
    padding-top: 42px;
    .top-box {
      width: 100%;
      height: 207px;
      background: #ffffff;
      border-radius: 8px;
      box-sizing: border-box;
      padding: 30px;
      .top-title {
        width: 100%;
        height: 36px;
        display: flex;
        flex-direction: row;
        align-items: center;
        font-size: 24px;
        font-weight: 600;
        color: #333333;
        line-height: 36px;
        .icon {
          width: 36px;
          height: 36px;
          margin-right: 10px;
        }
      }
      .top-tabs {
        width: 100%;
        height: auto;
        display: flex;
        flex-direction: row;
        align-items: center;
        margin-top: 24px;
        .tab-item {
          width: auto;
          height: auto;
          font-size: 18px;
          font-weight: 400;
          color: #333333;
          box-sizing: border-box;
          padding-bottom: 13px;
          border-bottom: 4px solid #fff;
          margin-right: 60px;
          cursor: pointer;
          &:hover {
            font-weight: 600;
            color: #3ca7fa;
            opacity: 0.8;
          }
          &.active {
            font-weight: 600;
            color: #3ca7fa;
            border-bottom: 4px solid #3ca7fa;
          }
        }
      }
      .top-status {
        width: 100%;
        height: auto;
        display: flex;
        flex-direction: row;
        align-items: center;
        margin-top: 30px;
        .item-radio {
          display: flex;
          height: 14px;
          flex-direction: row;
          align-items: center;
          margin-right: 40px;
          input {
            width: 14px;
            height: 14px;
            margin-right: 10px;
          }
          label {
            font-size: 14px;
            font-weight: 400;
            color: #666666;
            line-height: 14px;
            margin-right: 30px;
            cursor: pointer;
          }
        }
      }
    }
    .list-box {
      width: 100%;
      height: auto;
      background: #ffffff;
      border-radius: 8px;
      box-sizing: border-box;
      padding: 30px;
      margin-top: 30px;
      #page {
        width: 100%;
        float: left;
        height: auto;
        display: flex;
        flex-direction: row;
        justify-content: center;
        margin-top: 50px;
      }
      .item {
        width: 100%;
        height: 120px;
        display: flex;
        flex-direction: row;
        margin-bottom: 30px;
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
          &.completed {
            background: #f4fafe;
          }
          &.continue {
            border: 1px solid #3ca7fa;
            cursor: pointer;
            &:hover {
              opacity: 0.8;
            }
          }
        }
      }
    }
  }
}
</style>
