<template>
  <view class="panel">
    <button
      :class="'publish ' + (props.canPublish && !disabled ? 'can' : 'disabled')"
      :disabled="disabled"
      @click="publish"
    >
      {{ props.text }}
    </button>
    <view class="notice">
      发布前请先阅读
      <text class="nobody-will-read" @click="showDeal"> 《用户服务协议》 </text>
      及
      <text class="nobody-will-read" @click="showPolicy">
        《个人信息保护政策》
      </text>
      ，一旦发布即被视为同意上述协议和政策
    </view>
  </view>
  <deal v-if="isShow && type === 1"></deal>
  <policy v-if="isShow && type === 2"></policy>
</template>

<script setup lang="ts">
import Policy from "@/components/deal-policy/policy.vue";
import Deal from "@/components/deal-policy/deal.vue";
import { ref } from "vue";

interface Props {
  canPublish: boolean;
  text: string;
}
const props = withDefaults(defineProps<Props>(), {
  canPublish: false,
  text: "发布"
});
const emits = defineEmits(["publish"]);

const isShow = ref(false);
const type = ref(0);
const disabled = ref(false);

const publish = () => {
  if (!props.canPublish) return;
  disabled.value = true;
  emits("publish");
  setTimeout(() => {
    disabled.value = false;
  }, 2000);
};

function showDeal() {
  type.value = 1;
  isShow.value = !isShow.value;
}

function showPolicy() {
  type.value = 2;
  isShow.value = !isShow.value;
}
</script>

<style scoped lang="scss">
.panel {
  box-sizing: border-box;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100vw;
  height: 36vw;
  //box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 3px 10px 0 rgba(0, 0, 0, 0.19);
  display: flex;
  flex-direction: column;
  justify-content: center;
  background-color: #fdfdfd;
  .publish {
    box-sizing: border-box;
    margin-bottom: 3vw;
    width: 88vw;
    margin-left: 6vw;
    background-color: #1fa1ff;
    color: #ffffff;
    font-size: 4vw;
    text-align: center;
    height: 10vw;
    border-radius: 5vw;
    line-height: 10vw;
    transition-duration: 0.05s;
    letter-spacing: 1vw;
    &.can {
      &:active {
        filter: brightness(80%);
      }
    }
    &.disabled {
      background-color: #c7c7c7;
    }
  }
  .notice {
    box-sizing: border-box;
    font-size: 3vw;
    width: 88vw;
    margin-left: 6vw;
    .nobody-will-read {
      color: #1fa1ff;
    }
  }
}
</style>
