<template>
  <view class="background" @click="closeSelf" @touchmove.stop.prevent></view>
  <scroll-view class="frame" scroll-y="true" @touchmove.stop>
    <view class="main">
      <view class="comment">
        <view
          :style="{
            backgroundImage: 'url( ' + mainComment.user.avatarUrl + ')'
          }"
          class="avatar"
        ></view>
        <view class="thread">
          <view class="content">
            <view class="left">
              <view class="upper">
                <view class="username">{{ mainComment.user.nickname }}</view>
                <view class="timestamp"
                  >· {{ displayTime(mainComment.createAt) }}
                </view>
              </view>
              <view class="lower">{{ mainComment.text }}</view>
            </view>
            <view class="right">
              <view class="likes-frame" @click="likeComment(mainComment)">
                <view v-if="mainComment.isLiked" class="thumb liked"></view>
                <view v-else class="thumb"></view>
                <view class="likes">{{ mainComment.likeCount }}</view>
              </view>
            </view>
          </view>
        </view>
      </view>
      <view v-for="(comment, index) in comments" :key="index" class="reply">
        <view
          :style="{
            backgroundImage: 'url( ' + comment.user.avatarUrl + ')'
          }"
          class="avatar"
          style="margin-left: calc(42 / 390 * 100vw)"
        ></view>
        <view class="thread">
          <view class="content">
            <view class="left">
              <view class="upper">
                <view class="username">{{ comment.user.nickname }}</view>
                <view class="timestamp"
                  >· {{ displayTime(comment.createAt) }}
                </view>
              </view>
              <view class="lower">{{ comment.text }}</view>
            </view>
            <view v-if="comment.likeCount >= 0" class="right">
              <view class="likes-frame" @click="likeComment(comment)">
                <view v-if="comment.isLiked" class="thumb liked"></view>
                <view v-else class="thumb"></view>
                <view class="likes">{{ comment.likeCount }}</view>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
  </scroll-view>
</template>

<script lang="ts" setup>
import { Comment } from "@/apis/schemas";
import { displayTime } from "@/utils/time";
import { ref } from "vue";
import { getCommentsData, likeComment } from "@/pages/moment/utils";
import { onReachBottom } from "@dcloudio/uni-app";
import { CommentType } from "@/apis/comment/comment-interfaces";

const props = defineProps<{
  mainComment: Comment;
}>();

const comments = ref<Comment[]>([]);

let allCommentsLoaded = false;
let isCommentsLoaded = true;
let page = 0;
const localGetCommentsData = async () => {
  isCommentsLoaded = false;
  getCommentsData({
    id: props.mainComment.id,
    type: CommentType.Comment,
    page: page
  }).then((res) => {
    for (let i = 0; i < res.data.length; i++) {
      comments.value.push(res.data[i]);
    }
    isCommentsLoaded = true;
    page += 1;
    if (res.data.length < 10) allCommentsLoaded = true;
  });
};

localGetCommentsData();

onReachBottom(() => {
  if (isCommentsLoaded && !allCommentsLoaded) {
    localGetCommentsData();
  }
});

const emits = defineEmits(["closeReply"]);

function closeSelf() {
  emits("closeReply");
}
</script>

<style lang="scss" scoped>
.background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #22222288;
  z-index: 100;
}

.frame {
  overflow: scroll;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100vw;
  max-height: 75vh;
  z-index: 102;
  background-color: #fff;
  border-radius: 5vw 5vw 0 0;
}

.main {
  padding: calc(20 / 390 * 100vw);
  box-sizing: border-box;

  .comment,
  .reply {
    display: flex;
    color: #353535;
    font-size: calc(12 / 390 * 100vw);

    .avatar {
      width: calc(32 / 390 * 100vw);
      height: calc(32 / 390 * 100vw);
      border-radius: 50%;
      background-size: 100% 100%;
      margin-right: calc(11 / 390 * 100vw);
    }

    .thread {
      flex: auto;

      .content {
        display: flex;

        .left {
          flex: auto;
          width: 0;

          .upper {
            display: flex;
            color: #b8b8b8;

            .username {
              margin-right: calc(10 / 390 * 100vw);
            }
          }

          .lower {
            font-size: calc(14 / 390 * 100vw);
            margin: calc(8 / 390 * 100vw) 0;
          }
        }

        .right {
          display: flex;
          width: calc(28 / 390 * 100vw);
          justify-content: flex-end;
          padding-top: calc(22 / 390 * 100vw);

          .likes-frame {
            height: calc(20 / 390 * 100vw);
            display: flex;
            align-items: center;

            .thumb {
              background-image: url("../../static/images/like_grey_0.png");
              width: calc(12 / 390 * 100vw);
              height: calc(12 / 390 * 100vw);
              background-size: 100% 100%;

              &.liked {
                background-image: url("../../static/images/like_grey_1.png");
              }
            }

            .likes {
              width: calc(12 / 390 * 100vw);
              text-align: right;
            }
          }

          color: #b8b8b8;
        }
      }

      .sub-replies {
        display: flex;
        margin: calc(10 / 390 * 100vw) 0;

        .avatar {
          width: calc(22 / 390 * 100vw);
          height: calc(22 / 390 * 100vw);
        }

        .content {
          flex: auto;
          display: flex;

          .left {
            flex: auto;
            width: 0;
          }
        }
      }

      .more-sub-replies {
        color: #696969;
        margin-left: calc(33 / 390 * 100vw);
        display: flex;
        align-items: center;

        .down-arrow {
          background-image: url("../../static/images/down-black.png");
          width: calc(8 / 390 * 100vw);
          height: calc(5 / 390 * 100vw);
          background-size: 100% 100%;
          margin-left: calc(8 / 390 * 100vw);
        }
      }
    }
  }

  .comment {
    margin-bottom: calc(20 / 390 * 100vw);
  }

  .reply {
    margin: calc(20 / 390 * 100vw) 0;
  }
}
</style>
