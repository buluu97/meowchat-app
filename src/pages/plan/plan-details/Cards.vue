<template>
  <div class="card1">
    <div class="title">
      帮助<text style="color: #1f5eff">{{ plan?.cat?.name || "全体猫猫" }}</text
      >{{ props.plan.name }}
      <img :src="Icons.State_Frame" class="state-frame" />
      <view class="state">{{ planStateMap(props.plan.planState) }}</view>
    </div>

    <div class="category">
      <view class="category-style">
        <text class="category-content">{{
          planTypeMap(props.plan.planType)
        }}</text>
      </view>
      <view class="category-style">
        <text class="category-content">{{ props.plan.name }}</text>
      </view>
    </div>

    <div class="content">
      {{ props.plan.description }}
    </div>
  </div>

  <div class="card2">
    <div class="title-target">
      <text class="target">目标小鱼干： {{ props.plan.maxFish }}</text>
      <img :src="Icons.LittleFish" class="small-icon" />
    </div>

    <view class="time"
      >募集时间： {{ displayDate(props.plan.startTime, "YYYY/MM/DD") }} -
      {{ displayDate(props.plan.endTime, "YYYY/MM/DD") }}</view
    >

    <div class="dialog-box">
      <img :src="Icons.DialogBox" class="box" />
      <view class="dialog-content">
        还差<text style="color: blue">{{
          props.plan.maxFish - props.plan.nowFish
        }}</text
        >小鱼干
      </view>
    </div>

    <view class="progress-box">
      <view
        class="progress"
        :style="{
          width: (83 * props.plan.nowFish) / props.plan.maxFish + 'vw'
        }"
      ></view>
    </view>

    <view class="implication"
      >已获得{{ props.plan.nowFish }}小鱼干助力，还需要{{
        props.plan.maxFish - props.plan.nowFish
      }}小鱼干</view
    >
  </div>
  <div class="card3">
    <view>
      <text class="card3-title">执行说明</text>
      <text
        v-if="props.plan.planState === PlanState.StateComplete"
        class="card3-state"
        >已完成</text
      >
    </view>
    <text class="card3-details">{{ plan.instruction }}</text>
  </div>
  <template
    v-if="
      props.plan.planState === PlanState.StateComplete && props.plan.imageUrls
    "
  >
    <div class="card4">
      <view>
        <text class="card4-title">任务返图</text>
        <text
          v-if="props.plan.planState === PlanState.StateComplete"
          class="page"
          >{{ nowPicIndex + 1 }} /
          {{ props.plan.imageUrls ? props.plan.imageUrls.length : 0 }}</text
        >
      </view>

      <div class="pic-example">
        <img
          :src="props.plan.imageUrls[nowPicIndex]"
          class="task-pic"
          @click="
            onClickImage(
              props.plan.imageUrls[nowPicIndex],
              props.plan.imageUrls
            )
          "
        />
        <img
          :src="Icons.Pic_Left"
          class="pic-left"
          @click="
            nowPicIndex =
              (nowPicIndex - 1 + props.plan.imageUrls.length) %
              props.plan.imageUrls.length
          "
        />
        <img
          :src="Icons.Pic_Right"
          class="pic-right"
          @click="nowPicIndex = (nowPicIndex + 1) % props.plan.imageUrls.length"
        />
      </div>
    </div>
  </template>
  <div class="card5">
    <view class="card5-title">任务总结</view>
    <template v-if="props.plan.planState === PlanState.StateComplete">
      <view class="card5-content">
        {{ props.plan.summary }}
      </view>
    </template>
    <template v-else>
      <template v-if="props.plan.planState === PlanState.StateFunding">
        <text class="card5-content">计划还未开始</text>
      </template>
      <template v-else
        ><text class="card5-content">等待社团上传</text></template
      >
    </template>
  </div>
</template>

<script setup lang="ts">
import { Plan, PlanState } from "@/apis/schemas";
import { Icons } from "@/utils/url";
import { planTypeMap, planStateMap } from "@/pages/plan/utils";
import { ref } from "vue";
import { onClickImage } from "@/components/utils";
import { displayDate } from "@/utils/time";
const props = defineProps<{
  plan: Plan;
}>();

const nowPicIndex = ref<number>(0);
</script>

