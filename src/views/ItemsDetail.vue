<template>
  <ion-page>
    <ion-header class="ion-no-border bg-transparent">
      <ion-toolbar class="bg-transparent">
        <div slot="start" class="back-icon" @click="closeItemsDetail">
          <ion-icon :icon="chevronBackOutline"> </ion-icon>
        </div>
        <div slot="end" class="price-part">
          <div class="usd">$</div>
          <ion-text class="price-info">{{
            itemData.trackPrice ? itemData.trackPrice : "-"
          }}</ion-text>
        </div>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding" :fullscreen="true">
      <div class="content">
        <div class="image-container">
          <img class="image-item" :src="itemData.artworkUrl100" />
        </div>
        <ion-text class="track-name">{{
          itemData.trackName ? itemData.trackName : "-"
        }}</ion-text>
        <ion-text
          >{{ itemData.artistName ? itemData.artistName : "-" }} -
          {{ itemData.country ? itemData.country : "-" }}</ion-text
        >
        <ion-text
          >Release date:
          {{
            itemData.releaseDate ? setReleaseDate(itemData.releaseDate) : "-"
          }}</ion-text
        >
        <div class="genre">{{ itemData.primaryGenreName }}</div>

        <div>
          <audio ref="audioElement" :src="itemData.previewUrl"></audio>

          <div class="play-action">
            <ion-icon
              class="play-icon"
              @click="pauseAudio"
              :icon="pauseCircleOutline"
            ></ion-icon>

            <ion-icon
              class="play-icon"
              @click="playAudio"
              :icon="playCircleOutline"
            ></ion-icon>

            <ion-icon
              class="play-icon"
              @click="stopAudio"
              :icon="stopCircleOutline"
            ></ion-icon>
          </div>
        </div>
        <a
          v-if="itemData.artistViewUrl"
          :href="getArtistGallery(itemData.artistViewUrl)"
          target="_blank"
          rel="noopener noreferrer"
          >{{ `Explore more for all content from ${itemData.artistName} ` }}</a
        >
      </div>
    </ion-content>
  </ion-page>
</template>
<script>
import {
  IonPage,
  IonContent,
  IonHeader,
  IonToolbar,
  IonIcon,
} from "@ionic/vue";
import dayjs from "dayjs";
import {
  chevronBackOutline,
  playCircleOutline,
  pauseCircleOutline,
  stopCircleOutline,
} from "ionicons/icons";
import { defineComponent } from "vue";

export default defineComponent({
  props: {
    itemData: Object,
  },
  components: {
    IonPage,
    IonContent,
    IonHeader,
    IonToolbar,
    IonIcon
  },
  setup(_, { emit }) {
    const goBack = () => {
      emit("closeItemsDetail");
    };
    const setReleaseDate = (date) => {
      const setDate = dayjs(date);
      return setDate.format("YYYY-MM-DD");
    };

    const getArtistGallery = (urlString) => {
      const url = new URL(urlString);

      const baseURL = url.origin + url.pathname;
      return baseURL;
    };
    return {
      chevronBackOutline,
      playCircleOutline,
      pauseCircleOutline,
      stopCircleOutline,
      goBack,
      setReleaseDate,
      getArtistGallery,
    };
  },

  methods: {
    closeItemsDetail() {
      this.stopAudio();
      this.goBack();
    },
    playAudio() {
      this.$refs.audioElement.play();
    },
    pauseAudio() {
      this.$refs.audioElement.pause();
    },
    stopAudio() {
      this.$refs.audioElement.pause();
      this.$refs.audioElement.currentTime = 0;
    },
  },
});
</script>

<style lang="scss" scoped>
.bg-transparent {
  --background: transparent;
}
ion-text {
  color: black;
}
.back-icon {
  height: 32px;
  width: 32px;
  font-size: 22px;
  color: black;
  border-radius: 100%;
  padding-top: 4px;
  text-align: center;
}
.image-container {
  margin-top: 0px;
  margin-bottom: 32px;
  height: 90vw;
  width: 100%;
  min-width: 90vw;
  max-height: 100%;
  border-radius: 4%;
  margin-right: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.image-item {
  height: 90vw;
  width: 100%;
  border-radius: 16px;
  object-fit: cover;
  display: block;
}
.content {
  display: flex;
  flex-direction: column;
  --height: 100vh;
  --width: 100vw;
}
.track-name {
  font-size: 22px;
  font-weight: bold;
  margin-bottom: 4px;
}
.play-action {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  align-content: center;
  align-items: center;
  color: black;
  width: 100%;
  height: 64px;
  border-radius: 10px;
  margin-top: 32px;
  margin-bottom: 32px;
  background: #000000d8;
}

.play-icon {
  color: rgb(255, 255, 255);
  height: 42px;
  width: 42px;
}

.price-part {
  display: flex;
  flex-direction: row;
  justify-content: right;
  align-items: center;
}
.price-info {
  margin: 1px 0 1px 6px;
  font-family: Roboto;
  font-size: 18px;
  font-weight: bold;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.43px;
  color: #f5b014;
}
.usd {
  height: 24px;
  width: 24px;
  border: solid 1px #f5b014;
  border-radius: 100%;
  color: #f5b014;
  font-size: 18px;
  font-weight: bold;
  text-align: center;
  padding-top: 1px;
}
.genre {
  width: fit-content;
  height: 20px;
  padding: 5px 13px 4px;
  border-radius: 10px;
  background-color: #10b981;
  font-size: 10px;
  font-weight: 500;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.36px;
  margin-top: 6px;
}
.explore {
  color: blue;
  font-size: 16px;
  text-align: center;
  width: 100%;
}
</style>
