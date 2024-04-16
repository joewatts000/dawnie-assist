<script>
	import githubWhite from '$lib/images/github-white.svg';
</script>

<header>
	<div class="corner">
		<a href="https://github.com/joewatts000">
			<img src={githubWhite} alt="GitHub" />
		</a>
	</div>
</header>

<style>
	header {
		display: flex;
		justify-content: flex-end;
	}

	.corner {
		width: 3em;
		height: 3em;
	}

	.corner a {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
	}

	.corner img {
		width: 2em;
		height: 2em;
		object-fit: contain;
	}
	a:hover {
		transform: scale(1.1);
	}
</style>
