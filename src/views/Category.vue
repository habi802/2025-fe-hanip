<script setup>
import { Swiper, SwiperSlide } from "swiper/vue";
import {
  Navigation,
  Pagination,
  Scrollbar,
  A11y,
  Autoplay,
} from "swiper/modules";

// Import Swiper styles
import "swiper/css";
import "swiper/css";
import "swiper/css/navigation";
import "swiper/css/pagination";
import "swiper/css/scrollbar";

//
import { getStoreList } from "@/services/storeService";
import { reactive, onMounted, nextTick, ref } from "vue";
import StoreList from "@/components/StoreList.vue";
import { useRouter, useRoute } from "vue-router";
import Review from "@/components/Review.vue";

const router = useRouter();
const route = useRoute();
const state = reactive({
  stores: [],
});

// 쿼리에서 검색어 추출
const query = ref(route.query.search_text || "");
const result = ref([]);

const searchText = async (search_text) => {
  console.log("searchText 호출, q:", search_text);
  try {
    const params = { search_text };
    console.log("api 파라미터:", params);
    const res = await getStoreList(params);
    console.log("api:", res.data.resultData);
    result.value = res.data.resultData;
    state.stores = result.value;
    console.log("state.stores: ", state.stores);
  } catch (error) {
    console.error("에러 발생:", error);
    result.value = [];
  }
};

const name = reactive({
  text: "전체",
});

// 라우터 필터용
// onMounted(() => {
//   if (query.value) searchText(query.value)
// })

// 카테고리 검색창 용
//

onMounted(() => {
  nextTick(() => {
    if (query.value) {
      searchText(query.value);
      name.text = query.value;
    }
    const text = router.currentRoute.value.query.section;
    switch (text) {
      case "korean":
        searchKoreanFood();
        break;
      case "china":
        searchChina();
        break;
      case "japan":
        searchJapanese();
        break;
      case "pasta":
        searchWesternFood();
        break;
      case "cafe":
        searchDessert();
        break;
      case "snack":
        searchSnackFood();
        break;
      case "fast":
        searchFastFood();
        break;
      case "asian":
        searchAsian();
        break;
      case "chicken":
        searchChick();
        break;
      case "pizza":
        searchPizza();
        break;
      case "night":
        searchLateNight();
        break;
      default:
        if (query.value) {
          return;
        }
        findAll({});
    }
  });
});

// 카테고리 필터
// 전체 찾기
const searchAll = () => {
  findAll();
  name.text = "전체";
};
// 한식 찾기
const searchKoreanFood = () => {
  const searchCategory = {
    category: "한식",
  };
  name.text = "한식";
  findAll(searchCategory);
};
// 중식 찾기
const searchChina = () => {
  const searchCategory = {
    category: "중식",
  };
  name.text = "중식";
  findAll(searchCategory);
};
// 일식 찾기
const searchJapanese = () => {
  const searchCategory = {
    category: "일식",
  };
  name.text = "일식";
  findAll(searchCategory);
};
// 양식 찾기
const searchWesternFood = () => {
  const searchCategory = {
    category: "양식",
  };
  name.text = "양식";
  findAll(searchCategory);
};
// 디저트 찾기
const searchDessert = () => {
  const searchCategory = {
    category: "카페",
  };
  name.text = "카페/디저트";
  findAll(searchCategory);
};
// 분식 찾기
const searchSnackFood = () => {
  const searchCategory = {
    category: "분식",
  };
  name.text = "분식";
  findAll(searchCategory);
};
// 패스트푸드 찾기
const searchFastFood = () => {
  const searchCategory = {
    category: "패스트푸드",
  };
  name.text = "패스트푸드";
  findAll(searchCategory);
};
// 아시안 푸드 찾기
const searchAsian = () => {
  const searchCategory = {
    category: "아시안",
  };
  name.text = "아시안";
  findAll(searchCategory);
};
// 치킨 찾기
const searchChick = () => {
  const searchCategory = {
    category: "치킨",
  };
  name.text = "치킨";
  findAll(searchCategory);
};
// 피자 찾기
const searchPizza = () => {
  const searchCategory = {
    category: "피자",
  };
  name.text = "피자";
  findAll(searchCategory);
};
// 야식 찾기
const searchLateNight = () => {
  const searchCategory = {
    category: "야식",
  };
  name.text = "야식";
  findAll(searchCategory);
};

