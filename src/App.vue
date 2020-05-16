<template>
  <div id="app">
    <b-card header="Infinite Scroll" class="infinite-scroll-card text-center">
      <p class="card-text">載入次數：{{ infiniteScrollCount }}</p>
    </b-card>
    <div class="wrapper">
      <h1>Github Repos with Infinite Scroll</h1>
      <h6>Author: 洪士峰 Desmond&emsp;Date: 2020/05/16</h6>
      <ul class="repos" ref="repoContainer">
        <li class="repo-item" v-for="(repo, index) in repos" :key="index">
          <div class="display-3">{{ repo.name }}</div>
          <p>{{ repo.description }}</p>
          <div class="bottom-row">
            <div class="info-row">
              <b-badge variant="info">{{ repo.language }}</b-badge>
              <b-badge>Created: {{ repo.created_at }}</b-badge>
              <b-badge>Updated: {{ repo.updated_at }}</b-badge>
            </div>
            <b-button :href="repo.html_url" target="_blank" variant="outline-info">Github Link</b-button>
          </div>
        </li>
      </ul>
    </div>
    <b-modal ref="alert-modal" hide-footer>
      <template v-slot:modal-title>通知訊息</template>
      <div class="d-block text-center">
        <h3>無法獲取Github Repos</h3>
      </div>
      <b-button class="mt-3" block @click="$refs['alert-modal'].hide()">關閉</b-button>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      infiniteScrollCount: 1,
      allowAdd: true, // A flag for avoiding repeat request.
      repos: []
    };
  },
  methods: {
    getRepos() {
      const config = {
        baseURL: "https://api.github.com",
        url: "/user/repos",
        method: "get",
        headers: {
          Authorization: "token e6ea03bc0020faf1cb8c884ffc6c26b47c1d0b0b"
        }
        // params: { page: this.infiniteScrollCount, per_page: 100 }
      };
      this.axios.request(config).then(response => {
        if (!response.data) {
          this.$refs["alert-modal"].show();
          return;
        }
        this.repos.push.apply(this.repos, response.data);
        this.allowAdd = true;
      });
    },
    setEvents() {
      const that = this;
      window.onscroll = function() {
        if (!that.allowAdd) return;
        if (
          window.innerHeight + window.scrollY + 1 >=
          document.body.offsetHeight
        ) {
          that.allowAdd = false;
          that.infiniteScrollCount++;
          that.getRepos();
        }
      };
    }
  },
  computed: {},
  created() {},
  mounted() {
    this.getRepos();
    this.setEvents();
  },
  beforeDestroy() {}
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background: url("./assets/bg.jpg") repeat-x center top fixed #f9f9f9;
  color: #2c3e50;
  $color-border: #7caeba;
  @mixin flex-content($dir: row, $jus: center, $ali: center) {
    display: flex;
    flex-direction: $dir;
    justify-content: $jus;
    align-items: $ali;
  }
  .infinite-scroll-card {
    position: fixed;
    right: 0;
    top: 0;
  }
  .wrapper {
    width: 1250px;
    margin: 0 auto;
    padding: 2rem 0;
    .repos {
      min-height: calc(100vh - 100px);
      .repo-item {
        @include flex-content(column, flex-start, center);
        border: 2px solid $color-border;
        border-radius: 10px;
        background: rgba(255, 255, 255, 0.8);
        padding: 2rem;
        &:nth-child(n + 2) {
          margin-top: 1rem;
        }
        .bottom-row {
          width: 100%;
          @include flex-content(row, space-between, flex-end);
        }
        .info-row {
          @include flex-content(row, flex-end, center);
          .badge {
            &:nth-child(n + 2) {
              margin-left: 1rem;
            }
          }
        }
      }
    }
  }
}
</style>
