<template>
  <div class="emfe-modal" v-if="show">
    <div class="emfe-modal-mask"></div>
    <div class="emfe-modal-wrap" :style="{width: `${width}px`}">
      <div class="emfe-modal-header">
        <div class="emfe-modal-header-inner">{{ title }}</div>
        <div class="emfe-modal-header-close" @click="closeModal">＋</div>
      </div>
      <div class="emfe-modal-main" :class="mainName">
        <slot name="modal-main"></slot>
      </div>
      <div class="emfe-modal-footer" v-if="footerFlg">
        <emfe-button className="emfe-modal" @click="cancel" v-if="cancelFlg">{{cancelText}}</emfe-button>
        <emfe-button theme="primary" @click="ok" v-if="okFlg">{{okText}}</emfe-button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'EmfeModal',
  data() {
    return {
      footerFlg: true,
    };
  },
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    width: {
      type: Number,
      default: 440,
    },
    title: {
      type: String,
      default: '提示',
    },
    className: {
      type: String,
      default: '',
    },
    cancelText: {
      type: String,
      default: '取消',
    },
    okText: {
      type: String,
      default: '确定',
    },
    cancelFlg: {
      type: [String, Boolean],
      default: true,
    },
    okFlg: {
      type: [String, Boolean],
      default: true,
    },
    tip: {
      type: Boolean,
      default: false,
    },
  },
  created() {
    if (!this.cancelFlg && !this.okFlg) {
      this.footerFlg = false;
    }
  },
  computed: {
    mainName() {
      return [
        {
          [`${this.className}-modal-main`]: !!this.className,
          'emfe-modal-main-tip': this.tip,
        },
      ];
    },
  },
  methods: {
    cancel() {
      this.$emit('cancel');
    },
    ok() {
      this.$emit('ok');
    },
    closeModal() {
      this.$emit('close');
    },
  },
};
</script>
