<script lang="ts">
	import { onMount } from "svelte";

	let loaded = false;
	let iframe: HTMLIFrameElement;
	// use a map to store the filters
	const filtersMap = new Map();

	const handleUrlChange = (event: any) => {
		event.preventDefault();
		const url = event.target.value;
		loaded = false;
		iframe.style.opacity = '0';
		iframe.style.display = 'block';
		iframe.src = `https://www.hlsplayer.org/play?url=${url}`;
	};

	const updateElementFilters = () => {
		const filters = Array.from(filtersMap.entries()).map(([key, value]) => `${key}(${value})`).join(' ');
		iframe.style.filter = filters;
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
		iframe.style.filter = '';
		document.querySelectorAll('input[type="range"]').forEach((input: any) => {
			input.value = input.dataset.default;
		});
	};

	onMount(() => {
		loaded = true;
		iframe = document.getElementById('iframe') as HTMLIFrameElement;
		iframe.onload = () => {
			console.log('hi');
			loaded = true;
			iframe.style.opacity = '1';
		};
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
	<p>Enter webcam url below and then play around with the filters until you can see what you're about to shred</p>
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
	<button class="clear-button" on:click={clearAllFilters}>Reset</button>
	{#if !loaded}
		<div class="loader"></div>
	{/if}
	<iframe id="iframe" src="" frameBorder="0" width="100%" allowFullScreen title=""></iframe>
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

	iframe {
		aspect-ratio: 16 / 9;
		width: 100%;
		max-width: 100%;
		display: none;
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

	.clear-button {
		margin-bottom: 2rem;
	}

</style>
