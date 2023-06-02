<script setup lang="ts">
import { computed, watchEffect,reactive } from 'vue'
import { useWsLoginStore, LoginStatus } from '@/stores/ws'
import QrCode from 'qrcode.vue'
import wsIns from "@/utils/websocket";
import {WsRequestMsgType} from "@/utils/wsType";
import {CloseBold, Select} from "@element-plus/icons-vue";
import {ElMessage} from "element-plus";

const loginStore = useWsLoginStore()
const visible = computed({
  get() {
    return loginStore.showLogin
  },
  set(value) {
    loginStore.showLogin = value
  },
})

const loginQrCode = computed(() => loginStore.loginQrCode)
const loginStatus = computed(() => loginStore.loginStatus)



watchEffect(() => {
  // 打开窗口了 而且 二维码没获取，而且非登录就去获取二维码
  if (visible.value && !loginQrCode.value) {
    // 获取登录二维码
    // loginStore.getLoginQrCode()
    touristLoginHandlerClose();
  }
})

const touristLoginHandler = () => {
  touristInfo.isEdit=true;
}
const touristLoginHandlerClose = () => {
  touristInfo.isEdit=false;
}
const giteeLoginHandler = ()=>{
  wsIns.send({ type: WsRequestMsgType.GITEE_LOGIN})
}

const touristLogin = () => {
  if(touristInfo.userName==null || touristInfo.userName.trim()==""){
    ElMessage.warning('用户名不能为空哦~');
    return;
  }
  wsIns.send({ type: WsRequestMsgType.TOURIST_LOGIN,data:{"userName":touristInfo.userName} })
}
const wxLoginHandler = () =>{
  if (visible.value && !loginQrCode.value) {
    // 获取登录二维码
    loginStore.getLoginQrCode()
  }
}

const touristInfo = reactive({
  isEdit: false,
  userName: '',
  saving: false,
})
</script>

<template>
  <ElDialog class="login_box_modal" :width="376" v-model="visible" center>
    <div>

    </div>
    <div class="login_box">
      <img class="login_logo" src="@/assets/logo.png" alt="MallChat" />
      <p class="login_slogan">边聊边买，岂不快哉~</p>
<!--      <div class="login_qrcode_wrapper" v-loading="!loginQrCode">-->
<!--        <QrCode class="login_qrcode" v-if="loginQrCode" :value="loginQrCode" :size="328" :margin="5" />-->
<!--      </div>-->



      <p class="login_desc" v-if="loginStatus === LoginStatus.Waiting">
        <ElIcon :size="32" class="login_desc_icon" color="#67c23a"><IEpSuccessFilled /></ElIcon>
        扫码成功~，点击“登录”继续登录
      </p>
      <p class="login_desc">
<!--        使用「<strong class="login_desc_bold">微信</strong>」扫描二维码登录~~-->
        <a class="login-link" @click="touristLoginHandler"><strong class="ts_login_desc_bold">游客</strong>登录</a>
        &nbsp;&nbsp;&nbsp; | &nbsp;&nbsp;&nbsp;
        <a class="login-link" @click="wxLoginHandler"><strong class="login_desc_bold">微信</strong>登录</a>

        &nbsp;&nbsp;&nbsp; | &nbsp;&nbsp;&nbsp;
        <a class="login-link" @click="giteeLoginHandler"><strong class="login_desc_bold">Gitee</strong>登录</a>
      </p>

      <div class="name_edit_wrapper" v-show="touristInfo.isEdit">
        <ElInput type="text" v-model="touristInfo.userName" placeholder="请输入你的名称" maxlength="6" />
        <div style="margin-top: 5px;align-content: center">
          <el-button class="name_edit_icon" size="small" type="primary" @click="touristLogin">
            登录
          </el-button>
          <el-button
              class="name_edit_icon"
              size="small"
              type="danger"
              @click="touristLoginHandlerClose"
          >取消</el-button>
        </div>


      </div>


    </div>
  </ElDialog>
</template>

<style lang="scss" src="./styles.scss" scoped />
