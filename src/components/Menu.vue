<script setup>
import { addItem } from '@/services/cartService';
import { useAccountStore } from '@/stores/account';
import defaultImage from '@/imgs/owner/owner-service3.png';
import { computed } from 'vue';

const account = useAccountStore();
const emit = defineEmits(['addCart'])

const props = defineProps({
    item: {
        menuId: Number,
        name: String,
        comment: String,
        price: Number,
        imagePath: String
    }
});

//메뉴 이미지

// const menuImg = `/pic/menu-profile/${props.item.id}/${props.item?.imagePath}`

// const imgSrc = computed(() => {
//   return state.store && state.storeInfo[0]?.imagePath && state.storeInfo[0]?.imagePath !== 'null'
//   ? `/pic/store-profile/${state.store.id}/${state.storeInfo[0]?.imagePath}`
//   : defaultImage;
// })



const menuSrc = computed(() => {
    return props.item && props.item?.imagePath && props.item?.imagePath !== 'null'
        ? `/pic/menu-profile/${props.item.menuId}/${props.item?.imagePath}`
        : defaultImage;
})

const addCart = async () => {
    if (!account.state.loggedIn) {
        showModal('로그인 후 주문 가능합니다')
        return;
    }
    const params = {
        menuId: props.item.menuId,
        quantity: 1
    }
    const res = await addItem(params);

    if (res === undefined) {
        showModal('등록 실패')
        return;
    } else if (res.status === 500) {
        emit('addCart', props.item);
    } else {
        props.item.id = res.data.resultData;
        emit('addCart', props.item);
    }
};
console.log("props.img", props.item);
// 모달창 함수
const showModal = (message, onCloseCallback) => {
  const modalBody = document.getElementById("alertModalBody");
  if (modalBody) modalBody.textContent = message;
  const modal = new bootstrap.Modal(document.getElementById("alertModal"));
  modal._element.addEventListener('hidden.bs.modal', () => {
    if (typeof onCloseCallback === 'function') {
      onCloseCallback();  
    }
  });
  modal.show();

};

</script>

<template>
    <div class="menu border rounded-4 p-3 mb-2" @click="addCart()">
        <div class="row">
            <div class="col-8 col-md-10">
                <h5>{{ props.item.name }}</h5>
                <p class="comment">{{ props.item.comment }}</p>
                <div class="bottom-box">
                    <span>{{ props.item.price.toLocaleString() }}원</span>
                    <span class="check">
                        <!-- 0이 되면 스타일 바뀌게 해야함 -->
                        <!-- <div class="check-box"> - </div> -->
                        <!-- 갯수 -->
                        <!-- <div> 0 </div> -->
                        <!-- <div class="check-box"> + </div> -->
                    </span>
                </div>
            </div>
            <div id="menuImgs" class="col-4 col-md-2 border rounded">
                <img class="menuImgBox" :src="menuSrc" @error="e => e.target.src = defaultImage" />
            </div>
        </div>
    </div>
    <!-- 공통 알림 모달 -->
    <div class="modal fade" id="alertModal" tabindex="-1" role="dialog" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">알림</h5>
                </div>
                <div class="modal-body" id="alertModalBody">내용</div>
                <div class="modal-footer">
                    <button type="button" class="btn " data-bs-dismiss="modal">
                        확인
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="alertModal" tabindex="-1" role="dialog" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">알림</h5>
                </div>
                <div class="modal-body" id="alertModalBody">내용</div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-primary" data-bs-dismiss="modal">
                        확인
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
@font-face {
    // 프리텐다드
    font-family: 'Pretendard-Regular';
    src: url('https://fastly.jsdelivr.net/gh/Project-Noonnu/noonfonts_2107@1.1/Pretendard-Regular.woff') format('woff');
    font-weight: 400;
    font-style: normal;
}

* {
    font-family: 'Pretendard-Regular';
    font-size: 20px;
    font-size: 800;
}

.menu {
    cursor: pointer;

    &:hover {
        border-color: #fcaeae !important;
    }
}

#menuImgs {
    margin-left: -15px;
    margin-top: 3px;
    display: flex;
    width: 170px;
    height: 104px;
    overflow: hidden;
    justify-content: center;
    align-items: center;

    .menuImgBox {
        width: 170px;
    }
}

.bottom-box {
    display: flex;
    justify-content: space-between;
}

.check {
    display: flex;
    padding-right: 30px;
    gap: 10px;
}

.check-box {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 25px;
    height: 25px;
    border: #000 2px solid;
    font-size: 23px;
    border-radius: 5px;
}

.row {
    display: flex;
    justify-content: center;

}

.col-8 {
    width: 80%;
    margin-left: -15px;
}

.comment {
    font-size: 18px;
    margin-top: 10px;
    width: 600px;
    color: #9E9E9E;
    word-wrap: break-word;
    white-space: pre-wrap;
    line-height: 1.6;
    text-align: left;
}

.modal {
    top: 40%;
    font-family: 'Pretendard-Regular';
    font-weight: 800;
}
</style>
