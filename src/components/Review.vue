<script setup>
const props = defineProps({
  item: {
    userName: String,
    rating: Number,
    menuName: String,
    menuCount: Number,
    comment: String,
    imagePath: String,
    ownerComment: String,
    created: String,
  },
});
</script>

<template>
  <div id="big-box" class="border rounded p-3 mb-2">
    <div class="border-top">
      <!-- 이미지 -->
      <div class="p-2">
        <div>
          <!-- 주문 목록 텍스트 -->
          <div class="mb-2">
            <span>{{
              props.item.menuName +
              (props.item.menuCount > 1
                ? " 외 " + (props.item.menuCount - 1) + "개"
                : "")
            }}</span>
          </div>
          <!-- 이미지 -->
          <div class="review-image border">
            <!-- 이미지 필요 -->
            <div></div>
          </div>
        </div>
      </div>

      <div class="user-box">
        <!-- 유저 이름/별점 -->
        <div class="user-top">

          <div id="name-box">
            <span id="name">{{ props.item.userName }}</span>
            <span class="star">★★★★★</span>
          </div>
          <div id="name-left-box">
            <span class="created">{{ props.item.created }}</span>
          </div>
        </div>
        <!-- 유저 코멘트 -->
        <div id="u-comment" class="mb-2">
          <span>{{ props.item.comment }}</span>
        </div>
      </div>
    </div>

    <!-- 사장님 코멘트 -->
    <div
      v-if="props.item.ownerComment !== null && props.item.ownerComment !== ''"
    >
      <div class="owner-comment border rounded p-2">
        {{ props.item.ownerComment }}
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
#u-comment {
  border: #797979 1px solid;
  border-radius: 5px;
  width: 476px;
  height: 160px;
  padding: 25px;
}
.border {
}
.border-top {
  display: flex;
  border: none !important;
}
.owner-comment {
  //   background-color: #ff6666;
  border: 2px #ff6666 solid !important;
  margin-top: 20px;
}

.review-image {
  width: 270px !important;
  height: 160px;
  border-radius: 10px;
  overflow: hidden;
}
#big-box {
  padding: 20px !important;
  margin-top: 50px;
  border: #797979 1px solid !important;
}
.user-box {
  margin-top: 7px;
  margin-left: 30px;
}
.user-top {
  display: flex;
  align-items: center;
    justify-content: space-between;
  width: 100%;
  align-items: center;
}
.created {
  font-size: 13px;
}

#name {
  font-size: 20px;
  margin-right: 10px;
}

.star {
  font-size: 20px;
}
</style>
