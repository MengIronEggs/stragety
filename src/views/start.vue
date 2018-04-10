<template>
  <div class="bg">
      <div class="textContent">
      依据公司战略目标及管控原则，国安城市《组织手册》、《权责手册》已正式下发执行。为了更好的贯彻落实以上组织管控文件，现将开展“公司管控我了解”答题活动。活动时间为4月16日———4月20日连续五天，每人每日随机完成五道题目。每道题目答题时间为1分钟，题型为选择题，连续答对五道题即为通过，如中途答错，五道题目全部重新作答。
      </div>
      <div class="InputText">
          <div class="labelStyle" style="border-top:none;">
              <!-- <selector class="leftNamesCard"  title="公　　司" placeholder="请选择公司"  :options="companyList" v-model="company"></selector> -->
            <popup-picker class="leftNamesCard" title="公　　司　" value-text-align='left' :data="companyList"  v-model="company"  placeholder="请选择公司"></popup-picker>
          </div>
          <div class="labelStyle">
              <x-input class="leftName" title="姓　　名" name="name" disabled="disabled" v-model='name' placeholder="请输入姓名"></x-input>
          </div>
          <div class="labelStyle">
              <x-input class="leftName" title="手机号码"  name="tel" v-model="tel" disabled="disabled"  placeholder="请输入手机号码" keyboard="number" is-type="china-mobile"></x-input>
          </div>
      </div>
      <div class="position">
        <button class='button' @click="next">开　始</button>
      </div>
  </div>
