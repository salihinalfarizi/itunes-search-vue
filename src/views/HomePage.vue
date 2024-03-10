<template>
  <ion-page>
    <ion-header v-if="isShowSearchResult" class="ion-no-border">
      <ion-toolbar>
        <div class="header-search">
          <ion-icon class="def-icon" :icon="menuOutline" />
          <ion-icon class="ngmusic" :icon="'/assets/icons/ngmusic.svg'" />
          <ion-icon class="def-icon" :icon="searchOutline" />
        </div>
      </ion-toolbar>
    </ion-header>
    <!-- home screen -->
    <ion-content v-if="!isShowSearchResult" :fullscreen="true">
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
      :class="{ 'search-result-background': isShowSearchResult }"
    >
      <div class="search-text-result">
        <ion-text class="search-res">{{ `Search result for :` }} </ion-text>
        <ion-text class="search-key">{{ searchTerm }}</ion-text>
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
} from "@ionic/vue";
import { computed, defineComponent, ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import CardList from "@/components/CardList.vue";
import { searchOutline, menuOutline } from "ionicons/icons";

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
    CardList
  },
  setup() {
    const router = useRouter();
    const loading = ref(false);
    const isShowSearchResult = ref(false);
    const searchTerm = ref("");
    const searchResults = ref([]);
    const offset = ref(0);
    const limit = ref(15);
    const updateKey = (event) => {
      searchTerm.value = event.detail.value;
    };
    const setLoading = (val) => {
      loading.value = val;
    };

    const showLoadMore = computed(() => {
      return searchResults.value.length % limit.value === 0;
    });

    const search = async () => {
      offset.value = 0;
      isShowSearchResult.value = true;
      setLoading(true);
      await loadData();
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
      } catch (error) {
        console.error("Error fetching data:", error);
      }
      setLoading(false);
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
      searchTerm,
      searchResults,
      offset,
      limit,
      showLoadMore,
      search,
      loadMore,
      loadData,
      goTo,
      updateKey,
    };
  },
});
</script>

<style lang="scss" scoped>
ion-content::part(background) {
  background: linear-gradient(to bottom, #712bda, #a45deb);
}
.search-result-background::part(background) {
  background: #f8fafc;
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

.text-center {
  text-align: center;
}

.logo {
  height: 84px;
  width: 84px;
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
}
.btn-home-search {
  --background-activated: #ffffff3a;
  --background: #ffffff3a;
  --color: #ffffff;
  margin-top: 12px;
  margin-bottom: 12px;
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
ion-header {
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
.def-icon {
  height: 20px;
  width: 20px;
}
</style>
