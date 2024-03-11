<template>
  <div v-if="items.length === 0" class="d-flex flex-column container">
    <div class="full-row centered">
      <img class="not-found" src="/assets/icons/not-found.png" />
    </div>
    
      <ion-text class="text-black">{{
      `Sorry, "${searchKey}" is not found!!`
    }}</ion-text>

  </div>

  <ion-list v-else class="bg-transparent">
    <ion-item
      v-for="item in items"
      :key="item.trackId"
      lines="none"
      class="bg-transparent"
      :details="false"
      @click="setOpenItemsDetail(true, item)"
    >
      <div class="card d-flex flex-row">
        <div class="image-container">
          <img class="card-image" :src="item.artworkUrl100" />
          <ion-icon class="play-icon" :icon="playCircleOutline" />
        </div>
        <div class="d-flex flex-column space-between full-row">
          <div class="d-flex flex-column full-row">
            <ion-text class="singer">{{ item.artistName }}</ion-text>
            <ion-label class="song-text">{{
              item.trackName ? item.trackName : "-"
            }}</ion-label>
          </div>

          <div class="d-flex flex-row space-between">
            <div class="genre">{{ item.primaryGenreName }}</div>
            <div class="d-flex flex-row price-part">
              <div class="usd">$</div>
              <ion-text class="price-info">{{
                item.trackPrice ? item.trackPrice : "-"
              }}</ion-text>
            </div>
          </div>
        </div>
      </div>
      <!-- items detail -->

      <ion-modal
        :cssClass="'bg-items-detail'"
        :is-open="isOpenItemsDetail"
        @didDismiss="setOpenItemsDetail(false, {})"
      >
        <items-detail
          :itemData="selectedItem"
          @closeItemsDetail="setOpenItemsDetail(false, {})"
        />
      </ion-modal>
    </ion-item>
    <ion-item
      v-if="showLoadMoreButton && items.length > 0"
      lines="none"
      class="bg-transparent"
    >
      <div class="d-flex flex-row item-load-more">
        <div class="load-more" @click="loadMoreData">
          <ion-text v-if="!isLoadMoreData">Load More</ion-text>
          <ion-spinner v-else name="dots"></ion-spinner>
        </div>
      </div>
    </ion-item>
  </ion-list>
</template>

<script>
import { defineComponent, ref } from "vue";
import { logoUsd, playCircleOutline } from "ionicons/icons";
import ItemsDetail from "../views/ItemsDetail.vue";
export default defineComponent({
  props: {
    items: Array,
    loading: Boolean,
    showLoadMoreButton: Boolean,
    loadMore: Function,
    searchKey: String,
  },
  components: { ItemsDetail },
  setup(props) {
    const isLoadMoreData = ref(false);
    const isOpenItemsDetail = ref(false);
    const selectedItem = ref({});
    const setOpenItemsDetail = (val, itemDetail) => {
      isOpenItemsDetail.value = val;
      selectedItem.value = itemDetail;
    };
    const loadMoreData = async () => {
      isLoadMoreData.value = true;
      await props.loadMore();
      isLoadMoreData.value = false;
    };
    return {
      logoUsd,
      playCircleOutline,
      isLoadMoreData,
      loadMoreData,
      selectedItem,
      isOpenItemsDetail,
      setOpenItemsDetail,
    };
  },
});
</script>

<style scoped>

.bg-items-detail {
  --ion-background-color: #f8fafc;
}
.not-found {
  height: 256px;
  width: 256px;
  margin-top: 15vh;
  margin-bottom: 16px;
}

ion-item {
  --background: transparent;
}
.card {
  width: 100%;
  height: 120px;
  margin: 12px 0px 12px 0px;
  padding: 12px;
  border-radius: 10px;
  box-shadow: 0 4px 6px -1px rgba(255, 255, 255, 0.1),
    0 2px 4px -1px rgba(0, 0, 0, 0.06);
  background-color: #fff;
}

.image-container {
  height: 96px;
  width: 96px;
  min-width: 96px;
  max-width: 96px;
  border-radius: 10%;
  margin-right: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.card-image {
  height: 96px;
  width: 96px;
  border-radius: 10px;
  object-fit: cover;
  display: block;
}
.play-icon {
  position: absolute;
  height: 30px;
  width: 30px;
  font-size: 3rem; /* Adjust the size as needed */
  color: #ffffff;
  z-index: 999;
}
.singer {
  font-size: 12px;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.5px;
  color: #334155;
}
.song-text {
  margin-top: 5px;
  font-size: 14px;
  font-weight: bold;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.5px;
  color: #334155;
}

.full-row {
  width: 100%;
}
.item-load-more {
  width: 100%;
  justify-content: center;
  padding-bottom: 12px;
}
.load-more {
  width: fit-content;
  height: 34px;
  padding: 8px 20px;
  border-radius: 17px;
  background-color: #e2e8f0;
  color: #3d5569;
  margin-bottom: 8px;
}
.genre {
  width: fit-content;
  height: 20px;
  margin: 43px 0px 0 0px;
  padding: 5px 13px 4px;
  border-radius: 10px;
  background-color: #10b981;
  font-size: 10px;
  font-weight: 500;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.36px;
  margin-top: 1px;
}
.price-part {
  justify-content: right;
  align-items: center;
}
.price-info {
  margin: 1px 0 1px 6px;
  font-family: Roboto;
  font-size: 12px;
  font-weight: bold;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.43px;
  color: #f5b014;
}
.usd {
  height: 18px;
  width: 18px;
  border: solid 1px #f5b014;
  border-radius: 100%;
  color: #f5b014;
  font-size: 13px;
  font-weight: bold;
  text-align: center;
  padding-top: 1px;
}
ion-spinner {
  margin-top: -3px;
  --color: #712bda;
}

</style>