const findAll = async (params) => {
  const res = await getStoreList(params);
  state.stores = res.data.resultData;
};

const arrow = () => {
  window.scrollTo({
    top: 0,
    behavior: "smooth",
  });
};

// 검색
const search = ref("");

const caLink = async () => {
  console.log("검색: ", search.value);
  const res = await getStoreList({ searchText: search.value });
  console.log("res: ", res.data.resultData);
  state.stores = res.data.resultData;
  name.text = search.value;
};
</script>

<template>
  <!-- <div class="text">{{ name.text }}</div> -->
  <div class="categorys">
    <div class="categorySwipe">
      <div class="swiperLeft">
        <img class="left" src="/src/imgs/NavigationLeft.png" />
      </div>
      <swiper :navigation="{
        nextEl: '.swiperRight',
        prevEl: '.swiperLeft',
      }" :slides-per-view="6" :modules="[Navigation, Pagination, Scrollbar, A11y, Autoplay]" :speed="100"
        :loop="true">
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchAll" id="cImg" src="/src/imgs/allImg.png" alt="allImg" />
          </div>
          <div id="cName">전체</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchKoreanFood" id="cImg" src="/src/imgs/koreanfood.png" alt="koreanImg" />
          </div>
          <div id="cName">한식</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchChina" id="cImg" src="/src/imgs/jjajangmyeon.png" alt="ChinaImg" />
          </div>
          <div id="cName">중식</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchJapanese" id="cImg" src="/src/imgs/porkcutlet.png" alt="japanese" />
          </div>
          <div id="cName">일식</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchWesternFood" id="cImg" src="/src/imgs/pasta.png" alt="westernFood" />
          </div>
          <div id="cName">양식</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchDessert" id="cImg" src="/src/imgs/dessert.png" alt="dessert" />
          </div>
          <div id="cName">카페/디저트</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchSnackFood" id="cImg" src="/src/imgs/tteokbokki.png" alt="snackFood" />
          </div>
          <div id="cName">분식</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchFastFood" id="cImg" src="/src/imgs/hamburger.png" alt="fastFood" />
          </div>
          <div id="cName">패스트푸드</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchAsian" id="cImg" src="/src/imgs/nd.png" alt="asian" />
          </div>
          <div id="cName">아시안</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchChick" id="cImg" src="/src/imgs/chicken.png" alt="chick" />
          </div>
          <div id="cName">치킨</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchPizza" id="cImg" src="/src/imgs/pizza.png" alt="pizza" />
          </div>
          <div id="cName">피자</div>
        </swiper-slide>
        <swiper-slide>
          <div class="imgBox">
            <img @click="searchLateNight" id="cImg" src="/src/imgs/pigfeet.png" alt="lateNight" />
          </div>
          <div id="cName">야식</div>
        </swiper-slide>
      </swiper>
      <div class="swiperRight">
        <img class="right" src="/src/imgs/NavigationRight.png" />
      </div>
    </div>
  </div>
  <div class="searchBar">
    <input v-model="search" @keyup.enter="caLink" type="text" id="title" class="searchBox"
      placeholder="찾는 맛집 이름,메뉴가 무엇인가요?" />
    <img @click="caLink" class="searchImg" src="/src//imgs/fluent_search.png" />
  </div>
  <div class="guideBox">
    <!-- root : 검색 결과가 없을 시 나타내는 이미지 일단 임시로-->
    <div class="position-relative" v-if="state.stores.length === 0">
      <img src="/src/imgs/owner/owner-service2.png" style="position: absolute; transform: translateX(540px)" />
    </div>
    <div v-for="stores in state.stores" :key="stores.storeId">
      <StoreList :stores="stores" />
    </div>
    <!--  -->
    <!--  -->
    <!-- <div class="store">
      <div class="storeImgBox">
        <img class="sImg" src="/src/imgs/recStore_1.png" />
      </div>
      <div class="storeTextBox">
        <div class="sTextBox">
          <div class="sText">오십계</div>
          <div id="smallText">배달료 0원 ~ 3000원</div>
          <div id="smallText">최소 주문 금액 13000원</div>
        </div>
        <div class="icons">
          <div class="star">
            <img id="icon" src="/src/imgs/star.png" />
            4.8
            <span class="starNum">(938)</span>
          </div>
          <div class="love">
            <img id="icon" src="/src/imgs/love.png" />
            927
          </div>
        </div>
        <div class="btn">자세히보기</div>
      </div>
    </div> -->
  </div>
  <div class="footer"></div>
  <img @click="arrow" class="arrow" src="/src/imgs/arrow.png" />
