<script setup>
import { useAccountStore } from "@/stores/account";
import { useFavoriteStore } from "@/stores/favoriteStore";
import { ref, onMounted, reactive, computed } from "vue";
import { getFavoriteList, addFavorite, deleteFavorite } from "@/services/favoriteService";
import { useRoute, useRouter } from "vue-router";
import { watch } from "vue";

import defaultImage from '@/imgs/owner/haniplogo_sample2.png';
import favoriteImage from '@/imgs/love.png';
import noFavoriteImage from '@/imgs/loveBoard.png'
import ratingImage from '@/imgs/star.png';



const router = useRouter();
const route = useRoute();

const account = useAccountStore();
const favoriteStore = useFavoriteStore();

const state = reactive({
  // 찜 가게 정보
  favorites: []
});

// 찜 목록 불러오는 함수
const fetchFavorites = async () => {
  const res = await getFavoriteList();

  if (res === undefined || res.data.resultStatus !== 200) {
    const modal = new bootstrap.Modal(document.getElementById("favorite-error"));
    modal.show();
    return;
  }

  state.favorites = res.data.resultData;
  // 찜 목록 추가/삭제 함수 실행을 위해 찜 상태값을 강제로 넣어줌
  state.favorites.map(favorite => favorite.favorite = true);

  // 찜 ID들을 Pinia store에 저장
  const favoriteIds = state.favorites.map(favorite => favorite.storeId);
  favoriteStore.setFavorites(favoriteIds);
};

// 찜 목록 추가/삭제 함수
const toggleFavorite = async store => {
  if (account.state.loggedIn) {
    const storeId = Number(store.storeId);

    const res = store.favorite ? await deleteFavorite(storeId) : await addFavorite({ storeId });

    if (res === undefined || res.data.resultStatus !== 200) {
      // alert("찜 상태 변경 실패");
      const modal = new bootstrap.Modal(document.getElementById("faiF"));
      modal.show();
      return;
    }

    store.favorite = !store.favorite;
    store.favorites = store.favorite ? store.favorites + 1 : store.favorites - 1;

    // Pinia store에도 업데이트
    favoriteStore.toggleFavorite(storeId);
  }
};

// 더보기 버튼 함수
const visibleCount = ref(8);
const showMore = () => {
  visibleCount.value += 8;
};
const visibleCards = computed(() => {
  return state.favorites.slice(0, visibleCount.value);
});

// 위로 가기 버튼 함수
const arrow = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth',
  });
};

onMounted(() => {
  fetchFavorites();
});

watch(
  () => route.path,
  (path) => {
    if (path === "/favorites") {
      fetchFavorites();
    }
  }
);

const toStore = id => {
  router.push({ path: `/stores/${id}` });
};
</script>

