<template>
  <div id="main">
    <div v-if="isTelegramClient && isTelegramApiUpdated" class="text-center">
      <!--<h3>Window Control</h3>
      <b>isExpanded</b>: {{ TWA.isExpanded }}
      <button @click="TWA.expand()">Expand</button>
      <button @click="TWA.close()">Close</button><br>
      <h3>Functions and buttons</h3>
      -->

      <div v-if="code">
        <h3>QR code:</h3>
        {{ code }} <br>

        <!-- <v-btn v-if="isUrl" size="large" @click="openLink()">
          Open Link
        </v-btn> -->
        <!--<button @click="copyCodeClipboard()">copy to clipboard</button>-->
      </div>
      <div v-if="!code">
        <h3>Scan a QR code!</h3>
      </div>
    </div>



    <div v-if="!isTelegramClient" class="text-center">
      Please open the app from a Telegram client!<br>
    </div>
    <div v-if="isTelegramClient && !isTelegramApiUpdated" class="text-center">
      Please update Telegram to Use the app!<br>
      Telegram API version needed 6.4 or greater.<br>
      Your Telegram API version: {{ TWA.version }}
    </div>
  </div>
</template>


<script setup lang="ts">
// import { prepareUrl } from './helpers'

import { ref, onMounted } from 'vue';

// @ts-ignore
const TWA = window.Telegram.WebApp;

const isTelegramClient = ref(false);
const isTelegramApiUpdated = ref(false);
const code = ref(null);
// const isUrl = ref(null);
const url = ref(null);

// Binding function to all the event types
//this.TWA.onEvent('themeChanged', this.themeChanged);
TWA.MainButton.setText("Scan QR code");
TWA.onEvent('qrTextReceived', processQRCode);
TWA.onEvent('mainButtonClicked', mainButtonClicked);

isTelegramApiUpdated.value = TWA.isVersionAtLeast('6.4');
// platform not updated if version is not 6.4 or greater

if (TWA.platform != "unknown") {
  isTelegramClient.value = true;
}

if (isTelegramClient.value && isTelegramApiUpdated.value) {
  TWA.MainButton.show();
  showQRScanner();
}

onMounted(() => {
  TWA.ready();
});

// attached with onEvent function during created
function themeChanged() {
  //TWA.showAlert('Theme has changed');
}

function mainButtonClicked() {
  showQRScanner();
}
function openLink() {
  TWA.openLink(url.value);
}
function processQRCode(data) {
  code.value = data.data;
  // const result = prepareUrl(code.value)
  const result = code.value;
  // isUrl.value = result.is_url;
  // url.value = result.value;
  url.value = result;
  hapticImpact();
  TWA.closeScanQrPopup();

  //this.TWA.showAlert(data.data);
}
// End of callbacks
function showQRScanner() {
  const par = {
    text: ""
  };
  TWA.showScanQrPopup(par);
}
function hapticImpact() {
  // light medium heavy rigid soft
  TWA.HapticFeedback.impactOccurred("heavy");
}
  //copyCodeClipboard() {
  //  var Url = this.$refs.mylink;
  //  Url.innerHTML = window.location.href;
  //  console.log(Url.innerHTML)
  //  Url.select();
  //  document.execCommand("copy");
  //}
</script>

<style scoped>
/*
bg_color            .
secondary_bg_color  var(--tg-theme-secondary-bg-color)
link_color          var(--tg-theme-link-color).
*/
#main {
  background-color: var(--tg-theme-bg-color, white);
  color: var(--tg-theme-text-color, black);
  /*https://stackoverflow.com/questions/1165497/how-to-prevent-text-from-overflowing-in-css*/
  word-wrap: break-word;
}

b {
  color: var(--tg-theme-hint-color, black);
}

h3 {
  color: var(--tg-theme-text-color, black);
}

button {
  background-color: var(--tg-theme-button-color, #008CBA);
  border: 5px;
  color: var(--tg-theme-button-text-color, black);
  padding: 15px;
  margin: 5px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 15px;
}
</style>