</template>

<style lang="scss" scoped>
@font-face {
  font-family: "BMJUA";
  src: url("https://fastly.jsdelivr.net/gh/projectnoonnu/noonfonts_one@1.0/BMJUA.woff") format("woff");
  font-weight: normal;
  font-style: normal;
}

* {
  font-family: "BMJUA";
  letter-spacing: 1px;
}

.text {
  text-align: center;
  margin-top: 70px;
  font-size: 2.5em;
  color: #ff6666;
}

.categorys {
  display: flex;
  justify-content: center;
  margin-top: 60px;
  text-align: center;

  .categorySwipe {
    display: flex;
    justify-content: center;
    width: 1200px;

    .swiper-slide {
      width: 125px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    .imgBox {
      width: 125px;
      border-radius: 50%;
      overflow: hidden;
    }

    #cImg {
      cursor: pointer;
      // width: 220px;
      width: 125px;
      object-fit: cover; // 이미지가 비율 유지
      border-radius: 50%;
      transition: transform 0.3s ease-in-out;

      &:hover {
        transform: scale(1.2); // 확대비율
      }
    }

    // 카테고리 이름
    #cName {
      font-size: 24px;
      padding-top: 10px;
      margin-left: -5px;
      font-weight: 400;
    }

    .left {
      width: 17px;
      height: 28px;
      margin-top: 50px;
      margin-right: 50px;
      border-radius: 5px;
    }

    .right {
      width: 17px;
      height: 28px;
      margin-top: 50px;
      margin-left: 50px;
      border-radius: 5px;
    }
  }
}

.line {
  width: 1280px;
  margin: 0 auto;
  margin-top: 30px;
}

.guideBox {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  gap: 80px 10px;
  width: 1480px;
  height: 100%;
  margin: 0 auto;
  margin-top: 100px;
  background-color: #fff;
}

.footer {
  margin-bottom: 300px;
}

.imgBox {
  width: 125px;
  border-radius: 50%;
  overflow: hidden;
}

.arrow {
  position: sticky;
  width: 3.8%;
  bottom: 100px;
  left: 93%;
  z-index: 999;
  margin-bottom: 100px;

  &:hover {
    opacity: 80%;
  }
}

// 반응형
@media (max-width: 650px) {
  .searchBar {
    display: none;
  }
}

// 검색
.searchBar {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 50px auto 0;
  padding: 0 20px;
  width: 600px;
  height: 70px;
  border: 3px solid #ff6666;
  border-radius: 50px;
  position: relative;
  background-color: white;

  input {
    padding-left: 43px;
  }

  input:focus {
    outline: none;
    box-shadow: none;
  }

  .searchImg {
    width: 30px;
    position: relative;
    right: 35px;
    bottom: 2px;
    cursor: pointer;
  }

  .searchBox {
    flex: 1;
    height: 100%;
    border: none;
    border-radius: 50px;
    padding-left: 20px;
    font-size: 1em;
    color: #333;
  }

  .searchBox::placeholder {
    color: #fcaeae;
  }
}

.searchBar::placeholder {
  color: #ff6666;
}

.searchBar::-moz-focus-inner {
  outline: none;
  box-shadow: none;
}
</style>
