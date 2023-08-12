<script lang="ts">
	import Game from './Game.svelte';
	import '../styles.css';
	import Modal from './Modal.svelte';
	import { levels } from './levels';
	import { confetti } from '@neoconfetti/svelte';

	let state: 'waiting' | 'playing' | 'paused' | 'lost' | 'won' = 'waiting';
	let game: Game;
</script>

<svelte:head>
	<title>ematchi</title>
	<meta name="description" content="the emoji matching game" />
</svelte:head>

<main>
	<Game
		bind:this={game}
		on:play={() => {
			state = 'playing';
		}}
		on:pause={() => {
			state = 'paused';
		}}
		on:lost={() => {
			state = 'lost';
		}}
		on:won={() => {
			state = 'won';
		}}
	/>

	{#if state != 'playing'}
		<Modal>
			<header>
				<h1>e<span>match</span>i</h1>
				<p>the emoji matching game</p>
			</header>

			{#if state === 'lost' || state === 'won'}
				<p>You {state} the game</p>
			{:else if state === 'paused'}
				<p>game paused</p>
			{:else if state === 'waiting'}
				<p>choose a level:</p>
			{/if}

			<div class="buttons">
				{#if state === 'paused'}
					<button class="resume-button" on:click={() => game.resume()}>resume</button>
					<button
						class="quit-button"
						on:click={() => {
							state = 'waiting';
						}}>quit</button
					>
				{:else}
					{#each levels as level}
						<button
							class="level-button"
							on:click={() => {
								game.start(level);
							}}>{level.label}</button
						>
					{/each}
				{/if}
			</div>
		</Modal>
	{/if}

	{#if state === 'won'}
		<div
			class="confetti"
			use:confetti={{
				stageWidth: innerWidth,
				stageHeight: innerHeight
			}}
		/>
	{/if}
</main>

<style>
	main {
		text-align: center;
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
	}

	header {
		font-size: min(5vw, 2rem);
		font-family: Grandstander;
	}

	h1 {
		font-size: 4em;
		margin: 0;
		height: 1em;
	}

	h1 span {
		color: blue;
	}

	p {
		font-family: Grandstander;
	}

	.buttons {
		display: flex;
		justify-content: center;
		gap: 0.5em;
	}

	.level-button,
	.resume-button,
	.quit-button {
		background-color: blue;
		color: #fff;
		font-size: inherit;
		font-family: inherit;
		border: none;
		padding: 1em;
		border-radius: 5px;
	}

	.confetti {
		position: fixed;
		width: 100%;
		height: 100%;
		left: 50%;
		top: 30%;
		pointer-events: none;
	}
</style>
