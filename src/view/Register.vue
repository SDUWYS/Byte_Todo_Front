<template>
  <div class="bgc">
    <div class="center">
      <header>
        <icon-bytedance-color class="icon-byte" />
        <span style="color: white">Todo</span>
      </header>
      <div class="login">
        <span class="title">注册您的账户</span>
        <form class="form">
          <input
            type="text"
            name="fullName"
            placeholder="输入你的全名"
            class="fullname"
            v-model="form.fullName"
          />
          <div class="email">
            <input
              type="text"
              name="username"
              style="display: block"
              placeholder="输入你的邮箱"
              class="username"
              v-model="form.username"
            />
            <a-button
              type="outline"
              style="
                width: 7.7vw;
                margin-left: 1vw;
                height: 3vw;
                padding: 0.2vw;
              "
              @click="count"
              :loading="time.button_loading"
              :disabled="time.button_disabled"
              >{{ time.content }}</a-button
            >
          </div>
          <div class="rule">{{ rule }}</div>
          <input
            type="text"
            name="verifyCode"
            class="password"
            placeholder="输入你的邮箱验证码"
            v-model="form.verifyCode"
          />
          <input
            type="password"
            name="password"
            placeholder="输入你的密码"
            class="password"
            v-model="form.password"
          />

          <a-button
            type="primary"
            @click="register"
            :disabled="disabled"
            style="
              width: 100%;
              background-color: rgb(90, 172, 68);
              height: 3.5vw;
              border-radius: 1vw;
              color: white;
              text-align: center;
              font-size: 1.5vw;
              font-weight: 400;
              border: none;
            "
            >注册</a-button
          >
        </form>
        <router-link to="Login" class="rebuilt">已有账号？登录</router-link>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { IconBytedanceColor } from "@arco-design/web-vue/es/icon";
import { Message } from "@arco-design/web-vue";
import { useStore } from "vuex";
import { useRoute, useRouter } from "vue-router";
import { reactive, ref, defineComponent, watch } from "vue";
import router from "@/router";
import { sendEmail, registerUser } from "@/axios/api";
export default defineComponent({
  components: {
    IconBytedanceColor,
  },
  setup(props) {
    let color = reactive([
      {
        id: 1,
        color: "#0079BF",
      },
      {
        id: 2,
        color: "#D29034",
      },
      {
        id: 3,
        color: "#519839",
      },
      {
        id: 4,
        color: "#B04632",
      },
      {
        id: 5,
        color: "#755286",
      },
      {
        id: 6,
        color: "#FFFF00",
      },
      {
        id: 7,
        color: "#00FF00",
      },
      {
        id: 8,
        color: "#61bd4f",
      },
      {
        id: 9,
        color: "#f5de33",
      },
      {
        id: 10,
        color: "#ff9f1a",
      },
      {
        id: 11,
        color: "#eb5a46",
      },
      {
        id: 12,
        color: "#c377e0",
      },
      {
        id: 13,
        color: "#0079bf",
      },
      {
        id: 14,
        color: "#00c2e0",
      },
      {
        id: 15,
        color: "#51e898",
      },
      {
        id: 16,
        color: "#ff78cb",
      },
      {
        id: 17,
        color: "#344563",
      },
      {
        id: 18,
        color: "#b3bac5",
      },
    ]);
    let number = ref(0);
    const form = reactive({
      avatar: "",
      fullName: "",
      username: "",
      verifyCode: "",
      password: "",
    });
    let rule = ref("请输入邮箱");
    let disabled = ref(true);
    let time = reactive({
      content: "获取验证码",
      button_loading: false,
      button_disabled: true,
      sec: 60,
    });
    let timer: any = null;
    const getTime = () => {
      // 避免重复执行 setTimeout
      timer && clearTimeout(timer);
      timer = setTimeout(() => {
        if (time.sec > 0) {
          time.sec--;
          getTime(); // 递归调用
          time.content = "剩余" + time.sec + "秒";
          time.button_disabled = true;
        } else {
          time.content = "重新获取验证码";
          time.sec = 60;
          time.button_disabled = false;
        }
      }, 1000);
    };
    async function count() {
      getTime();
      try {
        time.button_loading = true;
        const res = await sendEmail(form.username);
        time.button_loading = false;
        Message.success("邮件发送成功！");
        console.log(res);
      } catch (error) {
        console.trace(error);
        timer && clearTimeout(timer);
        time.sec = 60;
        time.content = "获取验证码";
        time.button_disabled = false;
        time.button_loading = false;
      }
    }
    watch(
      () => [form.fullName, form.username, form.verifyCode, form.password],
      () => {
        const regEmail =
          /^([A-Za-z0-9_\-\.\u4e00-\u9fa5])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,8})$/;
        if (regEmail.test(form.username)) {
          rule.value = "邮箱格式正确";
          time.button_disabled = false;
        } else {
          rule.value = "请输入正确的邮箱地址！";
          time.button_disabled = true;
        }
        if (
          form.fullName &&
          regEmail.test(form.username) &&
          form.verifyCode &&
          form.password
        ) {
          disabled.value = false;
        } else {
          disabled.value = true;
        }
      }
    );
    async function register() {
      try {
        number.value = Math.floor(Math.random() * 19);
        form.avatar = color[number.value].color.slice(1, 7);
        const res = await registerUser(form);
        Message.success({ content: "注册成功！" });
        router.push("/Layout/WorkPlace");
        localStorage.setItem("token", `${res}`);
      } catch (error) {
        Message.error({ content: `${error}` });
      }
    }
    return {
      form,
      register,
      rule,
      disabled,
      time,
      count,
    };
  },
});
</script>

