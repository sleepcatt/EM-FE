<template>
  <div class="emfe-menu" ref="menu">
    <h3 class="emfe-menu-header" :class="{'emfe-menu-header-on': indexs[0] === 'header'}">
      <router-link v-if="header.router" :to="header.router" class="emfe-menu-header-link" exact-active-class="emfe-menu-header-on">
        <emfe-icon className="emfe-menu" :type="header.icon" />
        <span class="emfe-menu-header-text">{{header.name}}</span>
      </router-link>
      <div v-else class="emfe-menu-header-link" @click="goToPath(header)">
        <emfe-icon className="emfe-menu" :type="header.icon" />
        <span class="emfe-menu-header-text">{{header.name}}</span>
      </div>
    </h3>
    <ul class="emfe-menu-list" v-for="(menu, menuIndex) in datas" :key="menuIndex">
      <template v-for="(item, itemIndex) in menu">
        <router-link v-if="item.router" class="emfe-menu-list-li" :to="item.router" tag="li" exact-active-class="emfe-menu-list-li-on">
          <img class="emfe-menu-list-li-img" v-if="item.icon === 'yingxiaogongju'" src="./yingxiao.svg" alt="">
          <emfe-icon v-else className="emfe-menu" :type="item.icon" />
          <span class="emfe-menu-text">{{item.name}}</span>
        </router-link>
        <li v-else class="emfe-menu-list-li" :class="{'emfe-menu-list-li-on': indexs.length == 2 && menuIndex === indexs[0] && itemIndex === indexs[1]}" @click="goToPath(item)">
          <img class="emfe-menu-list-li-img" v-if="item.icon === 'yingxiaogongju'" src="./yingxiao.svg" alt="">
          <emfe-icon v-else className="emfe-menu" :type="item.icon" />
          <span class="emfe-menu-text">{{item.name}}</span>
        </li>
      </template>
    </ul>
  </div>
</template>
<script>
import Contant from '../../../contant';
import O from '../../../tools/o';

export default {
  name: 'EmfeMenu',
  data() {
    return {
      Contant,
      indexs: [],
    };
  },
  props: {
    className: {
      type: String,
      default: '',
    },
    datas: Array,
    header: {
      type: Object,
    },
  },
  mounted() {
    this.testUrl(this.datas);
  },
  methods: {
    testUrl(val) {
      const { href } = window.location;
      val.forEach((data, dataIndex) => {
        data.forEach((menu, menuIndex) => {
          if (href.indexOf(menu.path) > -1) {
            this.indexs.push(dataIndex, menuIndex);
          }
        });
      });
      // 比对头部
      if (O.hOwnProperty(this.header, 'path') && href.indexOf(this.header.path) > -1) {
        this.indexs.push('header');
      }
    },
    goToPath(item) {
      if (O.hOwnProperty(item, 'path')) {
        window.location.href = item.path;
      }
    },
  },
  watch: {
    datas(val) {
      this.testUrl(val);
    },
  },
};
</script>
