<template>
  <ion-page>
    <ion-header
      v-if="isShowSearchResult"
      class="ion-no-border"
      :class="{ 'home-header': isShowSearchResult }"
    >
      <ion-toolbar>
        <div class="header-search">
          <ion-icon @click="resetToHomeScreen()" class="def-icon" :icon="menuOutline" />
          <ion-icon class="ngmusic" :icon="'/assets/icons/ngmusic.svg'" />
          <ion-icon
            @click="setOpenUpdateSearch(loading ? false : true)"
            class="def-icon"
            :icon="searchOutline"
          />
        </div>
      </ion-toolbar>
    </ion-header>
    <!-- home screen -->
    <ion-content
      v-if="!isShowSearchResult"
      class="ion-padding"
      :fullscreen="true"
      :class="{ 'bg-home': !isShowSearchResult }"
    >
      <div class="home-background">
        <div id="container">
          <ion-icon class="logo" :icon="'/assets/icons/logo.svg'" />
        </div>
      </div>
    </ion-content>
    <!-- search result screen -->
    <ion-content
      v-else
      :fullscreen="true"
      class="ion-padding"
      :class="{ 'search-result-background': isShowSearchResult }"
    >
      <div v-if="searchResults.length > 0"" class="search-text-result">
        <ion-text class="search-res">{{ `Search result for :` }} </ion-text>
        <ion-text class="search-key">{{ savedSearchTerm }}</ion-text>
      </div>
      <div v-if="loading && searchResults.length === 0" class="center">
        <ion-spinner></ion-spinner>
      </div>
      <card-list
        v-else
        :items="searchResults"
        :showLoadMoreButton="showLoadMore"
        :loadMore="loadMore"
        :searchKey="searchTerm"
        :loading="loading"
      ></card-list>

      <!-- search update modal -->
      <ion-modal :is-open="isOpenUpdateSearch" @didDismiss="setOpenUpdateSearch(false)" :cssClass="'bg-modal'">
        <ion-header
          class="ion-no-border"
          :class="{ 'bg-transparent': isOpenUpdateSearch }"
        >
          <ion-toolbar>
            <ion-buttons slot="end">
              <ion-button @click="setOpenUpdateSearch(false)">
                <ion-icon class="def-icon" :icon="closeOutline" />
              </ion-button>
            </ion-buttons>
          </ion-toolbar>
        </ion-header>
        <ion-content :fullscreen="true" class="ion-padding">
          <div class="search-modal">
            <ion-text>Search</ion-text>
          </div>
          <div class="foot">
            <ion-input
              class="search-input"
              :clear-input="true"
              placeholder="Search by Artist / Album / Title"
              v-model="searchTerm"
              @ionChange="updateKey($event)"
            >
            </ion-input>
            <ion-button
              @click="search()"
              class="btn-modal-search"
              expand="full"
              shape="round"
            >
              Search
            </ion-button>
          </div>
        </ion-content>
      </ion-modal>
    </ion-content>
    <ion-footer v-if="!isShowSearchResult" class="ion-no-border">
      <div class="foot">
        <ion-input
          class="search-input"
          :clear-input="true"
          placeholder="Search by Artist / Album / Title"
          v-model="searchTerm"
          @ionChange="updateKey($event)"
        >
        </ion-input>
        <ion-button
          @click="search()"
          class="btn-home-search"
          expand="full"
          shape="round"
        >
          Search
        </ion-button>
      </div>
    </ion-footer>
    <ion-toast
      :is-open="isOpenToastInfo"
      message="Search key can not be empty !"
      :duration="5000"
      color="danger"
      position="top"
      @didDismiss="setOpenToast(false)"
    ></ion-toast>
  </ion-page>
</template>

