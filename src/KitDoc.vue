<template>
  <div class="kit-doc">
    <kit-doc-header
      :nav="config.header"
      :logo="config.logo"
      :active="config.active || '组件'"
    />
    <kit-doc-nav :navConfig="config.nav" :base="config.navBase" />
    <kit-doc-container :hasSimulator="!!(simulator && simulator.length)">
      <kit-doc-content>
        <kit-doc-title :title="docTitle" />
        <slot></slot>
        <kit-doc-footer-nav
          :base="config.navBase"
          :list="docList"
          :index="currentIndex"
        />
      </kit-doc-content>
    </kit-doc-container>
    <kit-doc-simulator v-if="simulator" :src="simulator" />
    <!--
      <kit-doc-simulator
        v-for="(url, index) in simulators"
        v-show="index === currentSimulator"
        :src="url"
        :key="url"
      />
    -->
    <!-- <kit-doc-footer :config="config.footer" /> -->
  </div>
</template>

<script>
// import { findIndex } from 'lodash'
import { camelCase, upperFirst } from "./utils"; // 使用简版
// 不能用已有标签，比如 header
// import DocNav from './components/Nav.vue'
// import DocBlock from './components/Block.vue'
// import DocHeader from './components/Header.vue'
// import DocFooter from './components/Footer.vue'
// import DocContainer from './components/Container.vue'
// import DocContent from './components/Content.vue'
// import DocTitle from './components/Title.vue'
// import DocFooterNav from './components/FooterNav.vue'
// import DocSimulator from './components/Simulator.vue'
// import DocDemoBlock from './components/DemoBlock.vue'

export default {
  name: "kit-doc",

  components: {
    // DocNav,
    // DocHeader,
    // DocFooter,
    // DocBlock,
    // DocContainer,
    // DocContent,
    // DocTitle,
    // DocFooterNav,
    // DocSimulator,
    // DocDemoBlock,
  },

  props: {
    config: {
      type: Object,
      required: true
    },
    currentSimulator: Number,
    simulator: String,
    simulators: {
      type: Array,
      default: () => {}
    }
  },

  data() {
    return {
      docList: []
    };
  },

  created() {
    this.setDocsList();
  },

  computed: {
    currentPath() {
      const baseUrl = this.config.navBase + "/"; // eg: '/docs'
      return this.$route.path.slice(baseUrl.length);
    },
    currentIndex() {
      const { currentPath, docList } = this;
      let index = -1;
      // findIndex
      for (let i = 0; i < docList.length; i++) {
        const item = docList[i];
        if (item.path === currentPath || item.link === currentPath) {
          index = i;
          break;
        }
      }
      // return findIndex(this.docList, { path: this.currentPath })
      return index;
    },
    docTitle() {
      const { title = "" } = this.docList[this.currentIndex] || "";
      return upperFirst(camelCase(title));
    }
  },

  methods: {
    setDocsList() {
      const { nav = [] } = this.config;
      for (let i = 0; i < nav.length; i++) {
        const navItem = nav[i];
        if (!navItem.groups) {
          this.docList.push(nav[i]);
        } else {
          for (let j = 0; j < navItem.groups.length; j++) {
            this.docList = this.docList.concat(navItem.groups[j].list);
          }
        }
      }
    }
  }
};
</script>

<style lang="stylus">
@import './style/index';
</style>