<style scoped lang="scss">
.card1 {
  border-radius: 2vw;
  width: 91.7vw;
  background-color: white;
  flex-direction: row;
  margin: 4vw;
  justify-content: space-between;
  box-sizing: border-box;
  padding-left: 1vw;
  padding-right: 1vw;
  padding-bottom: 4vw;

  .title {
    font-size: 4.5vw;
    letter-spacing: 0.4vw;
    font-weight: bold;
    margin-left: 5vw;
    writing-mode: horizontal-tb;
    text-align: left;
    display: inline-block;
    margin-top: 5vw;
    margin-bottom: -2vw;

    .state-frame {
      display: flex;
      height: 5.9vw;
      width: 18.46vw;
      margin-left: 67vw;
      margin-top: -6vw;
    }

    .state {
      font-size: 3.7vw;
      color: royalblue;
      letter-spacing: 0.3vw;
      font-weight: bold;
      position: relative;
      left: 69.5vw;
      top: -5.53vw;
    }
  }

  .category {
    display: flex;
    width: 85vw;
    margin-bottom: 4.5vw;
    margin-left: 5vw;
    height: 7.2vw;
    margin-top: -3vw;

    .category-style {
      font-size: 3.5vw;
      letter-spacing: 0.2vw;
      font-weight: bold;
      padding: 0.5vw 1.5vw;
      margin-right: 3vw;
      border: 0.7vw solid transparent;
      border-radius: 1vw;
      background-clip: padding-box, border-box;
      background-origin: padding-box, border-box;
      background-image: linear-gradient(to right, #fff, #fff),
        linear-gradient(to right, #0000ff, #1e90ff);
    }

    .category-content {
      background: linear-gradient(to right, #0000ff, #1e90ff);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
    }
  }

  .content {
    font-size: 3vw;
    letter-spacing: 0.4vw;
    color: grey;
    margin-left: 5vw;
    margin-right: 3vw;
    display: flex;
    text-align: left;
  }
}

.card2 {
  border-radius: 2vw;
  width: 91.7vw;
  background-color: white;
  flex-direction: row;
  margin: 4vw;
  justify-content: space-between;
  box-sizing: border-box;
  padding-top: 5vw;
  padding-bottom: 5vw;
  padding-left: 0.8vw;
  padding-right: 0.8vw;

  .title-target {
    width: 100vw;
    display: flex;
    margin-bottom: 2.2vw;

    .target {
      font-size: 4.5vw;
      letter-spacing: 0.4vw;
      font-weight: bold;
      margin-left: 5vw;
      writing-mode: horizontal-tb;
      text-align: left;
    }

    .small-icon {
      height: 6vw;
      width: 6vw;
      margin-left: 1vw;
    }
  }

  .time {
    font-size: 3.5vw;
    letter-spacing: 0.4vw;
    color: grey;
    margin-left: 5vw;
    margin-top: 0;
    margin-bottom: 5vw;
  }

  .dialog-box {
    margin-bottom: 0;
    position: relative;
    margin-left: 4vw;

    .box {
      width: 35vw;
      height: 12vw;
      z-index: 0;
      margin-top: -2vw;
    }

    .dialog-content {
      font-size: 3.5vw;
      font-weight: bold;
      letter-spacing: 0.4vw;
      z-index: 1;
      position: relative;
      top: -11.5vw;
      margin-left: 3vw;
    }
  }

  .progress-box {
    margin-left: 5vw;
    margin-right: 5vw;
    width: 83vw;
    height: 2vw;
    border-radius: 1vw;
    background: #dcdcdc;
    margin-top: -5vw;

    .progress {
      width: 60vw;
      height: 2vw;
      background: linear-gradient(to right, #0000ff, #1e90ff);
      border-radius: 1vw;
    }
  }

  .implication {
    font-size: 3vw;
    letter-spacing: 0.1vw;
    color: grey;
    margin-left: 5vw;
    margin-top: 1.5vw;
    margin-bottom: 3vw;
  }
}

.card3 {
  border-radius: 2vw;
  width: 91.7vw;
  background-color: white;
  flex-direction: row;
  margin: 4vw;
  padding-left: 6vw;
  justify-content: space-between;
  box-sizing: border-box;
  padding-top: 5vw;
  padding-bottom: 5vw;
  padding-right: 0.8vw;

  .card3-title {
    font-size: 4.5vw;
    letter-spacing: 0.4vw;
    //margin-left: 5vw;
    font-weight: bold;
  }

  .card3-state {
    background-color: dodgerblue;
    color: white;
    font-size: 3.5vw;
    font-weight: bold;
    letter-spacing: 0.3vw;
    border-radius: 1vw;
    padding: 0.5vw 1.7vw;
    //margin-left: 4vw;
  }

  .card3-details {
    font-size: 3.3vw;
    letter-spacing: 0.3vw;
    color: grey;
  }
}

.card4 {
  border-radius: 2vw;
  width: 91.7vw;
  background-color: white;
  flex-direction: row;
  margin: 4vw;
  justify-content: space-between;
  box-sizing: border-box;
  padding-left: 1vw;
  padding-right: 1vw;
  padding-bottom: 0vw;
  position: relative;

  .card4-title {
    font-size: 4.5vw;
    letter-spacing: 0.4vw;
    font-weight: bold;
    margin-left: 5vw;
    writing-mode: horizontal-tb;
    text-align: left;
    display: inline-block;
    margin-top: 5vw;
    margin-bottom: -2vw;
  }

  .page {
    font-size: 3vw;
    color: grey;
    margin-left: 58vw;
  }

  .pic-example {
    .task-pic {
      width: 91.7vw;
      height: 51.8vw;
      text-align: center;
      margin-left: -1vw;
      margin-top: 3vw;
      margin-bottom: -9vw;
    }

    .pic-left {
      width: 6.9vw;
      height: 6.9vw;
      margin-left: 3vw;
      position: absolute;
      top: 37vw;
      left: 0vw;
    }

    .pic-right {
      width: 6.9vw;
      height: 6.9vw;
      margin-left: 3vw;
      position: absolute;
      top: 37vw;
      left: 78vw;
    }
  }
}

.card5 {
  border-radius: 2vw;
  width: 91.7vw;
  background-color: white;
  flex-direction: row;
  margin: 4vw;
  justify-content: space-between;
  box-sizing: border-box;
  padding-left: 1vw;
  padding-right: 1vw;
  padding-bottom: 4vw;

  .card5-title {
    font-size: 4.5vw;
    letter-spacing: 0.4vw;
    font-weight: bold;
    margin-left: 5vw;
    writing-mode: horizontal-tb;
    text-align: left;
    display: inline-block;
    margin-top: 5vw;
    margin-bottom: 3vw;
  }

  .card5-content {
    font-size: 3vw;
    letter-spacing: 0.4vw;
    color: grey;
    margin-left: 5vw;
    margin-right: 3vw;
    display: flex;
    text-align: left;
  }
}
</style>
