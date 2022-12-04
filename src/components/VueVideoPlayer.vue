<template>
  <div>
    <video 
      v-bind:id='videoPlayerId'
      class="video-js vjs-big-play-centered vjs-default-skin"
      playsinline preload="auto"
      controls
      oncontextmenu="false"
      :autoplay="autoplay"
      />
    {{ information }}
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch } from 'vue';
import videojs, { type VideoJsPlayer } from 'video.js';
import 'video.js/dist/video-js.css';

const props = defineProps({
  options: Object,
  autoplay: { type: Boolean, default: true },
  videoPlayerId: { type: String, default: 'videoPlayerId' },
});

const information = ref<string>('');

let videoJsPlayer : VideoJsPlayer;

const handleLoadedMetadata = () => {
  const aspectRatio = videoJsPlayer.videoWidth() / videoJsPlayer.videoHeight();
  information.value = `aspect=${aspectRatio} (${videoJsPlayer.videoWidth()}x${videoJsPlayer.videoHeight()})`;
  console.log( information.value );
};

onMounted( () => {
  videoJsPlayer = videojs( props.videoPlayerId, props.options);
  videoJsPlayer.on( 'loadedmetadata', handleLoadedMetadata );
});

onBeforeUnmount( ()=> {
  if (videoJsPlayer != null) {
    videoJsPlayer.dispose();
  }
});

const seekPosition = (seekSeconds: number) => {
  if (videoJsPlayer != null) {
    videoJsPlayer.currentTime(seekSeconds);
  }
};


watch( 
  () => props.options?.sources,
  (newSources, _ ) => {
    const ispaused = videoJsPlayer.paused();
    if (!ispaused) {
      videoJsPlayer.pause();
    }
    videoJsPlayer.src( newSources );

    // videoJsPlayer.load();
    if(!ispaused) {
      videoJsPlayer.play();
      console.log('play (auto)');
    }
  },
  {
    deep: true
  }
);

defineExpose({
  seekPosition,
});
</script>

<style scoped>
.video-js {
  object-fit: fill;
  width: 95%;
}
</style>

<style>
.video-js .vjs-current-time,
.video-js .vjs-time-divider,
.video-js .vjs-duration {
  /* この設定をグローバルで適用しないと時間情報が表示されない */
  display: block !important;
}
</style>
