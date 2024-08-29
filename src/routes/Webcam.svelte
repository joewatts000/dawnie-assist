<script lang="ts">
  import { onMount } from "svelte";
  import Hls from 'hls.js';

  let video: HTMLVideoElement;
  let hls: Hls;
  export let url: string;
  export let setLoaded: (loaded: boolean) => void;
  export let loaded: boolean;

  const handleUrlChange = () => {
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
  };

  onMount(() => {
    handleUrlChange();
  });

  // when url changes update the video
  $: if (url) {
    handleUrlChange();
  }
</script>

<section class={loaded ? 'loaded' : ''}>
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
  section {
    opacity: 0;
    transition: opacity 0.2s ease-in-out;
  }
  .loaded {
    opacity: 1;
  }
</style>

  
