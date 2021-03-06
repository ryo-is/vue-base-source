<template lang="pug">
  v-app
    v-card(height="60px" flat tile)
      v-app-bar.pl-12.pr-6(
        color="indigo" height="60px"
        prominent dense absolute elevate-on-scroll scroll-target=".main-content"
      )
        v-toolbar-items.hidden-sm-and-down(v-if="hiddenToolbarItems")
          v-btn(
            :color="changeLinkButtonProps('/', 'color')"
            :text="changeLinkButtonProps('/', 'text')"
            dark depressed width=160 @click="linkPage('/')") Home
        v-spacer
        v-toolbar-items.hidden-sm-and-down(v-if="hiddenToolbarItems")
          v-switch.mr-5.theme-switch(
            v-model="darkTheme" dark color="grey darken-1"
            label="テーマを変える" style="padding-top: 19px"
            @change="changeTheme()"
          )
        v-toolbar-items.hidden-sm-and-down(v-if="hiddenToolbarItems")
          v-btn.signout-button(text @click="signout()" dark) ログアウト
    v-content
      v-overlay(:value="overlay")
        v-progress-circular(indeterminate size="80" color="indigo" width="5")
      router-view
</template>

<script lang="ts">
import Vue from "vue"
import router from "./router"
import { Auth } from "aws-amplify"
import { flagsNamespacedHelper } from "@/stores/flags"

const flagsMapState = flagsNamespacedHelper.mapState

export default Vue.extend({
  data() {
    return {
      darkTheme: true
    }
  },
  computed: {
    ...flagsMapState({
      hiddenToolbarItems: (state: any) => state.hiddenToolbarItems,
      overlay: (state: any) => state.overlay
    }),
    changeLinkButtonProps(): Function {
      const currentRoute: string = this.$route.path
      return (routeString: string, propText: string): string | boolean => {
        switch (propText) {
          case "text":
            return routeString !== currentRoute
          default:
            return routeString === currentRoute ? "indigo darken-3" : ""
        }
      }
    }
  },
  methods: {
    async signout(): Promise<void> {
      try {
        await Auth.signOut()
        router.push("/auth")
      } catch (err) {
        console.error(err)
      }
    },
    linkPage(linkPath: string): void {
      if (linkPath !== this.$route.path) {
        router.push(linkPath)
      }
    },
    changeTheme(): void {
      this.$vuetify.theme.dark = this.darkTheme
    }
  }
})
</script>

<style lang="scss">
body {
  margin: 0;

  // スクロールバー非表示
  ::-webkit-scrollbar {
    width: 0px;
  }

  &::-webkit-scrollbar {
    width: 0px;
  }

  // &::-webkit-scrollbar-track {
  //   background-color: $scrollbar-track-color;
  // }

  // &::-webkit-scrollbar-thumb {
  //   background: rgba($scrollbar-thumb-color, .6);
  //   border-radius: 8px;
  // }
}

#app {
  font-family: "Avenir", "Helvetica Neue", "Helvetica", "Arial", "Hiragino Sans",
    "ヒラギノ角ゴシック", YuGothic, "Yu Gothic", "メイリオ", Meiryo,
    "ＭＳ Ｐゴシック", "MS PGothic";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

.theme-switch {
  &.v-input {
    .v-label {
      color: white;
      font-size: 14px;
      line-height: 22px;
    }
  }
}
</style>
