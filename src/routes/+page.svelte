<script lang="ts">
	import { onMount } from "svelte";
	import Webcam from "./Webcam.svelte";


  const nightMode = {
    brightness: '140%',
    contrast: '180%',
    saturate: '60%',
    // hueRotate: '90deg',
    grayscale: '80%',
    // invert: '200%',
    // sepia: '200%',
  };


  ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

	let loaded = false;
	let webcamBox: HTMLDivElement;
  let video: HTMLVideoElement;
  let url: string;
  let filterState: 'original' | 'edited' | 'none' = 'none';
	// use a map to store the filters
	const filtersMap = new Map();
  const setLoaded = (newLoaded: boolean) => {
    loaded = newLoaded;
  };
  let autoAdjustInterval: NodeJS.Timeout;

  ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

  function analyzeFrame() {
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    video = document.getElementById('video') as HTMLVideoElement;
    if (!ctx || !video) return;
    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
    const frame = ctx.getImageData(0, 0, canvas.width, canvas.height);
    
    // Compute histogram or average pixel brightness (simple example)
    let totalBrightness = 0;
    for (let i = 0; i < frame.data.length; i += 4) {
      const r = frame.data[i];
      const g = frame.data[i + 1];
      const b = frame.data[i + 2];
      const brightness = (r + g + b) / 3;
      totalBrightness += brightness;
    }
    
    const avgBrightness = totalBrightness / (canvas.width * canvas.height);
    
    // Adjust brightness and contrast dynamically based on average brightness
    const dynamicBrightness = 1.5 - avgBrightness / 255; // Example formula
    const dynamicContrast = 1.2 + (1 - avgBrightness / 255);

    video.style.filter = `brightness(${dynamicBrightness}) contrast(${dynamicContrast}) blur(1px)`;
  }

  const scrollIntoView = () => {
    const scrollTarget = document.querySelector('#scrollTarget'); 
    if (!scrollTarget) return;
    scrollTarget.scrollIntoView({behavior: "smooth", block: 'end' });
  };

	const handleUrlChange = (event: any) => {
		event.preventDefault();
    url = '';
		url = event.target.value;
    // set url in local storage
    localStorage.setItem("lastWebcamUrl", url);
		loaded = false;
    // scroll the video to the top
    scrollIntoView();
	};

  const setInputValue = (id: string, value: string) => {
    const input = document.getElementById(id) as HTMLInputElement;
    if (!input) return;
    input.value = value;
  };

	const updateElementFilters = () => {
    filterState = 'edited';
		const filters = Array.from(filtersMap.entries()).map(([key, value]) => `${key}(${value})`).join(' ');
		webcamBox.style.filter = filters;
    // save the filters to local storage
    localStorage.setItem('filters', JSON.stringify(Array.from(filtersMap.entries())));
	};

  const applyOptimalSettings = () => {
    filtersMap.clear();
    // make nightmode into an array and then set the values
    Object.entries(nightMode).forEach(([key, value]: [string, string]) => {
      filtersMap.set(key, value);
      setInputValue(key, value);
    });
    updateElementFilters();
  };  

	const changeBrightness = (event: any) => {
		const brightness = event.target.value;
		filtersMap.set('brightness', `${brightness}%`);
		updateElementFilters();
	};

	const changeContrast = (event: any) => {
		const contrast = event.target.value;
		filtersMap.set('contrast', `${contrast}%`);
		updateElementFilters();
	};

	const changeSaturation = (event: any) => {
		const saturation = event.target.value;
		filtersMap.set('saturate', `${saturation}%`);
		updateElementFilters();
	};

	const changeHue = (event: any) => {
		const hue = event.target.value;
		filtersMap.set('hue-rotate', `${hue}deg`);
		updateElementFilters();
	};

	const changeGrayscale = (event: any) => {
		const grayscale = event.target.value;
		filtersMap.set('grayscale', `${grayscale}%`);
		updateElementFilters();
	};

	const changeInvert = (event: any) => {
		const invert = event.target.value;
		filtersMap.set('invert', `${invert}%`);
		updateElementFilters();
	};

	const changeSepia = (event: any) => {
		const sepia = event.target.value;
		filtersMap.set('sepia', `${sepia}%`);
		updateElementFilters();
	};

	const clearAllFilters = () => {
		filtersMap.clear();
		webcamBox.style.filter = '';
		document.querySelectorAll('input[type="range"]').forEach((input: any) => {
			input.value = input.dataset.default;
		});
    filterState = 'none';
    video = document.getElementById('video') as HTMLVideoElement;
    video.style.filter = '';
    // remove filters from local storage
    localStorage.removeItem('filters');
    stopAdjustFilters();
	};

  const applyRecentFilters = () => {
    const filters = JSON.parse(localStorage.getItem('filters') as string);
    if (!filters) return;
    filters.forEach(([key, value]: [string, string]) => {
      filtersMap.set(key, value);
      setInputValue(key, value.replace('%', ''));
    });
    updateElementFilters();
  };

  const showOriginal = () => {
    filterState = 'original';
    filtersMap.clear();
		webcamBox.style.filter = '';
		document.querySelectorAll('input[type="range"]').forEach((input: any) => {
			input.value = input.dataset.default;
		});
  };

  const showRecentEdits = () => {
    filterState = 'edited';
    applyRecentFilters();
  };

  const startAdjustFilters = () => {
    clearAllFilters();
    autoAdjustInterval = setInterval(analyzeFrame, 1000);
  };

  const stopAdjustFilters = () => {
    clearInterval(autoAdjustInterval);
    video = document.getElementById('video') as HTMLVideoElement;
    video.style.filter = '';
  };

	onMount(() => {
		webcamBox = document.getElementById('webcamBox') as HTMLDivElement;
    // get last url from local storage
    const lastUrl = localStorage.getItem("lastWebcamUrl");
    if (lastUrl) {
      url = lastUrl;
      scrollIntoView();
    }
	});

  // when loaded changed, scroll into view
  $: if (loaded) {
    scrollIntoView();
  }
