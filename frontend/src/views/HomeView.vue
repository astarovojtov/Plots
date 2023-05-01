<script setup>
import Map from '../components/Map.vue'
import { ref } from 'vue';
import Popup from '../components/Popup.vue';
let popupMessage = ref("");
let popupStatus = ref("");
let visibility = ref(false);
const showPopup = function(result) {
  popupStatus.value = result.status;
  popupMessage.value = result.message;
  visibility.value = true;
  
  setTimeout(() => { 
    visibility.value = false;
    
  }, 2000)
}
</script>

<template>
  <main>
      <Popup :message="popupMessage" :status="popupStatus" :isVisible="visibility" />
      <Map @apiResponded="showPopup"/>
  </main>
</template>
