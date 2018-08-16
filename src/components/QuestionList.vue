<template>
    <div class="bg">
        <span class="times">{{times}}s</span>
        <span class="num">{{questionNum}}/5</span>
        <p style="height:8rem"></p>
        <transition name="slide-fade">
            <div class="questions" v-if="questionContent">
                <p style="height:0.2rem"></p>
                <group :title="title1">
                    <radio :options="answerList"  v-model="answer" v-show="Radios" @on-change="change"></radio>
                    <checklist :label-position="labelPosition" v-show="Multiple" required :options="answerList" v-model="checklist001" @on-change="Chackchange"></checklist>
                </group>
                <!-- <p style="font-size:.7rem;padding-left:.5rem;line-height:2rem;" v-show="Multiple">该题目为多选题</p> -->
            </div>
        </transition>
        <div v-show="Multiple" style="width:100%;height:50px;position:fixed;bottom:2rem;left:0;text-align:center;">
          <button class="onButton" @click="sunClick()"></button>
          <p style="font-size:.6rem;line-height:.6rem;color:#fff;" v-show="Multiple">该题目为多选题</p>
        </div>
    </div>
</template>
<script>
import { XButton, Radio, Group, Checklist } from "vux";
export default {
  components: {
    XButton,
    Radio,
    Group,
    Checklist
  },
  data() {
    return {
      Radios: false,
      Multiple: false,
      checklist001: [],
      MultipleArr: "",
      labelPosition: "left",
      questionContent: false,
      times: 60,
      // 页码
      questionNum: 1,
      // 标题集合
      titles: [],
      // 选项集合
      answers: [],
      title1: "", //"1、在全国消费升级的情况下，什么样的产业升级最心动？",
      answerList: "", //["A、一站式服务", "B、深耕行业", "C、坚持传统模式"],
      correctanswer: [],
      answer: "",
      myanswer: "",
      currentPage: 1,
      name: "",
      tel: "",
      company: "",
      openid: ""
      //   buttonText: "下一题"
    };
  },
  methods: {
    qolt() {
      let phone = localStorage.getItem("strategy_tel");
      // let geturl = "http://ntest.guoanfamily.com/examiner/question?phone=" + phone;
      // let geturl = "http://192.168.1.105:8280/question/0";
      let geturl = "https://mt.guoanfamily.com/examiner/question/0";
      this.$http.get(geturl).then(
        response => {
          if (response.status == 200) {
            if (response.body.bankList.length > 0) {
              for (let i = 0; i < response.body.bankList.length; i++) {
                //  问题集合
                this.titles.push(response.body.bankList[i].content);
                //  选项集合
                let arr = [];
                arr.push({
                  key: "A",
                  value: "A、" + response.body.bankList[i].A
                });
                arr.push({
                  key: "B",
                  value: "B、" + response.body.bankList[i].B
                });
                arr.push({
                  key: "C",
                  value: "C、" + response.body.bankList[i].C
                });
                if (response.body.bankList[i].D) {
                  arr.push({
                    key: "D",
                    value: "D、" + response.body.bankList[i].D
                  });
                }
                if (response.body.bankList[i].E) {
                  arr.push({
                    key: "E",
                    value: "E、" + response.body.bankList[i].E
                  });
                }
                this.answers.push(arr);
                // 答案集合
                this.correctanswer.push(response.body.bankList[i].answer);
              }
              console.log("本轮答案", this.correctanswer);
              this.title1 = this.titles[0];
              this.answerList = this.answers[0];
              if (this.correctanswer[0].length > 1) {
                this.Multiple = true;
                this.Radios = false;
              } else {
                this.Multiple = false;
                this.Radios = true;
              }
            } else {
              this.msgalert("您已经答过题了");
              history.go(-1);
            }
          } else {
            this.showalert(response.statusText);
          }
        },
        response => {
          this.showalert(response.statusText);
        }
      );
    },
    change(val, label) {
      this.myanswer = val;
      // 判断答案是否匹配
      if (this.myanswer !== this.correctanswer[this.questionNum - 1]) {
        this.msgalert("回答错误，大侠重新来过");
        this.checklist001 = [];
        this.times = 60;
        this.questionNum = 1;
        this.answer = "";
        this.myanswer = "";
        this.titles = [];
        this.answers = [];
        this.correctanswer = [];
        this.currentPage = 1;
        this.qolt();
        this.questionContent = false;
        setTimeout(() => {
          this.answer = "";
          this.questionContent = true;
        }, 1000);
      } else {
        if (this.questionNum < 5) {
          if (this.correctanswer[this.questionNum].length > 1) {
            this.Multiple = true;
            this.Radios = false;
          } else {
            this.Multiple = false;
            this.Radios = true;
          }
        }
        this.checklist001 = [];
        this.questionContent = false;
        setTimeout(() => {
          this.questionContent = true;
          this.answer = "";
        }, 1000);
        this.times = 60;
        //清空答案
        this.myanswer = "";
        //设置题目和答案
        if (this.currentPage == 5) {
          this.submit();
        } else if (this.currentPage < 5) {
          this.questionNum++;
          this.currentPage += 1;
          this.title1 = this.titles[this.currentPage - 1];
          this.answerList = this.answers[this.currentPage - 1];
        }
      }
      //   }
    },
    Chackchange(val, label) {
      this.MultipleArr = val;
    },
    sunClick() {
      // let answers = this.MultipleArr.toString().replace(/,/g, ""); //选择的答案转为字符串
      // console.log('我选的',answers);
      var b = this.correctanswer[this.questionNum - 1];
      let answers = this.MultipleArr;
      var arr = 0;
      let a = null;
      for (let i = 0; i < answers.length; i++) {
        if (b.indexOf(answers[i]) !== -1) {
          arr += 1;
        }
      }
      if (arr == b.length) {
        a = true;
      }
      if (!a) {
        this.msgalert("回答错误，大侠重新来过");
        this.checklist001 = [];
        this.times = 60;
        this.questionNum = 1;
        this.answer = "";
        this.myanswer = "";
        this.titles = [];
        this.answers = [];
        this.correctanswer = [];
        this.currentPage = 1;
        this.qolt();
        this.questionContent = false;
        setTimeout(() => {
          this.answer = "";
          this.questionContent = true;
        }, 1000);
      } else {
        // console.log(this.questionNum)
        if (this.questionNum < 5) {
          if (this.correctanswer[this.questionNum].length > 1) {
            this.Multiple = true;
            this.Radios = false;
          } else {
            this.Multiple = false;
            this.Radios = true;
          }
        }
        this.checklist001 = [];
        this.questionContent = false;
        setTimeout(() => {
          this.questionContent = true;
          this.answer = "";
        }, 1000);
        this.times = 60;
        //清空答案
        this.myanswer = "";
        //设置题目和答案
        if (this.currentPage == 5) {
          this.submit();
        } else if (this.currentPage < 5) {
          this.questionNum++;
          this.currentPage += 1;
          this.title1 = this.titles[this.currentPage - 1];
          this.answerList = this.answers[this.currentPage - 1];
        }
      }
    },
    // xuntong(){
    //   XuntongJSBridge.call("getPersonInfo", {}, userinfo => {
    //       this.name = userinfo.data.name;
    //       this.tel = userinfo.data.userName;
    //       this.openid = userinfo.data.openId;
    //   });
    // },
    // 保存信息
    submit() {
      let post_data = {
        openid: "",
        name: this.name,
        phone: this.tel,
        company: this.company.toString()
      };
      // alert(JSON.stringify(post_data));
      let posturl = "https://mt.guoanfamily.com/examiner/submit";
      // let posturl = "http://192.168.1.124:8280/submit";

      this.$http.post(posturl, post_data).then(
        response => {
          // get body data
          // console.log(response);
          if (response.status == 200) {
            // this.msgalert("保存成功");
            this.$router.push("end");
            this.$router.push({ path: "end", query: { body: response.body } });
          } else {
            this.showalert(response.body.msg);
          }
        },
        response => {
          this.showalert(response.body.msg);
        }
      );
    },
    // 计时器
    setTime() {
      let me = this;
      let setTimeouts = setInterval(function() {
        me.times--;
        if (me.times === 0) {
          clearInterval(setTimeouts);
          me.times = 60;
          // 时间走完未答题，重新调用接口
          me.questionNum = 1;
          me.answer = "";
          me.myanswer = "";
          me.titles = [];
          me.answers = [];
          me.correctanswer = [];
          me.currentPage = 1;
          me.title1 = "";
          me.answerList = "";
          me.qolt();
          me.questionContent = false;
          setTimeout(() => {
            me.questionContent = true;
            me.setTime();
          }, 1000);
        }
      }, 1000);
    },

    // 提示窗口
    showalert(msg) {
      // 显示
      this.$vux.alert.show({
        title: "提示信息",
        content: msg
      });
    },
    msgalert(msg) {
      this.$vux.toast.show({
        text: msg,
        type: "text"
      });
    }
  },
  mounted() {
    //调用后台接口，获取会议信息、权限及是否已经签到
    // this.msgalert('您已经打过题了');
    // history(-1);
    this.company = this.$route.query.company;
    this.tel = this.$route.query.tel;
    this.name = this.$route.query.name;
    // alert(this.company.toString() + this.tel + this.name);
    this.setTime();
    // this.questionOnLoad();
    this.qolt();

    setTimeout(() => {
      this.questionContent = true;
    }, 500);
  }
};
</script>
<style>
.weui-cells__title {
  font-size: 16px !important;
}
.weui-cells {
  font-size: 16px !important;
  border-radius: 10px;
}
</style>
<style scoped lang='less'>
.questions {
  width: 80%;
  height: 14rem;
  background: #ffffff;
  overflow-y:scroll ;
  // background: red;
  border-radius: 10px;
  position: fixed;
  top: 7rem;
  left: 10%;
  transition: 0.9s;
}
// .question {
//   left: 10%;
// }
// .rightquestion {
//   right: -80%;
// }
.weui-btn_warn {
  background-color: #ec0011;
  font-weight: 500;
}
.times {
  display: block;
  position: fixed;
  left: 1rem;
  top: 0.9rem;
  font-size: 18px;
  color: #333333;
}
.num {
  display: block;
  position: fixed;
  right: 1rem;
  top: 0.9rem;
  font-size: 18px;
  color: #333333;
}
.bg {
  position: fixed;
  left:0;
  top:0;
  width: 100%;
  /* height: 100%; */
  height: 100%;
  // background: url("https://img.guoanfamily.com/image/ks/bj1.png") no-repeat top;
  background: url("../assets/start.png") no-repeat top;
  background-size: 100% 100%;
  /* overflow-y: auto; */
  padding-bottom: 1.25rem;
}

.onButton {
  width: 6rem;
  height: 50px;
  background: url("../assets/queren.png") no-repeat center;
  background-size: cover;
  border-radius: 10px;
  border: none;
}
.slide-fade-enter-active {
  transition: all 0.3s ease;
}
.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter,
.slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
</style>
