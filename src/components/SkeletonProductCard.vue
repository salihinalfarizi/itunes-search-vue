<template>
    <div class="ion-no-padding column card">
      <ion-skeleton-text class="mx-2 my-2" animated style="width: 80%; height: 14px"></ion-skeleton-text>
      <ion-row class="ion-align-items-start">
        <ion-col class="mr-2 ion-no-padding" size="auto">
          <ion-thumbnail class="thumbnail ml-2 mb-2">
            <ion-skeleton-text animated></ion-skeleton-text>
          </ion-thumbnail>
        </ion-col>
        <ion-col>
          <div class="pt-2">
            <ion-skeleton-text animated style="width: 30%; height: 14px"></ion-skeleton-text>
          </div>
          <div>
            <ion-skeleton-text animated style="width: 60%; height: 14px"></ion-skeleton-text>
          </div>
          <div>
            <ion-skeleton-text animated style="width: 50%; height: 14px"></ion-skeleton-text>
          </div>
        </ion-col>
      </ion-row>
    </div>
  </template>
  <script>
  import { defineComponent } from 'vue'
  
  export default defineComponent({
    setup() {
        
    },
  })
  </script>
  
  <style lang="scss" scoped>
  .title {
    height: 16px;
  }
  
  .thumbnail {
    width: 100px;
    height: 100px;
  }
  
  ion-thumbnail {
    display: block;
  }
  
  ion-skeleton-text {
    border-radius: 4px;
  }
  
  .column {
    display: flex;
    flex-direction: column;
  }
  
  .card {
    position: relative;
    background: #ffffff;
    box-shadow: 0px 2px 8px rgba(0, 0, 0, 0.18);
    border-radius: 8px;
  }
  </style>
  