<template>
  <div class="content">
    <div class="nav">
      <a @click="$router.push({ name: 'Exam' })">在线考试</a> /
      <span> 考试错题本 </span>
    </div>
    <div class="banner">
      <div class="tit">考试错题本</div>
      <div class="btn-box">
        <!--<div class="day-play" @click="run('random')">随机练习</div>-->
        <div class="btn-all-play" @click="run('order')">全部练习</div>
      </div>
    </div>
    <!--<div class="statistics-box">
      <div class="tit">每日错题统计</div>
      <div class="statistics">
        <ve-histogram :data="chartData"> </ve-histogram>
      </div>
    </div>-->

    <div class="contanier">
      <div class="question-box" v-if="list">
        <div class="question-item" @click="goDetail(1)" v-if="list[1] > 0">
          <div class="question-item-type">单选题</div>
          <div class="question-item-num">共{{ list[1] }}题错题</div>
        </div>
        <div class="question-item" @click="goDetail(2)" v-if="list[2] > 0">
          <div class="question-item-type">多选题</div>
          <div class="question-item-num">共{{ list[2] }}题错题</div>
        </div>
        <div class="question-item" @click="goDetail(5)" v-if="list[5] > 0">
          <div class="question-item-type">判断题</div>
          <div class="question-item-num">共{{ list[5] }}题错题</div>
        </div>
        <div class="question-item" @click="goDetail(3)" v-if="list[3] > 0">
          <div class="question-item-type">填空题</div>
          <div class="question-item-num">共{{ list[3] }}题错题</div>
        </div>
        <div class="question-item" @click="goDetail(4)" v-if="list[4] > 0">
          <div class="question-item-type">问答题</div>
          <div class="question-item-num">共{{ list[4] }}题错题</div>
        </div>
        <div class="question-item" @click="goDetail(6)" v-if="list[6] > 0">
          <div class="question-item-type">题帽题</div>
          <div class="question-item-num">共{{ list[6] }}题错题</div>
        </div>
      </div>
    </div>

    <nav-footer></nav-footer>
  </div>
</template>
<script>
import { mapState, mapMutations } from "vuex";
import NavFooter from "../../../components/footer.vue";

export default {
  components: {
    NavFooter,
  },
  data() {
    return {
      list: null,
      chartData: {
        columns: ["日期", "新增"],
        rows: [],
      },
      total: null,
      loading: false,
    };
  },
  computed: {
    ...mapState(["isLogin", "user"]),
  },
  mounted() {
    this.getData();
  },
  methods: {
    ...mapMutations(["showLoginDialog", "changeDialogType"]),
    goLogin() {
      this.changeDialogType(1);
      this.showLoginDialog();
    },
    getData() {
      if (this.loading) {
        return;
      }
      this.loading = true;
      this.$api.Exam.WrongBook.Detail().then((res) => {
        this.loading = false;
        let results = res.data.questions;
        this.chartData.rows = res.data.data;
        this.list = res.data.types_count;
      });
    },
    goDetail(id) {
      if (!this.isLogin) {
        this.goLogin();
        return;
      }
      if (this.list[id] === 0) {
        return;
      }
      this.$router.push({
        name: "ExamWrongBookPlay",
        query: {
          type: id,
        },
      });
    },
    run(mode) {
      if (!this.isLogin) {
        this.goLogin();
        return;
      }
      if (this.wrong_question_count === 0) {
        this.$message.error("暂无错题");
        return;
      }
      this.$router.push({
        name: "ExamWrongBookPlay",
        query: {
          mode: mode,
        },
      });
    },
  },
};
</script>
<style lang="less" scoped>
.content {
  width: 100%;
  .nav {
    width: 1200px;
    margin: 0 auto;
    height: 14px;
    display: flex;
    flex-direction: row;
    align-items: center;
    font-size: 14px;
    color: #999999;
    line-height: 14px;
    margin-top: 30px;
    margin-bottom: 30px;
    a {
      height: 14px;
      font-size: 14px;
      font-weight: 400;
      color: #999999;
      line-height: 14px;
      margin-right: 6px;
      &:not(:first-of-type) {
        margin-left: 6px;
      }
    }
    span {
      height: 14px;
      margin-left: 6px;
      font-size: 14px;
      font-weight: 400;
      color: #666666;
      line-height: 14px;
    }
  }
  .banner {
    width: 1200px;
    margin: 0 auto;
    height: 198px;
    background: linear-gradient(315deg, #ff7474 0%, #ff4040 100%);
    box-shadow: 0px 4px 8px 0px rgba(102, 102, 102, 0.1);
    border-radius: 8px;
    margin-bottom: 30px;
    padding-top: 50px;
    box-sizing: border-box;
    .tit {
      width: 100%;
      height: 28px;
      font-size: 20px;
      font-family: PingFangSC-Semibold, PingFang SC;
      font-weight: 600;
      color: #ffffff;
      line-height: 28px;
      text-align: center;
      margin-bottom: 50px;
    }
    .btn-box {
      width: 100%;
      display: flex;
      flex-direction: row;
      justify-content: center;
      .day-play {
        width: 104px;
        height: 40px;
        border-radius: 4px;
        border: 1px solid #ffffff;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 16px;
        font-weight: 400;
        color: #ffffff;
        margin-right: 50px;
        &:hover {
          opacity: 0.8;
        }
      }
      .btn-all-play {
        width: 104px;
        height: 40px;
        background: #ffffff;
        border-radius: 4px;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 16px;
        font-weight: 400;
        color: #ff5068;
        &:hover {
          opacity: 0.8;
        }
      }
    }
  }
  .statistics-box {
    width: 1200px;
    margin: 0 auto;
    height: auto;
    background: #ffffff;
    border-radius: 8px;
    margin-bottom: 30px;
    padding: 30px;
    box-sizing: border-box;
    .tit {
      width: 100%;
      height: 16px;
      font-size: 16px;
      font-weight: 500;
      color: #333333;
      line-height: 16px;
      margin-bottom: 50px;
    }
    .statistics {
      width: 100%;
      height: auto;
    }
  }
  .contanier {
    width: 1200px;
    min-height: 500px;
    margin: 0 auto;
    box-sizing: border-box;
    .question-box {
      width: 100%;
      display: grid;
      gap: 30px;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      .question-item {
        width: 585px;
        height: 116px;
        background: #ffffff;
        border-radius: 8px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        box-sizing: border-box;
        padding: 30px;
        cursor: pointer;
        &:hover {
          box-shadow: 0px 4px 8px 0px #e5e5e5;
          opacity: 0.8;
        }
        .question-item-type {
          height: 16px;
          font-size: 16px;
          font-weight: 500;
          color: #333333;
          line-height: 16px;
        }
        .qquestion-item-num {
          height: 30px;
          font-size: 16px;
          font-weight: 400;
          color: #666666;
          line-height: 30px;
        }
      }
    }
  }
}
</style>