<style scoped lang="less">
.bgc {
  background: linear-gradient(
    -30deg,
    #03a9f4 0%,
    #3a78b7 50%,
    #262626 30%,
    #607d8b 100%
  );
  animation: animate 10s linear infinite;
  display: flex;
  justify-content: center;
  @keyframes animate {
    0% {
      filter: hue-rotate(0deg);
    }
    100% {
      filter: hue-rotate(360deg);
    }
  }
  .center {
    padding: 40px;
    height: calc(100vh - 80px);
    width: 600px;
    header {
      display: flex;
      justify-content: center;
      align-items: center;
      margin-bottom: 30px;
      .icon-byte {
        height: 60px;
        width: 60px;
        margin-right: 20px;
      }
      span {
        font-weight: 900;
        font-size: 50px;
        font-family: Alibaba-PuHuiTi-Light;
        color: rgb(37, 56, 88);
      }
    }
    .login {
      width: 100%;
      height: 700px;
      background-color: white;
      box-shadow: 0 8px 16px 10px rgb(9 30 66 / 25%);
      border-radius: 10px;
      display: flex;
      flex-direction: column;
      align-items: center;
      span.title {
        display: inline-block;
        color: rgb(94, 108, 132);
        margin-top: 50px;
        font-size: 24px;
        font-weight: 900;
      }
      .form {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-top: 50px;
        width: 450px;
        input:focus {
          outline: none;
          border: 5px solid rgb(76, 154, 255);
        }
        .fullname {
          width: 100%;
          border: 5px solid #dfe1e6;
          background-color: rgb(232, 240, 254);
          height: 50px;
          border-radius: 5px;
          margin-bottom: 40px;
          font-size: 22px;
        }
        .email {
          display: flex;
          align-items: center;
          .username {
            width: 290px;
            border: 5px solid #dfe1e6;
            background-color: rgb(232, 240, 254);
            height: 50px;
            border-radius: 5px;
            // margin-bottom: 40px;
            font-size: 22px;
          }
          span {
            width: 150px;
            line-height: 50px;
            background-color: rgb(232, 240, 254);
            text-align: center;
            border-radius: 10px;
            margin-left: 10px;
            cursor: pointer;
          }
          span:hover {
            background-color: rgb(213, 224, 243);
          }
        }
        .rule {
          line-height: 40px;
          align-self: flex-start;
        }
        .password {
          width: 100%;
          border: 5px solid #dfe1e6;
          background-color: rgb(232, 240, 254);
          height: 50px;
          border-radius: 5px;
          font-size: 22px;
          margin-bottom: 30px;
        }
        .submit {
          width: 100%;
          background-color: rgb(90, 172, 68);
          height: 50px;
          border-radius: 5px;
          color: white;
          text-align: center;
          font-size: 24px;
          font-weight: 800;
          border: none;
          cursor: pointer;
        }
      }
      .rebuilt {
        margin-top: 30px;
      }
    }
  }
}
</style>
