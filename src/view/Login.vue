<template>
  <div class="bgc">
    <div class="center">
      <header>
        <icon-bytedance-color class="icon-byte" />
        <span style="color: white;">Todo</span>
      </header>
      <div class="login">
        <span class="title">登录到Todo</span>
        <form class="form">
          <input
            type="text"
            name="username"
            style="display: block"
            class="username"
            v-model="form.username"
          />
          <input type="password" name="password" class="password" v-model="form.password" />
          <a-button class="submit" :loading="loading" @click="login">登录</a-button>
        </form>
        <router-link to="Register" class="rebuilt">注册新账号</router-link>
        <router-link to="Forget" class="rebuilt">忘记密码</router-link>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { IconBytedanceColor } from "@arco-design/web-vue/es/icon";
import { Message } from "@arco-design/web-vue";
import { useStore } from "vuex";
import { reactive, ref, defineComponent, toRefs, toRef } from "vue";
import { getUser } from "../axios/api";
import { useRouter } from "vue-router";
import store from "@/store";
import { useRequest } from "@/hooks/useRequest";

export default defineComponent({
  components: {
    IconBytedanceColor,
  },
  setup(props) {
    const router = useRouter();
    const form = reactive({
      username: "",
      password: "",
    });
    const { data, loading, error, run } = useRequest(getUser, {
      onError: () => {
        console.trace(error);
      },
    });
    async function login() {
      const res = await run(form);
      store.commit("setCurrentUser", res.userInfo);
      localStorage.setItem("token", `${res.token}`);
      Message.success({ content: "登陆成功！" });
      router.push("/Layout/WorkPlace");
    }
    return {
      form,
      login,
      loading,
    };
  },
});
</script>

<style scoped lang="less">
.bgc {
  // background-color: rgb(249, 250, 252);
  // background: -webkit-linear-gradient(left, #7579ff, #b224ef);
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
        animation: animate 2s linear infinite;
        @keyframes animate {
          0% {
            filter: hue-rotate(0deg);
          }
          100% {
            filter: hue-rotate(360deg);
          }
        }
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
      display: flex;
      flex-direction: column;
      align-items: center;
      border-radius: 10px;
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
        .username {
          width: 100%;
          border: 5px solid #dfe1e6;
          background-color: rgb(232, 240, 254);
          height: 50px;
          border-radius: 5px;
          margin-bottom: 40px;
          font-size: 22px;
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
          height: 60px;
          border-radius: 5px;
          text-align: center;
          color: white;
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