<template>
  <div class="all-box">
    <div class="box">
      <div>
        <div class="text-top">내가 찜한 가게</div>
        <div class="top">
          <div class="solid"></div>
        </div>
      </div>

      <div v-if="state.favorites.length > 0">

        <div class="for">
          <div class="all" v-for="store in visibleCards" :key="store.storeId">
            <div id="imgBigBox" class="card h-100 shadow-sm">
              <div id="imgBox" class="card-img-top">
                <img class="sImg"
                  :src="store.imagePath !== null ? `/pic/store-profile/${store.storeId}/${store.imagePath}` : defaultImage" />
              </div>
              <!-- <img src="" class="card-img-top" alt="음식 이미지"> -->
              <div class="card-body">
                <h6 class="card-title">{{ store.name }}</h6>
                <div v-if="store.rating !== 'NaN'">
                  <img class="star" :src="ratingImage" />
                  <span class="small">
                    {{ store.rating }}&nbsp;({{ store.reviews }})&nbsp;&nbsp;
                    <img class="love" :src="store.favorite ? favoriteImage : noFavoriteImage"
                      @click="toggleFavorite(store)" />
                    {{ store.favorites }}
                  </span>
                </div>
                <div v-else>
                  <img class="star" :src="ratingImage" />
                  <span class="small">
                    0&nbsp;(0)&nbsp;&nbsp;
                    <img class="love" :src="store.favorite ? favoriteImage : noFavoriteImage"
                      @click="toggleFavorite(store)" />
                    {{ store.favorites }}
                  </span>
                </div>
                <p class="mb-1 text-muted small">배달비 0원 ~ 3000원</p>
                <p class="mb-2 text-muted small">최소 주문 금액 10,000원</p>
                <div class="d-flex justify-content-center align-items-center">
                  <button @click="toStore(store.storeId)" class="btn btn-outline-danger btn-sm">
                    자세히보기
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="btnBox" v-if="state.favorites.length > 0">
          <div id="btnB" v-if="visibleCount < state.favorites.length" @click="showMore">
            더보기
          </div>
        </div>

      </div>

      <div v-else>
        <div class="container">
          <div class="text-no">찜한 가게가 없습니다.</div>
          <div class="img-box">
            <img src="/src/imgs/owner/owner-service2.png" />
          </div>
        </div>
      </div>
    </div>
  </div>

  <img @click="arrow" class="arrow" src="/src/imgs/arrow.png" />

  <!-- 조회 실패 -->
  <div class="modal fade" id="favorite-error" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
    aria-hidden="true">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">경고</h5>
        </div>
        <div class="modal-body">조회에 실패하였습니다</div>
        <div class="modal-footer">
          <a class="btn" id="modalY" href="#" data-bs-dismiss="modal">닫기</a>
        </div>
      </div>
    </div>
  </div>

  <!-- 찜 실패 -->
  <div class="modal fade" id="faiF" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">알림</h5>
        </div>
        <div class="modal-body">찜 하기에 실패하였습니다</div>
        <div class="modal-footer">
          <a class="btn" id="modalY" href="#" data-bs-dismiss="modal">닫기</a>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@font-face {
  font-family: "BMJUA";
  src: url("https://fastly.jsdelivr.net/gh/projectnoonnu/noonfonts_one@1.0/BMJUA.woff") format("woff");
  font-weight: 600;
  font-style: normal;
}

.all-box {
  display: flex;
  justify-content: center;

  width: 100%;
}

.top {
  display: flex;
  justify-content: center;
}

.box {
  display: block;
  font-family: "BMJUA";
  font-size: 1.4em;
  letter-spacing: -1.5px;
  margin-top: 90px;
  margin-bottom: 120px;
  width: 1470px;

  .solid {
    width: 1470px;
    border: 1px #000 solid;
    margin-bottom: 70px;
  }
}

.for {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 75px;
  margin-bottom: 100px;
  letter-spacing: .015em;
}

#imgBox {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 300px;
  height: 251px;
  overflow: hidden;
}

.sImg {
  width: 310px;
}

#imgBigBox {
  border-radius: 15px !important;
}

.small {
  font-size: .7em;
}

.btn {
  width: 150px;
  background-color: #ff6666;
  color: #fff;
  border-radius: 6px;
}

.star {
  width: 20px;
  margin-right: 5px;
}

.love {
  width: 20px;
  cursor: pointer;
}

.btnBox {
  display: flex;
  justify-content: center;
}

#btnB {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  font-size: 40px;
  width: 60% !important;
  height: 50px;
  margin-left: -5px;
  margin-top: -20px;
  border: none;
}

.arrow {
  position: sticky;
  width: 3.8%;
  bottom: 100px;
  left: 93%;
  z-index: 999;
  margin-bottom: 100px;
}

.text-no {
  text-align: center;
  font-size: 30px;
  margin-top: 90px;
  color: #5f5f5f;
  margin-bottom: 20px;
}

.text-top {
  font-size: 25px;
  text-align: center;
  margin-bottom: 50px;
}
</style>
