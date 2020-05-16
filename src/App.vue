<template>
  <div id="app">
    <div class="wrapper">
      <b-modal ref="alert-modal" hide-footer>
        <template v-slot:modal-title>通知訊息</template>
        <div class="d-block text-center">
          <h3>無法獲取Github Repos</h3>
        </div>
        <b-button class="mt-3" block @click="$refs['alert-modal'].hide()">關閉</b-button>
      </b-modal>

      <div class="repo-item" v-for="(repo, index) in repos" :key="index">
        <div class="display-3">{{ repo.name }}</div>
        <p>{{ repo.description }}</p>
        <div class="bottom-row">
          <div class="info-row">
            <b-badge variant="dark">{{ repo.language }}</b-badge>
            <b-badge>Created: {{ repo.created_at }}</b-badge>
            <b-badge>Updated: {{ repo.updated_at }}</b-badge>
          </div>
          <b-button :href="repo.html_url" target="_blank">HTML Link</b-button>
        </div>
      </div>
      <b-jumbotron class="repo-item" v-for="(repo, index) in repos" :key="index">
        <template v-slot:header>{{ repo.name }}</template>
        <template v-slot:lead>{{ repo.description }}</template>
        <hr class="my-4" />
        <p>測試</p>
        <b-button variant="primary" :href="repo.html_url" target="_blank">Repo Link</b-button>
      </b-jumbotron>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      repos: [
        {
          name: "123",
          description: "haha",
          html_url: "google.com",
          created_at: "2020-05-14T17:04:47Z",
          updated_at: "2020-05-15T17:11:32Z",
          language: "JavaScript"
        },
        {
          name: "456",
          description: "haha2",
          html_url: "google.com",
          created_at: "2020-05-15T17:04:47Z",
          updated_at: "2020-05-16T17:11:32Z",
          language: "JavaScript"
        },
        {
          name: "789",
          description: "haha3",
          html_url: "google.com",
          created_at: "2020-05-16T17:04:47Z",
          updated_at: "2020-05-17T17:11:32Z",
          language: "JavaScript"
        }
      ]
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
      };
      this.axios.request(config).then(response => {
        if (!response.data) {
          this.$refs["alert-modal"].show();
          // this.dismissCountDown = this.dismissSecs;
          return;
        }
        this.repos = response.data;
        console.log(this.repos);
      });
    }
  },
  computed: {},
  created() {},
  mounted() {
    // this.getRepos();
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
  background: url("./assets/bg.jpg") repeat-x center top #f9f9f9;
  color: #2c3e50;
  @mixin flex-content($dir: row, $jus: center, $ali: center) {
    display: flex;
    flex-direction: $dir;
    justify-content: $jus;
    align-items: $ali;
  }
  .wrapper {
    width: 1250px;
    min-height: 100vh;
    margin: 0 auto;
    padding: 1rem 0;
    .repo-item {
      @include flex-content(column, flex-start, center);
      border: 2px solid #7caeba;
      border-radius: 10px;
      background: rgba(255, 255, 255, 0.8);
      padding: 1rem 2rem;
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
          &:nth-child(n+2) {
            margin-left: 1rem;
          }
        }
      }
    }
  }
}
</style>
