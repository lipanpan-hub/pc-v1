<template>
  <div class="content">
    <div class="mask" v-if="results.openmask">
      <div class="popup borderbox" v-if="dialogStatus">
        <div class="tabs">
          <div class="item-tab">确认信息</div>
          <img
            class="btn-close"
            @click="cancel()"
            src="../../../assets/img/commen/icon-close.png"
          />
        </div>
        <div class="text">正在考试中,是否确认返回?</div>
        <div class="button">
          <div class="confirm" style="cursor: pointer" @click="confirm()">
            确认
          </div>
          <div class="cancel" style="cursor: pointer" @click="cancel()">
            取消
          </div>
        </div>
      </div>
      <div class="popup borderbox" v-if="submitTip">
        <div class="tabs">
          <div class="item-tab">确认信息</div>
          <img
            class="btn-close"
            @click="cancel()"
            src="../../../assets/img/commen/icon-close.png"
          />
        </div>
        <div class="text" v-if="surplus !== 0">
          还有{{ surplus }}道题未做，确认要交卷吗？
        </div>
        <div class="text" v-else>确认要交卷吗？</div>
        <div class="button">
          <div class="confirm" style="cursor: pointer" @click="submitHandle()">
            确定
          </div>
          <div class="cancel" style="cursor: pointer" @click="cancel()">
            继续答题
          </div>
        </div>
      </div>
      <div class="popup borderbox" v-if="readTip">
        <div class="tabs">
          <div class="item-tab">交卷提示</div>
          <img
            class="btn-close"
            @click="cancel()"
            src="../../../assets/img/commen/icon-close.png"
          />
        </div>
        <div class="text">此次在线考试包含主观题，等待老师阅卷后查看成绩</div>
        <div class="button">
          <div class="confirm" style="cursor: pointer" @click="ok()">好的</div>
        </div>
      </div>
    </div>
    <div class="navheader">
      <div class="top">
        <div class="top-left" v-if="userPaper && userPaper.status === 1">
          {{ paper.title }}
        </div>
        <div
          class="top-left"
          @click="goBack()"
          v-if="userPaper && userPaper.status === 2"
        >
          <img
            class="icon-back"
            src="../../../assets/img/commen/icon-back-h.png"
          />
          {{ paper.title }}
        </div>
        <div class="top-right">
          <div class="score" v-if="userPaper && userPaper.status === 2">
            考试得分：<strong>{{ userPaper.score }}分</strong>
          </div>
          <div class="score-info" v-if="userPaper && userPaper.status === 1">
            及格分：{{ paper.pass_score }}分/{{ paper.score }}分
          </div>
          <div
            v-if="userPaper && userPaper.status === 1"
            class="remaining-time"
          >
            倒计时：
            {{ remainingTime.hr }}时{{ remainingTime.min }}分{{
              remainingTime.sec
            }}秒
          </div>
          <div
            v-if="userPaper && userPaper.status === 1"
            class="button"
            @click="submitAll"
          >
            立即交卷
          </div>
        </div>
      </div>
    </div>
    <div class="paper-box">
      <div class="fix-left-box">
        <div
          class="number-box"
          v-show="questions && userPaper && userPaper.status === 1"
        >
          <div
            class="num"
            :class="{
              active: activeQuestions[item.question_id],
            }"
            v-for="(item, index) in questions"
            :key="index"
            @click="goDetail(index)"
          >
            {{ index + 1 }}
          </div>
        </div>
        <div
          class="number-box"
          v-show="questions && userPaper && userPaper.status === 2"
        >
          <div
            class="num"
            :class="{
              correct: item.is_correct === 1,
              error: item.is_correct === 0,
              no: item.question.type === 4 || item.question.type === 6,
            }"
            v-for="(item, index) in questions"
            :key="index"
            @click="goDetail(index)"
          >
            {{ index + 1 }}
          </div>
        </div>
      </div>
      <div class="right-box">
        <div class="questions-box" v-if="questions && userPaper">
          <template v-for="(question, index) in questions">
            <div class="item" :key="index" :id="index">
              <template v-if="userPaper.status === 2 && collects">
                <div
                  class="collect-icon"
                  @click="collectAnswer(question.question_id)"
                >
                  <template v-if="collects[question.question_id] === 1">
                    <img src="../../../assets/img/commen/icon-collect-h.png" />
                    <strong>已收藏</strong>
                  </template>
                  <template v-else>
                    <img src="../../../assets/img/commen/icon-collect-n.png" />
                    收藏试题
                  </template>
                </div>
              </template>
              <!-- 单选 -->
              <question-choice
                :num="index + 1"
                v-if="question.question.type === 1"
                :question="question.question"
                :reply="question.answer_content"
                :score="question.score"
                :is-correct="question.is_correct"
                @update="questionUpdate"
                :is-over="userPaper.status === 2"
              ></question-choice>

              <!-- 多选 -->
              <question-select
                :num="index + 1"
                v-else-if="question.question.type === 2"
                :question="question.question"
                :reply="question.answer_content"
                :score="question.score"
                :is-correct="question.is_correct"
                @update="questionUpdate"
                :is-over="userPaper.status === 2"
              ></question-select>

              <!-- 填空 -->
              <question-input
                :num="index + 1"
                v-else-if="question.question.type === 3"
                :question="question.question"
                :reply="question.answer_content"
                :score="question.score"
                :is-correct="question.is_correct"
                @update="questionUpdate"
                :is-over="userPaper.status === 2"
              ></question-input>

              <!-- 问答 -->
              <question-qa
                :num="index + 1"
                v-else-if="question.question.type === 4"
                :question="question.question"
                :reply="question.answer_content"
                :thumbs="question.thumbs_rows"
                :score="question.score"
                :is-correct="question.is_correct"
                @update="questionUpdate"
                :show-image="true"
                :is-over="userPaper.status === 2"
              ></question-qa>

              <!-- 判断 -->
              <question-judge
                :num="index + 1"
                v-else-if="question.question.type === 5"
                :question="question.question"
                :score="question.score"
                :is-correct="question.is_correct"
                :reply="parseInt(question.answer_content)"
                @update="questionUpdate"
                :is-over="userPaper.status === 2"
              ></question-judge>

              <!-- 题帽题 -->
              <question-cap
                :num="index + 1"
                v-else-if="question.question.type === 6"
                :question="question.question"
                :score="question.score"
                :show-image="true"
                :is-correct="false"
                :reply="question.answer_content"
                @update="questionUpdate"
                :is-over="userPaper.status === 2"
              ></question-cap>
            </div>
          </template>
        </div>
      </div>
    </div>
    <snap-shot
      :cid="paper.id"
      resource-type="paper"
      v-if="configFunc.snapshort && userPaper && userPaper.status === 1"
      :duration="workTime"
    ></snap-shot>
  </div>
