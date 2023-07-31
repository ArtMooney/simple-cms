<template>
  <div>
    <Login v-if="loginFlag" @status="handleLoginStatus" />
    <Cms v-if="cmsFlag" @initLoadedFlag="handleInitLoaded" />
    <Loader v-if="loaderFlag" />
  </div>
</template>

<script>
import Login from "./components/Login.vue";
import Cms from "./components/Cms.vue";
import Loader from "./components/Loader.vue";

export default {
  name: "App",
  components: { Login, Cms, Loader },

  data() {
    return {
      loaderFlag: false,
      loginFlag: false,
      cmsFlag: false,
    };
  },

  created() {
    if (!this.getLocalStorage("simple-cms-login")) {
      this.loginFlag = true;
    } else {
      this.loaderFlag = true;
      this.cmsFlag = true;
    }
  },

  methods: {
    handleLoginStatus(status) {
      if (status === "ok") {
        this.loaderFlag = true;
        this.cmsFlag = true;
      }
    },

    handleInitLoaded(initLoadedFlag) {
      if (initLoadedFlag) {
        this.loaderFlag = false;
        this.cmsFlag = true;
      }
    },

    getLocalStorage(name) {
      const itemStr = localStorage.getItem(name);

      if (!itemStr) {
        return null;
      }

      const item = JSON.parse(itemStr);
      const now = new Date();

      if (now.getTime() > item.expiry) {
        localStorage.removeItem(name);
        return null;
      }
      return item.value;
    },

    deleteLocalStorage(name) {
      localStorage.removeItem(name);
    },
  },
};
</script>

<style>
@import "./css/normalize.css";
@import "./css/webflow.css";
@import "./css/simple-cms.css";
</style>
