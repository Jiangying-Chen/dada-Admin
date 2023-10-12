<template>
  <div class="topPadding">
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="微信设置" name="first">
        <div class="app-container">
          <el-form
            ref="form"
            :model="form"
            :rules="rules"
            size="small"
            label-width="150px"
            label-position="left"

          >
            <el-form-item label="微信小程序AppId">
              <el-input v-model="form.WxAppId" style="width: 370px" />
            </el-form-item>
            <el-form-item label="微信小程序密钥">
              <el-input
                v-model="form.wxAppSecret"
                style="width: 370px"
                type="password"
              />
            </el-form-item>
            <el-form-item label="APP微信支付ID">
              <el-input
                v-model="form.app_appid"
                style="width: 370px"
              />
            </el-form-item>
            <el-form-item label="微信支付商户号">
              <el-input v-model="form.wxPayKey" style="width: 370px" />
            </el-form-item>
            <el-form-item label="微信支付商户秘钥">
              <el-input
                v-model="form.wxPaySecret"
                style="width: 370px"
                type="password"
              />
            </el-form-item>
            <el-form-item label="微信支付回调地址">
              <el-input
                v-model="form.appNotifyurl"
                style="width: 370px"
                placeholder="例如: https://api.xxx.com/app/pay/rolBack"
              />
              <p class="formInfo">
                支付回调地址= api接口 + /app/pay/rolBack
              </p>
            </el-form-item>
            <el-form-item label="h5支付后跳转地址">
              <el-input
                v-model="form.redirectUrl"
                style="width: 370px"
                placeholder="例如: https://www.linfeng.tech/#/pages/bill/bill?types=0"
              />
              <p class="formInfo">
                h5支付之后跳转地址= h5端访问域名 + /#/pages/bill/bill?types=0
              </p>
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>
      <el-tab-pane label="短信设置" name="second">
        <div class="app-container">
          <el-form
            ref="form"
            :model="form2"
            :rules="rules"
            size="small"
            label-width="150px"
          >
            <el-form-item label="短信开启">
              <el-radio v-model="form2.sms_open" :label="0">开启</el-radio>
              <el-radio v-model="form2.sms_open" :label="1">关闭</el-radio>
            </el-form-item>
            <el-form-item label="阿里云模板id">
              <el-input v-model="form2.sms_templateId" style="width: 300px" />
            </el-form-item>
            <el-form-item label="签名">
              <el-input v-model="form2.sms_sign" style="width: 300px" />
            </el-form-item>
            <el-form-item label="地区">
              <el-input v-model="form2.sms_region" style="width: 300px" />
            </el-form-item>
            <el-form-item label="短信key">
              <el-input v-model="form2.sms_access_key" style="width: 300px" />
            </el-form-item>
            <el-form-item label="短信密钥">
              <el-input
                v-model="form2.sms_access_secret"
                style="width: 300px"
                type="password"
              />
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form2)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>
      <el-tab-pane label="帖子设置" name="third">
        <div class="app-container">
          <el-form
            ref="form"
            :model="form"
            :rules="rules"
            size="small"
            label-width="150px"
          >
            <el-form-item label="普通贴人工审核">
              <el-radio v-model="form.normalPost" :label="0">人工审核</el-radio>
              <el-radio v-model="form.normalPost" :label="1">自动过审</el-radio>
            </el-form-item>
            <el-form-item label="付费贴人工审核">
              <el-radio v-model="form.vipPost" :label="0">人工审核</el-radio>
              <el-radio v-model="form.vipPost" :label="1">自动过审</el-radio>
            </el-form-item>

            <el-form-item label="付费贴抽成 (%)">
              <el-input
                v-model="form.postPrice"
                style="width: 200px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>
      <el-tab-pane label="前台设置" name="four">
        <div class="app-container">
          <el-form
            ref="form"
            :model="form2"
            :rules="rules"
            size="small"
            label-width="150px"
          >
            <el-form-item label="视频入口开关">
              <el-radio v-model="form2.isOpen" :label="1">开启</el-radio>
              <el-radio v-model="form2.isOpen" :label="0">关闭</el-radio>
            </el-form-item>
            <el-form-item label="充值开关">
              <el-radio v-model="form2.chargeIsOpen" :label="0">开启</el-radio>
              <el-radio v-model="form2.chargeIsOpen" :label="1">关闭</el-radio>
            </el-form-item>
            <el-form-item label="提现开关">
              <el-radio v-model="form2.canCashOut" :label="1">开启</el-radio>
              <el-radio v-model="form2.canCashOut" :label="0">关闭</el-radio>
            </el-form-item>
            <el-form-item label="积分兑换余额开关">
              <el-radio v-model="form2.exchange" :label="0">开启</el-radio>
              <el-radio v-model="form2.exchange" :label="1">关闭</el-radio>
            </el-form-item>

            <el-form-item label="兑换比例">
              <el-input
                v-model="form2.integral"
                style="width: 200px"
                type="number"
              />
              <p class="formInfo">
                兑换一块钱需要的积分数 必须为整数
              </p>
            </el-form-item>
            <el-form-item label="圈子页公告">
              <el-input v-model="form2.noticeContent" style="width: 400px" />
            </el-form-item>
            <el-form-item label="项目logo" prop="coverImage">
              <el-upload
                class="avatar-uploader"
                :action="url"
                :show-file-list="false"
                :on-success="handleIconSuccess"
              >
                <img v-if="form2.img" :src="form2.img" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon" />
              </el-upload>
              <p class="formInfo">建议尺寸：200*200像素，jpg、png图片类型</p>
            </el-form-item>
            <el-form-item label="用户个人页背景图" prop="coverImage">
              <el-upload
                class="avatar-uploader"
                :action="url"
                :show-file-list="false"
                :on-success="handleIconSuccess3"
              >
                <img v-if="form2.bgImg" :src="form2.bgImg" class="avatar2" />
                <i v-else class="el-icon-plus avatar-uploader-icon" />
              </el-upload>
              <p class="formInfo">建议尺寸：750*400像素，jpg、png图片类型</p>
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form2)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>

      <el-tab-pane label="客服设置" name="five">
        <div class="app-container">
          <el-form
            ref="form3"
            :model="form2"
            :rules="rules"
            size="small"
            label-width="150px"
          >
            <el-form-item label="客服工作时间">
              <el-input v-model="form3.contactTime" style="width: 370px" />
            </el-form-item>
            <el-form-item label="客服微信号">
              <el-input v-model="form3.contactWechat" style="width: 370px" />
            </el-form-item>
            <el-form-item label="客服电话">
              <el-input v-model="form3.contactPhone" style="width: 370px" />
            </el-form-item>
            <el-form-item label="客服微信二维码" prop="contactWechatQr">
              <el-upload
                class="avatar-uploader"
                :action="url"
                :show-file-list="false"
                :on-success="handleIconSuccess2"
              >
                <img
                  v-if="form3.contactWechatQr"
                  :src="form3.contactWechatQr"
                  class="avatar"
                />
                <i v-else class="el-icon-plus avatar-uploader-icon" />
              </el-upload>
              <p class="formInfo">建议尺寸：430*430像素，jpg、png图片类型</p>
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form3)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>

      <el-tab-pane label="抽奖设置" name="six">
        <div class="app-container">
          <el-form
            ref="form4"
            :model="form4"
            :rules="rules"
            size="small"
            label-width="150px"
          >
            <el-form-item label="抽奖是否开启">
              <el-radio v-model="form4.luckDrawStatus" :label="1"
                >开启</el-radio
              >
              <el-radio v-model="form4.luckDrawStatus" :label="0"
                >关闭</el-radio
              >
            </el-form-item>
            <el-form-item label="每次抽奖消耗积分数">
              <el-input
                v-model="form4.luckDrawIntegral"
                style="width: 250px"
                type="number"
              />
            </el-form-item>

            <el-form-item label="每天抽奖次数">
              <el-input
                v-model="form4.surplus"
                style="width: 250px"
                type="number"
              />
            </el-form-item>

            <el-form-item label="抽奖规则">
              <el-input
                v-model="form4.luckDrawRule"
                style="width: 500px"
                type="textarea"
                :autosize="{ minRows: 5, maxRows: 50}"
              />
            </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form4)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>

      <el-tab-pane label="注册设置" name="seven">
        <div class="app-container">
          <el-form
            ref="form5"
            :model="form4"
            :rules="rules"
            size="small"
            label-width="150px"
          >
          <el-form-item label="邮箱登录是否开启">
              <el-radio v-model="form5.emailLogin" :label="1"
                >开启</el-radio
              >
              <el-radio v-model="form5.emailLogin" :label="0"
                >关闭</el-radio
              >
            <p class="formInfo">开启后请在后端yml文件中配置邮箱相关参数</p>
          </el-form-item>
          <el-form-item label="个人隐私协议">
              <el-input
                v-model="form5.privacy"
                style="width: 500px"
                :autosize="{ minRows: 10, maxRows: 50}"
                type="textarea"
              />
          </el-form-item>
          <el-form-item label="用户服务协议">
              <el-input
                v-model="form5.protocol"
                style="width: 500px"
                type="textarea"
                autosize
              />
            <p class="formInfo">内容不要超过1500字符</p>
          </el-form-item>
            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form5)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>

      <el-tab-pane label="会员设置" name="eight">
        <div class="app-container">
          <el-form
            ref="form6"
            :model="form4"
            :rules="rules"
            size="small"
            label-width="160px"
            label-position="left"
          >
          <el-form-item label="会员充值协议">
              <el-input
                v-model="form6.vipAgreeContent"
                style="width: 500px"
                type="textarea"
                autosize
              />
            <p class="formInfo">内容不要超过1500字符</p>
          </el-form-item>
            <el-form-item label="会员积分奖励翻倍数">
              <el-input
                v-model="form6.vipIntegral"
                style="width: 180px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="发帖积分奖励">
              <el-input
                v-model="form6.addPostIntegral"
                style="width: 180px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="会员每月改名次数">
              <el-input
                v-model="form6.vipRename"
                style="width: 180px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="普通用户每月改名数">
              <el-input
                v-model="form6.commonRename"
                style="width: 180px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="会员可创建圈子数">
              <el-input
                v-model="form6.vipTopicNumber"
                style="width: 180px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="普通用户可创建圈子">
              <el-input
                v-model="form6.commonTopicNumber"
                style="width: 180px"
                type="number"
              />
            </el-form-item>
            <el-form-item label="会员入口开关">
              <el-radio v-model="form6.vipShow" :label="0"
                >开启</el-radio
              >
              <el-radio v-model="form6.vipShow" :label="1"
                >关闭</el-radio
              >
            </el-form-item>

            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form6)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>
      <el-tab-pane label="流量主广告" name="nine">
        <div class="app-container">
          <el-form
            ref="form7"
            :model="form7"
            :rules="rules"
            size="small"
            label-width="150px"
            label-position="left"
          >

            <el-form-item label="流量主广告是否开启">
              <el-radio v-model="form7.adIsOpen" :label="1"
                >开启</el-radio
              >
              <el-radio v-model="form7.adIsOpen" :label="0"
                >关闭</el-radio
              >
            </el-form-item>
            <el-form-item label="微信小程序adpid">
              <el-input
                v-model="form7.wxAdpid"
                style="width: 200px"
              />
            </el-form-item>
            <el-form-item label="H5的adpid">
              <el-input
                v-model="form7.h5Adpid"
                style="width: 200px"
              />
            </el-form-item>

            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form7)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>
      <el-tab-pane label="页面配置" name="ten">
        <div class="app-container">
          <el-form
            ref="form8"
            :model="form8"
            :rules="rules"
            size="small"
            label-width="150px"
            label-position="left"
          >

            <el-form-item label="首页-最新-帖子样式">
              <el-radio v-model="form8.indexStyle1" :label="0"
                >普通列表</el-radio
              >
              <el-radio v-model="form8.indexStyle1" :label="1"
                >瀑布流列表</el-radio
              >
            </el-form-item>
            <el-form-item label="首页-圈子-帖子样式">
              <el-radio v-model="form8.indexStyle2" :label="0"
                >普通列表</el-radio
              >
              <el-radio v-model="form8.indexStyle2" :label="1"
                >瀑布流列表</el-radio
              >
            </el-form-item>
            <el-form-item label="首页-关注-帖子样式">
              <el-radio v-model="form8.indexStyle3" :label="0"
                >普通列表</el-radio
              >
              <el-radio v-model="form8.indexStyle3" :label="1"
                >瀑布流列表</el-radio
              >
            </el-form-item>
            <el-form-item label="帖子详情页-布局样式">
              <el-radio v-model="form8.showType" :label="0"
                >格子布局</el-radio
              >
              <el-radio v-model="form8.showType" :label="1"
                >轮播布局</el-radio
              >
            </el-form-item>

            <el-form-item label="">
              <el-button type="primary" @click="doSubmit(form8)">提交</el-button>
            </el-form-item>
          </el-form>
        </div>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: "",
      activeName: "first",
      dataList: [],
      form: {
        WxAppId: "",
        wxAppSecret: "",
        normalPost: 0,
        vipPost: 0,
        postPrice: 0,
        wxPaySecret: "",
        wxPayKey: "",
        appNotifyurl: "",
        redirectUrl: "",
        app_appid: "",
      },
      form2: {
        sms_sign: "",
        sms_region: "",
        sms_access_key: "",
        sms_access_secret: "",
        sms_templateId: "",
        sms_open: 0,
        isOpen: 0,
        chargeIsOpen: 0,
        canCashOut:0,
        exchange: 0,
        integral: 0,
        noticeContent: "",
        img: "",
        bgImg:"",
      },
      form3: {
        contactTime: "",
        contactPhone: "",
        contactWechatQr: "",
        contactWechat: "",
      },
      form4: {
        surplus: "",
        luckDrawStatus: "",
        luckDrawRule: "",
        luckDrawIntegral: "",
      },
      form5:{
        protocol:"",
        privacy:"",
        emailLogin:''
      },
      form6:{
        vipAgreeContent:"",
        vipIntegral:0,
        addPostIntegral:0,
        vipRename:0,
        commonRename:0,
        vipTopicNumber:0,
        commonTopicNumber:0,
        vipShow:0,
      },
      form7:{
        adIsOpen:'',
        wxAdpid:'',
        h5Adpid:''
      },
      form8:{
        indexStyle1:'',
        indexStyle2:'',
        indexStyle3:'',
        showType:''
      },
      rules: {},
    };
  },
  created() {
    this.getDataList();
  },
  methods: {
    handleClick(tab, event) {
      this.activeName = tab.name;
    },
    // 获取数据列表
    getDataList() {
      this.url = this.$http.adornUrl(
        `/sys/oss/upload?token=${this.$cookie.get("token")}`
      );
      const that = this;
      this.$http({
        url: this.$http.adornUrl("/sys/config/list"),
        method: "get",
        params: this.$http.adornParams({
          page: 1,
          limit: 1000,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          data.page.list.map(function (key, value) {
            const keyName = key.paramKey;
            const newValue = key.paramValue;
            if (keyName in that.form) {
              that.form[keyName] = newValue;
            } else if (keyName in that.form2) {
              that.form2[keyName] = newValue;
            } else if (keyName in that.form3) {
              that.form3[keyName] = newValue;
            } else if (keyName in that.form4) {
              that.form4[keyName] = newValue;
            } else if (keyName in that.form5) {
              that.form5[keyName] = newValue;
            } else if (keyName in that.form6) {
              that.form6[keyName] = newValue;
            } else if (keyName in that.form7) {
              that.form7[keyName] = newValue;
            } else if (keyName in that.form8) {
              that.form8[keyName] = newValue;
            }
          });
          this.form.normalPost = parseInt(this.form.normalPost);
          this.form.vipPost = parseInt(this.form.vipPost);
          this.form.postPrice = parseInt(this.form.postPrice);
          this.form2.sms_open = parseInt(this.form2.sms_open);
          this.form2.isOpen = parseInt(this.form2.isOpen);
          this.form2.chargeIsOpen = parseInt(this.form2.chargeIsOpen);
          this.form2.canCashOut = parseInt(this.form2.canCashOut);
          this.form2.exchange = parseInt(this.form2.exchange);
          this.form2.integral = parseInt(this.form2.integral);
          this.form4.surplus = parseInt(this.form4.surplus);
          this.form4.luckDrawStatus = parseInt(this.form4.luckDrawStatus);
          this.form4.luckDrawIntegral = parseInt(this.form4.luckDrawIntegral);
          this.form5.emailLogin = parseInt(this.form5.emailLogin);
          this.form6.vipIntegral = parseInt(this.form6.vipIntegral);
          this.form6.addPostIntegral = parseInt(this.form6.addPostIntegral);
          this.form6.vipRename = parseInt(this.form6.vipRename);
          this.form6.commonRename = parseInt(this.form6.commonRename);
          this.form6.vipTopicNumber = parseInt(this.form6.vipTopicNumber);
          this.form6.commonTopicNumber = parseInt(this.form6.commonTopicNumber);
          this.form6.vipShow = parseInt(this.form6.vipShow);
          this.form7.adIsOpen = parseInt(this.form7.adIsOpen);
          this.form8.indexStyle1 = parseInt(this.form8.indexStyle1);
          this.form8.indexStyle2 = parseInt(this.form8.indexStyle2);
          this.form8.indexStyle3 = parseInt(this.form8.indexStyle3);
          this.form8.showType = parseInt(this.form8.showType);
        }
      });
    },
    doSubmit(form) {
      this.$http({
        url: this.$http.adornUrl(`/sys/config/updateBatch`),
        method: "post",
        data: form,
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "设置成功",
            type: "success",
            duration: 1500,
            onClose: () => {
              this.visible = false;
              this.$emit("refreshDataList");
            },
          });
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/sys/config/${!this.dataForm.id ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              paramKey: this.dataForm.paramKey,
              paramValue: this.dataForm.paramValue,
              remark: this.dataForm.remark,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    handleIconSuccess(response) {
      this.form2.img = response.url;
      this.$forceUpdate();
    },
    handleIconSuccess2(response) {
      this.form3.contactWechatQr = response.url;
      this.$forceUpdate();
    },
    handleIconSuccess3(response) {
      this.form2.bgImg = response.url;
      this.$forceUpdate();
    },
  },
};
</script>

<style scoped>
.app-container {
  padding: 20px 20px 45px 20px;
}
.topPadding {
  padding-left: 30px;
}

.formInfo {
  line-height: 0px;
  color: #999999;
  font-size: 12px;
}
.notice {
  line-height: 0px;
  color: #656161;
}
.avatar-uploader .el-upload {
  border: 3px dashed #979494;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
.avatar {
  width: 100px;
  height: 100px;
  display: block;
}
.avatar2 {
  width: 200px;
  height: 80px;
  display: block;
}
</style>
