<script setup lang="ts">
import { reactive, ref, toRef, watch } from 'vue';
import VueVideoPlayer from './components/VueVideoPlayer.vue';

let videoOptions = reactive({
  sources: [
    {
      type: 'video/mp4',
      src: 'https://vjs.zencdn.net/v/oceans.mp4',
    }
  ],
  html5: {
    nativeCaptions: false
  },
  controlBar: {
    children: [
      'PlayToggle',
      'VolumePanel',
      'currentTimeDisplay',
      'timeDivider',
      'durationDisplay',
      'ProgressControl',
      //'PictureInPictureToggle',
      //'FullscreenToggle',
    ],
    
    VolumePanel: {
      inline: false,
    },
  },
  
});

const vueVideoPlayer = ref<any>(null);
const inputUrl = ref('https://vjs.zencdn.net/v/oceans.mp4');
const inputSeekSec = ref(10);

const refreshMovie = () => {
  videoOptions.sources[0] = {
    type: 'video/mp4',
    src: inputUrl.value,
  };
}

const seekMovie = () => {
  const seekSeconds = Number(inputSeekSec.value);
  vueVideoPlayer.value.seekPosition(seekSeconds);
}
</script>

<template>
  <v-container>
    <v-row no-gutters>
      <v-col>
        <v-sheet>
          <vue-video-player :options="videoOptions"
            ref="vueVideoPlayer"
            v-bind:autoplay="false"
          />
          </v-sheet>
      </v-col>
      <v-col class="v-col-5">
        <v-container>
          <v-row>
            <v-col>
              <v-text-field label="動画URL" v-model="inputUrl"></v-text-field>
            </v-col>
            <v-col class="v-col-1">
              <v-btn v-on:click="refreshMovie">更新</v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="v-col-6">
              <v-text-field label="指定秒数へジャンプ" v-model="inputSeekSec"></v-text-field>
            </v-col>
            <v-col class="v-col-1">
              <v-btn v-on:click="seekMovie">適用</v-btn>
            </v-col>
          </v-row>
        </v-container>
      </v-col>
    </v-row>

  </v-container>
</template>

<style >

</style>
