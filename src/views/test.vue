<template>
  <div>
    <YuhouNav></YuhouNav>
    <YuhouFooter></YuhouFooter>
    <button @click="getClimaxAudio"></button>
    <YuhouBack></YuhouBack>
  </div>
</template>

<script>
import YuhouNav from "@/components/yuhouNav.vue";
import YuhouFooter from "@/components/yuhouFooter.vue";
const MiniApp = window.MiniApp;
export default {
  name: 'test',
  data () {
    return {
    };
  },
  components: {
    YuhouFooter,
    YuhouNav,
    YuhouBack,
  },
  created() {},
  mounted() {
    if (window.history && window.history.pushState) {
      // 按需使用：在页面一进来的时候，添加一个历史记录，popstate不可修改
      // pushState带有三个参数：一个状态对象，一个标题（现在被忽略了），以及一个可选的URL地址。
      history.pushState(null, null, document.URL);
      // 给 window 添加一个 popstate 事件，拦截返回键，执行 this.rtnBack() 事件
      window.addEventListener("popstate", this.rtnBack, false); //false阻止默认事件
    }
  },
  beforeDestroy() {
    window.removeEventListener("popstate", this.rtnBack, false);
  },
  methods: {
    rtnBack() {
      if (this.preTime === 0) {
        this.preTime = new Date().getTime();
        MiniApp.showToast({ title: "再按一次退出应用" });
        setTimeout(() => {
          this.preTime = 0;
          history.pushState(null, null, document.URL);
        }, 2000);
      } else if (new Date().getTime() - this.preTime < 2000) {
        MiniApp.exitMiniApp();
      }
    },

    /**
     * 返回类型（chords - 和弦，dbeats - 节拍，tonality - 调性，
     *  segments - 歌曲结构，drumbeats - 鼓点，dbeat_dbn - 卡点，vocal_pitch - 人声音高）
     *
     * 不填fields返回
     *  author_name: "歌手名",audio_name: "歌曲名",hash: "音频哈希",
     */
    async getClimaxAudio() {
      await MiniApp.getClimaxAudio({
        // fields: "segments",
        album_audio_ids: ["32072514", "108735213"],
        success: (resp) => {
          console.log(resp.data);
        },
        fail: (resp) => {
          console.log(resp.errMsg);
        },
      });
    },
  },
};
</script>

<style scoped>
button {
  width: 20vw;
  height: 10vw;
}
</style>