</template>
<script>
import { mapState } from "vuex";
import QuestionChoice from "../../../components/choice.vue";
import QuestionSelect from "../../../components/select.vue";
import QuestionInput from "../../../components/input.vue";
import QuestionQa from "../../../components/qa.vue";
import QuestionJudge from "../../../components/judge.vue";
import QuestionCap from "../../../components/cap.vue";
import SnapShot from "../../../components/snapshort.vue";

export default {
  components: {
    QuestionChoice,
    QuestionSelect,
    QuestionInput,
    QuestionQa,
    QuestionJudge,
    QuestionCap,
    SnapShot,
  },
  data() {
    return {
      results: {
        openmask: false,
        surplus: null,
      },
      flag: false,
      dialogStatus: false,
      submitTip: false,
      readTip: false,
      timer: null,
      surplus: 0,
      workTime: 0,
      id: this.$route.query.id || 0,
      pid: this.$route.query.pid || 0,
      paper: [],
      questions: [],
      userPaper: null,
      remainingTime: {
        all: 0,
        day: 0,
        hr: 0,
        min: 0,
        sec: 0,
      },
      collects: null,
      activeQuestions: [],
    };
  },
  beforeDestroy() {
    this.timer && clearInterval(this.timer);
  },
  computed: {
    ...mapState(["configFunc"]),
  },
  mounted() {
    this.getData();
  },
  methods: {
    goDetail(val) {
      document.documentElement.scrollTop = document.getElementById(
        val
      ).offsetTop;
    },
    goBack() {
      if (this.userPaper.status === 1) {
        this.results.openmask = true;
        this.dialogStatus = true;
      } else {
        this.cancel();
        this.$router.back();
      }
    },
    confirm() {
      this.cancel();
      this.$router.back();
    },
    cancel() {
      this.results.openmask = false;
      this.submitTip = false;
      this.readTip = false;
      this.dialogStatus = false;
    },
    ok() {
      this.results.openmask = false;
      this.submitTip = false;
      this.readTip = false;
      setTimeout(() => {
        this.$router.back();
      }, 500);
    },
    finish() {
      if (this.userPaper.status === 1) {
        this.submitHandle();
      }
    },
    questionUpdate(qid, answer, thumbs) {
      this.$api.Exam.PaperSubmitSingle(this.paper.id, {
        pid: this.userPaper.id,
        images: thumbs,
        answer: answer,
        question_id: qid,
      });
      if (typeof qid == "string" && qid.indexOf("-") != -1) {
        this.$set(
          this.activeQuestions,
          qid.substring(0, qid.indexOf("-")),
          true
        );
      } else {
        this.$set(this.activeQuestions, qid, true);
      }
      let num = 0;
      for (let i = 0; i < this.activeQuestions.length; i++) {
        if (this.activeQuestions[i]) {
          num++;
        }
      }
      this.surplus = this.questions.length - num;
    },
    submitAll() {
      this.results.openmask = true;
      this.submitTip = true;
    },
    //倒计时
    countdown() {
      const end = parseInt(this.remainingTime.all);
      const now = Date.parse(new Date());
      const msec = end - now;
      if (msec < 0) {
        this.flag = true;
        this.workTime = parseInt(this.paper.expired_minutes) * 60;
        this.finish();
        return;
      }
      this.workTime =
        parseInt(this.paper.expired_minutes) * 60 - parseInt(msec / 1000);
      let day = parseInt(msec / 1000 / 60 / 60 / 24);
      let hr = parseInt((msec / 1000 / 60 / 60) % 24);
      let min = parseInt((msec / 1000 / 60) % 60);
      let sec = parseInt((msec / 1000) % 60);
      this.remainingTime.day = day;
      this.remainingTime.hr = hr > 9 ? hr : "0" + hr;
      this.remainingTime.min = min > 9 ? min : "0" + min;
      this.remainingTime.sec = sec > 9 ? sec : "0" + sec;
      const that = this;
      if (min >= 0 && sec >= 0) {
        //倒计时结束关闭订单
        if (min === 0 && sec === 0 && hr === 0) {
          that.flag = true;
          that.finish();
          return;
        }
      }
    },
    getData() {
      this.$api.Exam.PaperJoinRecord(this.id, this.pid)
        .then((res) => {
          this.paper = res.data.paper;
          document.title = res.data.paper.title;
          this.userPaper = res.data.user_paper;
          this.remainingTime.all = Date.parse(
            new Date(res.data.user_paper.ended_at)
          );
          let normaldata = res.data.questions;
          if (normaldata.length === 0) {
            this.$message.error("未获取到试题");
            this.$router.push({
              name: "ExamPapers",
            });
            return;
          }
          let unread = 0;
          normaldata.forEach((item) => {
            if (!item.answer_content) {
              unread++;
            }
          });
          this.questions = normaldata;
          this.questions.forEach((item) => {
            if (item.answer_content) {
              this.$set(this.activeQuestions, item.question_id, true);
            }
          });
          this.surplus = unread;
          if (this.userPaper.status === 1) {
            this.timer = setInterval(() => {
              if (this.flag) {
                this.timer && clearInterval(this.timer);
              } else {
                this.countdown();
              }
            }, 1000);
          } else if (this.userPaper.status === 2) {
            this.getCollectStatus();
          }
        })
        .catch((e) => {
          this.$message.error(e.message);
        });
    },
    submitHandle() {
      this.$api.Exam.PaperSubmit(this.paper.id, {
        pid: this.userPaper.id,
      })
        .then((res) => {
          let status = res.data.status;
          let totalScore = res.data.total_score;
          this.results.openmask = false;
          this.submitTip = false;
          if (status === 2) {
            this.$message.success("考试结束，得分：" + totalScore);
            this.getData();
          } else {
            this.results.openmask = true;
            this.readTip = true;
          }
        })
        .catch((e) => {
          this.$message.error(e.message);
        });
    },
    getCollectStatus() {
      let data = this.questions;
      let ids = [];
      for (let i = 0; i < data.length; i++) {
        ids.push(data[i].question_id);
      }
      this.$api.Exam.Practice.CollectionStatus({
        question_ids: ids,
      })
        .then((res) => {
          this.collects = res.data;
        })
        .catch((e) => {
          this.$message.error(e.message);
        });
    },
    collectAnswer(id) {
      this.$api.Exam.Practice.Collect({
        question_id: id,
      }).then(() => {
        if (this.collects[id] === 1) {
          this.$message.success("已取消收藏");
          this.$set(this.collects, id, 0);
        } else {
          this.$message.success("已收藏试题");
          this.$set(this.collects, id, 1);
        }
      });
    },
  },
};
</script>
<style lang="less" scoped>
.content {
  width: 100%;
  height: 100%;
  .navheader {
    position: sticky;
    top: 0;
    z-index: 20;
    width: 100%;
    height: 70px;
    background: #ffffff;
    .top {
      width: 1200px;
      height: 70px;
      margin: 0 auto;
      font-size: 16px;
      font-weight: 500;
      color: #333333;
      line-height: 16px;
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: space-between;
      .top-left {
        display: flex;
        flex-direction: row;
        align-items: center;
        cursor: pointer;
        .icon-back {
          width: 24px;
          height: 24px;
          margin-right: 10px;
          cursor: pointer;
        }
      }
      .top-right {
        display: flex;
        flex-direction: row;
        align-items: center;
        .score {
          height: 24px;
          font-size: 20px;
          font-weight: 600;
          color: #333333;
          line-height: 24px;
          strong {
            color: #ff4d4f;
          }
        }
        .score-info {
          height: 16px;
          font-size: 16px;
          font-weight: 400;
          color: #666666;
          line-height: 16px;
          margin-right: 30px;
        }
        .remaining-time {
          height: 16px;
          font-size: 16px;
          font-weight: 400;
          color: #666666;
          line-height: 16px;
          margin-right: 50px;
        }
        .button {
          width: 104px;
          height: 40px;
          background: #3ca7fa;
          border-radius: 4px;
          cursor: pointer;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size: 16px;
          font-weight: 400;
          color: #ffffff;
        }
      }
    }
  }
  .paper-box {
    display: flex;
    width: 1200px;
    margin: 0 auto;
    min-height: 500px;
    margin-top: 30px;
    margin-bottom: 150px;
    position: relative;
    .fix-left-box {
      width: 300px;
      display: flex;
      flex-direction: column;
      position: fixed;
      top: 100px;
      z-index: 50;
      min-height: 500px;
      background: #fff;
      border-radius: 8px;
      .number-box {
        width: 100%;
        padding: 30px;
        box-sizing: border-box;
        display: grid;
        grid-row-gap: 30px;
        grid-column-gap: 22px;
        grid-template-columns: repeat(5, minmax(0, 1fr));
        .num {
          width: 30px;
          height: 30px;
          border-radius: 50%;
          border: 1px solid #3ca7fa;
          cursor: pointer;
          display: flex;
          align-items: center;
          justify-content: center;
          color: #3ca7fa;
          font-weight: 500;
          font-size: 16px;
          &:hover {
            opacity: 0.8;
          }
          &.correct {
            border: none;
            background: #04c877;
            color: #fff;
          }
          &.error {
            border: none;
            color: #fff;
            background: #f63b46;
          }
          &.no {
            border: none;
            color: #fff;
            background: #fa8c16;
          }
          &.active {
            border: none;
            background: #3ca7fa;
            color: #fff;
          }
        }
      }
    }
    .right-box {
      margin-left: 330px;
      width: 870px;
      min-height: 500px;
      height: auto;
      float: left;
      .title-box {
        width: 870px;
        background: #ffffff;
        border-radius: 8px;
        box-sizing: border-box;
        padding: 30px;
        height: auto;
        font-size: 18px;
        font-weight: 500;
        color: #333333;
        line-height: 18px;
        margin-bottom: 20px;
      }
      .questions-box {
        width: 870px;
        height: auto;
        float: left;
        .item {
          width: 100%;
          height: auto;
          float: left;
          position: relative;
          border-radius: 8px;
          overflow: hidden;
          margin-bottom: 20px;
          .collect-icon {
            position: absolute;
            display: flex;
            width: auto;
            height: 24px;
            cursor: pointer;
            right: 30px;
            top: 30px;
            z-index: 10;
            font-size: 14px;
            font-weight: 400;
            color: #666666;
            line-height: 24px;
            strong {
              color: #ffc53d;
            }
            img {
              width: 24px;
              height: 24px;
              margin-right: 10px;
            }
          }
        }
      }
    }
  }
  .mask {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.6);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1001;
    .popup {
      width: 500px;
      height: 300px;
      background: #ffffff;
      border-radius: 8px;
      display: flex;
      flex-direction: column;
      animation: scaleBig 0.3s;
      .tabs {
        width: 100%;
        height: auto;
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
        position: relative;
        padding: 23px 23px 0px 30px;
        overflow: hidden;
        .item-tab {
          width: 72px;
          height: 18px;
          font-size: 18px;
          font-weight: 500;
          color: #333333;
          line-height: 18px;
          margin-top: 7px;
        }
        .btn-close {
          width: 19px;
          height: 19px;
          cursor: pointer;
          &:hover {
            opacity: 0.8;
            animation: rotate360 1s;
          }
        }
      }
      .text {
        width: 100%;
        height: 178px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 16px;
        font-weight: 400;
        color: #333333;
        line-height: 18px;
      }
      .button {
        width: 100%;
        height: 74px;
        background: #ffffff;
        box-shadow: 0px -1px 5px 0px rgba(0, 0, 0, 0.1);
        border-radius: 0px 0px 8px 8px;
        display: flex;
        flex-direction: row-reverse;
        align-items: center;
        .cancel {
          width: 88px;
          height: 44px;
          border-radius: 4px;
          border: 1px solid #cccccc;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size: 14px;
          font-weight: 400;
          color: #666666;
          cursor: pointer;
          margin-right: 30px;
          &:hover {
            opacity: 0.8;
          }
        }
        .confirm {
          width: 88px;
          height: 44px;
          background: #3ca7fa;
          border-radius: 4px;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size: 14px;
          font-weight: 400;
          color: #ffffff;
          cursor: pointer;
          margin-right: 30px;
          &:hover {
            opacity: 0.8;
          }
        }
      }
    }
  }
}
</style>