</template>
<script>
import { XInput, Selector } from "vux";
export default {
  components: {
    XInput,
    Selector
  },
  data() {
    return {
      name: "",
      tel: "",
      place: "请选择公司",
      company: ["请选择公司"],
      openid: "",
      companyList: [
        [
          "请选择公司",
          "国安城市",
          "北京公司",
          "一城控股",
          "上海公司",
          "北海公司",
          "峨眉公司",
          "海南公司",
          "一城会展",
          "西藏北分",
          "国安社区",
          "首佳信拆迁",
          "国安物业（北京）",
          "国安瑞",
          "中科同德",
          "国安家",
          "国安创客",
          "重庆公司"
        ]
      ]
      // { key: "科控总部", value: "科控总部" },
      // { key: "北京公司", value: "北京公司" },
      // { key: "一城控股 ", value: "一城控股" },
      // { key: "上海公司", value: "上海公司" },
      // { key: "峨眉公司", value: "峨眉公司" },
      // { key: "北海公司", value: "北海公司" },
      // { key: "海南公司", value: "海南公司" },
      // { key: "一城会展", value: "一城会展" },
      // { key: "西藏国投", value: "西藏国投" },
      // { key: "国安社区", value: "国安社区" },
      // { key: "首佳信拆迁", value: "首佳信拆迁" },
      // { key: "国安物业", value: "国安物业" },
      // { key: "国安瑞", value: "国安瑞" },
      // { key: "中科同德", value: "中科同德" },
      // { key: "国安家", value: "国安家" },
      // { key: "国安创客", value: "国安创客" },
      // { key: "重庆公司", value: "重庆公司" }
      //公司集合,
    };
  },
  methods: {
    next() {
      // localStorage.removeItem('strategy_name');
      // localStorage.removeItem('strategy_tel');
      // localStorage.removeItem('strategy_company');
      if (
        this.company == "" ||
        this.company == undefined ||
        this.company == null ||
        this.company == "null" ||
        this.company == "undefined" ||
        this.company == "请选择公司"
      ) {
        this.msgalert("请选择公司");
        return false;
      }
      let geturl =
        "http://mt.guoanfamily.com/examiner/question?phone=" + this.tel;
      this.$http.get(geturl).then(
        response => {
          // console.log(response);
          if (response.status == 200) {
            if (response.body.isFinished == false) {
              localStorage.setItem("strategy_company", this.company);
              localStorage.setItem("strategy_name", this.name);
              localStorage.setItem("strategy_tel", this.tel);
              let data = {
                company: this.company,
                tel: this.tel,//"15733111923", //this.tel,
                name: this.name//"嘻嘻" //
              };
              this.$router.push({ path: "question", query: data });
            } else {
              this.confirm();
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
    },
    confirm() {
      let me = this;
      me.$vux.confirm.show({
        title: "提示信息",
        content: "您今天已经答过题了，再来一次？",
        // 组件除show外的属性
        onCancel() {},
        onConfirm() {
          let data = {
            company: me.company,
            tel: me.tel,
            name: me.name
          };
          // alert("xixi" + JSON.stringify(data));
          me.$router.push({ path: "/explain", query: data });
          //  me.$router.push("explain");
        }
      });
    }
  },
  mounted() {
    let company = localStorage.getItem("strategy_company");
    if (company) {
      // this.company = [company]// company;
      this.company = [];
      this.company.push(company);
    } else {
      this.company = ["请选择公司"];
    }
    let name = localStorage.getItem("strategy_name");
    if (
      name !== "" ||
      name !== undefined ||
      name !== null ||
      name !== "null" ||
      name !== "undefined"
    ) {
      this.name = name;
    } else {
      this.name = "";
    }
    let phone = localStorage.getItem("strategy_tel");
    if (
      phone !== "" ||
      phone !== undefined ||
      phone !== null ||
      phone !== "null" ||
      phone !== "undefined"
    ) {
      this.tel = phone;
    } else {
      this.tel = "";
    }
    XuntongJSBridge.call("getPersonInfo", {}, userinfo => {
      this.name = userinfo.data.name;
      this.tel = userinfo.data.userName;
      this.openid = userinfo.data.openId;
      localStorage.setItem("strategy_name", this.name);
      localStorage.setItem("strategy_tel", this.tel);
    });
  }
};
</script>

<style scoped>
/* .topName {
  width: 44%;
  height: 5rem;
  background: url("../assets/strategy.png") no-repeat top;
  background-size: 100%;
  position: fixed;
  left: 15%;
  top: 1rem;
}
.topText {
  width: 100%;
  height: 3rem;
  background: url("../assets/text.png") no-repeat top;
  background-size: 75%;
  position: fixed;
  left: 30%;
  top: 6rem;
} */
.textContent {
  width: 90%;
  position: fixed;
  top: 7rem;
  left: 5%;
  color: #ffffff;
  font-size: .6rem;
  line-height: 1rem;
  text-align: left;
  padding:10px;
}
.InputText {
  width: 76%;
  height: 5.8rem;
  position: fixed;
  /* background: #ffffff; */
  bottom: 4.5rem;
  left: 12%;
  /* border-bottom: 1px solid #e6e6e6; */
  border-radius: 0.25rem;
  border: 1px solid #bbcade;
}
.labelStyle {
  width: 90%;
  border-top: 1px solid #9fa0a0;
  font-size: 0.6rem;
  margin-left: 5%;
}
.bg {
  position: fixed;
  width: 100%;
  height: 100%;
  /* background: url("https://img.guoanfamily.com/image/ks/bj.png") no-repeat top; */
  background: url("../assets/start.png") no-repeat top;
  background-size: 100% 100%;
}
.position {
  position: absolute;
  bottom: 5%;
  width: 100%;
  height: 50px;
  text-align: center;
}
.button {
  /* background: url("../assets/startbutton.png") no-repeat center; */
  /* background-size: 100%; */
  border: none;
  width: 50%;
  height: 2rem;
  font-size: 24px;
  font-weight: 600;
  color: #ffffff;
  border-radius: 0.25rem;
  background: #0081e7;
}
</style>
<style lang="less">
.weui-dialog__hd {
  padding-top: 0.5rem;
}
.weui-dialog__btn_primary {
  color: #e40011;
}
.labelStyle {
  .leftName {
    .weui-label {
      width: 5rem;
      text-align: left;
      color: #0052c2;
      font-weight: 500;
    }
    .weui-input{
      color:#fff;
    }
    // .weui-label::before {
    //   content: "*";
    //   position: absolute;
    //   color: red;
    //   left: 5px;

    // }
  }
  .leftNamesCard {
    .weui-label {
      width: 3.5rem;
      text-align: left;
      //   background:red;
      color: #0052c2;
      font-weight: 500;
    }
    .vux-cell-value{
      color:#fff;
    }
    // .weui-label::before {
    //   content: "*";
    //   position: absolute;
    //   color: red;
    //   left: 5px;
    // }
  }
}
</style>

