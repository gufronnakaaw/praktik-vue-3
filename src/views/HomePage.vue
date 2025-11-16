<template>
  <ion-page>
  <ion-content :fullscreen="true">
      <Home />
    </ion-content>
  </ion-page>

</template>

<script setup lang="ts">
import Home from '@/components/Home.vue';
import { IonContent, IonPage } from '@ionic/vue';
</script>