<script>
import {
  IonPage,
  IonContent,
  IonHeader,
  IonFooter,
  IonToolbar,
  IonButton,
  IonIcon,
  IonSpinner,
  IonInput,
  IonModal,
  IonToast,
} from "@ionic/vue";
import { computed, defineComponent, ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import CardList from "@/components/CardList.vue";
import { searchOutline, menuOutline, closeOutline } from "ionicons/icons";

export default defineComponent({
  components: {
    IonPage,
    IonContent,
    IonHeader,
    IonFooter,
    IonToolbar,
    IonButton,
    IonIcon,
    IonInput,
    IonSpinner,
    IonModal,
    IonToast,
    CardList,
  },
  setup() {
    const router = useRouter();
    const loading = ref(false);
    const isShowSearchResult = ref(false);
    const searchTerm = ref("");
    const savedSearchTerm = ref("")
    const searchResults = ref([]);
    const offset = ref(0);
    const limit = ref(15);
    const updateKey = (event) => {
      searchTerm.value = event.detail.value;
    };
    const setLoading = (val) => {
      loading.value = val;
    };

    const isOpenUpdateSearch = ref(false);
    const isOpenToastInfo = ref(false);

    const setOpenUpdateSearch = (val) => (isOpenUpdateSearch.value = val);
    const setOpenToast = (val) => (isOpenToastInfo.value = val);

    const showLoadMore = computed(() => {
      return searchResults.value.length % limit.value === 0;
    });

    const search = async () => {
      if (searchTerm.value.length < 1) {
        setOpenToast(true);
      } else {
        offset.value = 0;
        isShowSearchResult.value = true;
        setLoading(true);
        setOpenUpdateSearch(false);
        await loadData();
      }
    };
    const loadMore = async () => {
      offset.value += limit.value;
      await loadData();
    };
    const loadData = async () => {
      try {
        const response = await axios.get("https://itunes.apple.com/search", {
          params: {
            term: searchTerm.value,
            limit: limit.value,
            offset: offset.value,
          },
        });
        if (offset.value === 0) {
          searchResults.value = response.data.results;
        } else {
          searchResults.value = [
            ...searchResults.value,
            ...response.data.results,
          ];
        }
        savedSearchTerm.value = searchTerm.value
      } catch (error) {
        console.error("Error fetching data:", error);
      }
      setLoading(false);
    };
    const resetToHomeScreen = () => {
      isShowSearchResult.value = false;
      searchTerm.value = '';
      savedSearchTerm.value = '';
      searchResults.value = [];

    };
    const goTo = (path) => {
      router.push(path);
    };
    return {
      isShowSearchResult,
      loading,
      setLoading,
      searchOutline,
      menuOutline,
      closeOutline,
      searchTerm,
      savedSearchTerm,
      searchResults,
      offset,
      limit,
      showLoadMore,
      search,
      loadMore,
      loadData,
      goTo,
      updateKey,
      resetToHomeScreen,
      isOpenUpdateSearch,
      setOpenUpdateSearch,
      isOpenToastInfo,
      setOpenToast,
    };
  },
});
</script>

<style lang="scss" scoped>
.bg-home {
  --background: linear-gradient(to bottom, #712bda, #a45deb);
}
.search-result-background::part(background) {
  --background: #f8fafc;
}

.bg-modal {
  --ion-background-color: rgba(0, 0, 0, 0.4);
}

#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  color: #8c8c8c;
  margin: 0;
}

#container a {
  text-decoration: none;
}

.search-input {
  --background-activated: #8a8a8a94;
  --background: #ffffff;
  --color: black;
  --padding-bottom: 10px;
  --padding-top: 10px;
  --padding-start: 20px;
  --padding-end: 20px;
  --border-radius: 25px;
  --border-color: transparent;
  text-align: center;
  min-height: 48px;
}
.btn-home-search {
  --background-activated: #ffffff3a;
  --background: #ffffff3a;
  --color: #ffffff;
  margin-top: 12px;
  margin-bottom: 12px;
  text-transform: none;
  min-height: 48px;
}

.btn-modal-search {
  --background-activated: #a45deb;
  --background: linear-gradient(to right, #712bda, #a45deb);
  --color: #ffffff;
  margin-top: 12px;
  margin-bottom: 12px;
  text-transform: none;
  min-height: 48px;
}
.search-modal {
  font-size: 20px;
  font-weight: bold;
  width: 100%;
  margin-top: 30vh;
  margin-bottom: 24px;
  text-align: center;
}
.center {
  margin-top: 30vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
ion-spinner {
  height: 32px;
  width: 32px;
  --color: #000000;
}
ion-footer {
  --background: transparent;
  padding: 0px 12px 0px 12px;
}
.foot {
  --background: transparent;
}
.ngmusic {
  width: 80px;
  height: 20px;
}
.header-search {
  display: flex;
  flex-direction: row;
  justify-items: center;
  justify-content: space-between;
  align-items: center;
  background: transparent;
}
ion-toolbar {
  --background: transparent;
}
.home-header {
  margin: 0 0 39px;
  padding: 18px 15px 22.2px;
  box-shadow: 0 0 6px 0 rgba(148, 77, 230, 0.75);
  background: linear-gradient(to right, #712bda, #a45deb);
  border-bottom-left-radius: 32%;
  border-bottom-right-radius: 32%;
}
ion-icon {
  --color: white;
}
.search-text-result {
  display: flex;
  flex-direction: row;
  align-content: center;
  justify-content: center;
  justify-items: center;
  align-items: center;
  margin-bottom: 32px;
  .search-res {
    font-size: 14px;
    font-weight: normal;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: 0.5px;
    color: #334155;
  }
  .search-key {
    margin-left: 10px;
    font-size: 18px;
    font-weight: bold;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: 0.64px;
    color: #7b34dd;
  }
}

</style>
