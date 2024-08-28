<script lang="ts">
  import { onMount } from "svelte";
  import Hls from 'hls.js';

  let video: HTMLVideoElement;
  let hls: Hls;
  // let loaded = false;
  export let url: string;

  onMount(() => {
    video = document.getElementById('video') as HTMLVideoElement;
    if (!url) return;
    if (Hls.isSupported()) {
      hls = new Hls();
      hls.loadSource(url);
      hls.attachMedia(video);
      hls.on(Hls.Events.MANIFEST_PARSED, () => {
        // video.play();
      });
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = url;
    }
  });
</script>

<section>
  <video
    controls      
    playsInline
    muted
    autoPlay
    id="video"
  >
    Your browser does not support the video tag.
  </video>
</section>

<style>
  video {
    width: 100%;
    padding-bottom: 200px;
  }
</style>

  
