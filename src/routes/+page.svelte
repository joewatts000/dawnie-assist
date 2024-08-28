<script lang="ts">
	import { onMount } from "svelte";
	import Webcam from "./Webcam.svelte";

  const nightMode = {
    brightness: '200%',
    contrast: '200%',
    saturate: '200%',
    hueRotate: '90deg',
    grayscale: '100%',
    invert: '200%',
    sepia: '200%',
  };

	let loaded = false;
	let webcamBox: HTMLDivElement;
  let url: string;
	// use a map to store the filters
	const filtersMap = new Map();

	const handleUrlChange = (event: any) => {
		event.preventDefault();
		url = event.target.value;
		// loaded = false;
	};

  const setInputValue = (id: string, value: string) => {
    const input = document.getElementById(id) as HTMLInputElement;
    if (!input) return;
    input.value = value;
  };

	const updateElementFilters = () => {
		const filters = Array.from(filtersMap.entries()).map(([key, value]) => `${key}(${value})`).join(' ');
		webcamBox.style.filter = filters;
	};

  const applyOptimalSettings = () => {
    filtersMap.clear();
    filtersMap.set('brightness', nightMode.brightness);
    filtersMap.set('contrast', nightMode.contrast);
    filtersMap.set('saturate', nightMode.saturate);
    filtersMap.set('hue-rotate', nightMode.hueRotate);
    filtersMap.set('grayscale', nightMode.grayscale);
    filtersMap.set('invert', nightMode.invert);
    filtersMap.set('sepia', nightMode.sepia);
    // update elements 
    setInputValue('contrast', nightMode.contrast);
    setInputValue('saturation', nightMode.saturate);
    setInputValue('hue', nightMode.hueRotate);
    setInputValue('grayscale', nightMode.grayscale);
    setInputValue('invert', nightMode.invert);
    setInputValue('sepia', nightMode.sepia);
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
	};

	onMount(() => {
		loaded = true;
		webcamBox = document.getElementById('webcamBox') as HTMLDivElement;
	});
</script>

<svelte:head>
	<title>Dawnie Assist</title>
	<meta name="description" content="Dawnie assist app" />
</svelte:head>

<section>
	<h1>Dawn, the Dawnie assistant</h1>
	<h2><i>"Never not know, before you do or dont go"</i> - Dawn</h2>
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
			<input type="range" min="0" max="500" value="100" data-default="100" id="saturation" on:change={changeSaturation} on:input={changeSaturation} />
		</div>
		<div class="input-box">
			<label for="hue">Hue</label>
			<input type="range" min="0" max="360" value="0" data-default="0" id="hue" on:change={changeHue} on:input={changeHue} />
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
    <button class="button" on:click={applyOptimalSettings}>Night mode</button>
    <button class="button" on:click={clearAllFilters}>Reset</button>
  </div>
	{#if !loaded}
		<div class="loader"></div>
	{/if}
  <div id="webcamBox">
    {#if url}
      <Webcam url={url} />
    {/if}
  </div>
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
    justify-content: center;
    gap: 1rem;
  }
	.button {
		margin-bottom: 2rem;
	}

  p {
    text-align: center;
  }

</style>
