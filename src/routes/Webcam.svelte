<script lang="ts">
  import { onMount } from "svelte";
  import Hls from 'hls.js';

  let video: HTMLVideoElement;
  let hls: Hls;
  // let loaded = false;
  export let url: string;
  export let setLoaded: (loaded: boolean) => void;

  onMount(() => {
    video = document.getElementById('video') as HTMLVideoElement;
    if (!url) return;
    if (Hls.isSupported()) {
      hls = new Hls();
      hls.loadSource(url);
      hls.attachMedia(video);
      hls.on(Hls.Events.MANIFEST_PARSED, () => {
        setLoaded(true);
      });
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = url;
      video.addEventListener('timeupdate', () => {
        if (video.currentTime > 0) {
          setLoaded(true);
        }
      });
    }
  });
</script>

<section>
  <video
    playsinline
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
  }
</style>

  