</script>

<svelte:head>
	<title>Dawnie Assist</title>
	<meta name="description" content="Dawnie assist app" />
</svelte:head>

<section>
	<h1>Dawn, the Dawnie assistant</h1>
	<h2 class="text-center"><i>"Never not know, before you do or dont go"</i> <br /><small>Dawn</small></h2>
	<br />
	<p>Enter webcam url below and then play around with the filters until you can see the waves</p>
	<div class="input-box">
		<label for="video" aria-hidden="true" class="hidden">Webcam url</label>
		<input class="url-input" type="text" placeholder="Enter a url" on:change={handleUrlChange} />
	</div>
	<div class="inputs">
		<div class="input-box">
			<label for="brightness">Brightness</label>
			<input type="range" min="0" max="500" value="100" data-default="100" id="brightness" on:change={changeBrightness} on:input={changeBrightness} />
		</div>
		<div class="input-box">
			<label for="contrast">Contrast</label>
			<input type="range" min="0" max="500" value="100" data-default="100" id="contrast" on:change={changeContrast} on:input={changeContrast} />
		</div>
		<div class="input-box">
			<label for="saturation">Saturation</label>
			<input type="range" min="0" max="500" value="100" data-default="100" id="saturate" on:change={changeSaturation} on:input={changeSaturation} />
		</div>
		<div class="input-box">
			<label for="hue">Hue</label>
			<input type="range" min="0" max="360" value="0" data-default="0" id="hue-rotate" on:change={changeHue} on:input={changeHue} />
		</div>
		<div class="input-box">
			<label for="grayscale">Grayscale</label>
			<input type="range" min="0" max="100" value="0" data-default="0" id="grayscale" on:change={changeGrayscale} on:input={changeGrayscale} />
		</div>
		<div class="input-box">
			<label for="invert">Invert</label>
			<input type="range" min="0" max="100" value="0" data-default="0" id="invert" on:change={changeInvert} on:input={changeInvert} />
		</div>
		<div class="input-box">
			<label for="sepia">Sepia</label>
			<input type="range" min="0" max="100" value="0" data-default="0" id="sepia" on:change={changeSepia} on:input={changeSepia} />
		</div>
	</div>
  <div class="buttons">
    <button class="button" on:click={applyOptimalSettings}>Night mode preset</button>
    <button class="button" on:click={clearAllFilters}>Clear filters</button>
    {#if filterState === 'original'}
      <button class="button" on:click={showRecentEdits}>Show edits</button>
    {:else if filterState === 'edited'}
      <button class="button" on:click={showOriginal}>Show original</button>
    {/if}
    <button class="button" on:click={startAdjustFilters}>Start auto adjust</button>
    <button class="button" on:click={stopAdjustFilters}>Stop auto adjust</button>
  </div>
	{#if !loaded && url}
		<div class="loader"></div>
	{/if}
  <div id="webcamBox" class="webcamBox">
    {#if url && url !== ''}
      <Webcam url={url} setLoaded={setLoaded} loaded={loaded} />
    {/if}
  </div>
  <div id="scrollTarget"></div>
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}
	h1 {
		width: 100%;
		margin-bottom: 2rem;
	}

	.loader {
		margin: 80px auto;
		width: 45px;
		aspect-ratio: 1;
		--c: no-repeat linear-gradient(var(--color-text) 0 0);
		background:
			var(--c) 0%   100%,
			var(--c) 50%  100%,
			var(--c) 100% 100%;
		animation: l2 1s infinite linear;
	}
	@keyframes l2 {
		0%  {background-size: 20% 100%,20% 100%,20% 100%}
		20% {background-size: 20% 60% ,20% 100%,20% 100%}
		40% {background-size: 20% 80% ,20% 60% ,20% 100%}
		60% {background-size: 20% 100%,20% 80% ,20% 60% }
		80% {background-size: 20% 100%,20% 100%,20% 80% }
		100%{background-size: 20% 100%,20% 100%,20% 100%}
	}

	.url-input {
		margin-left: 1rem;
    padding: 0.8rem;
    border-radius: 4px;
    border: 0;
		margin-bottom: 1rem;
	}

	.hidden {
		display: none;
	}

	.inputs {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		padding: 1rem;
		gap: 1rem;
		flex-wrap: wrap;
	}

	.input-box {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 1rem;
	}
  .buttons {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    gap: 1rem;
  }

  p {
    text-align: center;
  }
  .webcamBox {
    padding-top: 2rem;
    padding-bottom: 200px;
  }

  .text-center {
    text-align: center;
  }
  small {
    margin-top: 0.5rem;
    display: block;
  }

</style>
