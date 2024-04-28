<script lang="ts">
	import { emoji } from './emoji'

	type State = 'start' | 'playing' | 'paused' | 'won' | 'lost'

	let state: State = 'start'
	let size = 20
	let grid = createGrid()
	let maxMatches = grid.length / 2
	let selected: number[] = []
	let matches: string[] = []
	let timerId: any | null = null
	let time = 60

	function createGrid() {
		let cards = new Set<string>()
		let maxSize = size / 2

		while (cards.size < maxSize) {
			const randomIndex = Math.floor(Math.random() * emoji.length)
			cards.add(emoji[randomIndex])
		}

		return shuffle([...cards, ...cards])
	}

	function shuffle<Items>(array: Items[]) {
		return array.sort(() => Math.random() - 0.5)
	}

	function startGameTimer() {
		function countdown() {
			state !== 'paused' && (time -= 1)
		}
		timerId = setInterval(countdown, 1000)
	}

	function selectCard(cardIndex: number) {
		selected = selected.concat(cardIndex)
	}

	function matchCards() {
		// array destructuring can have any name for the values
		const [first, second] = selected

		if (grid[first] === grid[second]) {
			matches = matches.concat(grid[first])
		}

		// clear selected
		setTimeout(() => (selected = []), 300)
	}

	function pauseGame(e: KeyboardEvent) {
		if (e.key === 'Escape') {
			switch (state) {
				case 'playing':
					state = 'paused'
					break
				case 'paused':
					state = 'playing'
					break
			}
		}
	}

	function resetGame() {
		timerId && clearInterval(timerId)
		grid = createGrid()
		maxMatches = grid.length / 2
		selected = []
		matches = []
		timerId = null
		time = 60
	}

	function gameWon() {
		state = 'won'
		resetGame()
	}

	function gameLost() {
		state = 'lost'
		resetGame()
	}

	$: if (state === 'playing') {
		!timerId && startGameTimer()
	}

	$: selected.length === 2 && matchCards()
	$: maxMatches === matches.length && gameWon()
	$: time === 0 && gameLost()
</script>

<svelte:window on:keydown={pauseGame} />
<h1 class="text-3xl text-center mt-8">Memory Game</h1>

{#if state === 'start'}
<button
class="btn btn-xl variant-outline-primary border-2 border-pink-500 hover:variant-filled-primary flex justify-center mx-auto my-8 font-bold"
on:click={() => (state = 'playing')}>Start</button
>
{/if}

{#if state === 'paused'}
<h1 class="text-3xl text-center mt-8">Game Paused</h1>
{/if}

<!-- Playing state -->
{#if state === 'playing'}
 	<!-- Timer -->
	<div class="w-16 mx-auto border-4 border-pink-500 mt-4">
        <h1 class="timer text-3xl text-center p-2 text-slate-800" class:pulse={time <= 10}>
            {time}
        </h1>
    </div>

	<div class="matches flex justify-start gap-4 max-w-md mx-auto text-4xl">
        {#each matches as match}
            <div>{match}</div>
        {/each}
    </div>

	<div class="cards max-w-md mx-auto grid grid-cols-5 gap-4 justify-items-center overflow-hidden md:max-w-3xl p-8">
		{#each grid as card, cardIndex}
			{@const isSelected = selected.includes(cardIndex)}
			{@const isSelectedOrMatch =
				selected.includes(cardIndex) || matches.includes(card)}
			{@const match = matches.includes(card)}

			<button
				class="card"
				class:selected={isSelected}
				class:flip={isSelectedOrMatch}
				disabled={isSelectedOrMatch}
				on:click={() => selectCard(cardIndex)}
			>
				<div class="back" class:match>
					{card}
				</div>
			</button>
		{/each}
	</div>
{/if}

<!-- Lost state -->
{#if state == 'lost'}
    <h1 class="text-3xl text-center mt-8">Game Over! 🥺</h1>
    <button class="btn btn-xl variant-outline-primary border-2 border-pink-500 hover:variant-filled-primary flex justify-center mx-auto my-8 font-bold" on:click={() => (state = 'playing')}>Play again</button>
{/if}

<!-- Won state -->
{#if state == 'won'}
    <h1 class="text-3xl text-center mt-8">Victory is yours! You've won! 🥳</h1>
    <button class="btn btn-xl variant-outline-primary border-2 border-pink-500 hover:variant-filled-primary flex justify-center mx-auto my-8 font-bold" on:click={() => (state = 'playing')}>Play again</button>
{/if}

<style>
	.card {
		height: 128px;
		width: 128px;
		border-radius: 0.5rem;
		box-shadow: 0px 6px 10px rgba(0, 0, 255, .2);
        font-size: 60px;
		transition: rotate 0.3s ease-out;
		transform-style: preserve-3d;

		&.selected {
			border: 4px solid var(--border);
		}

		&.flip {
			rotate: y 180deg;
			pointer-events: none;
		}

		& .back {
			position: absolute;
			inset: 0;
			display: grid;
			place-content: center;
			backface-visibility: hidden;
			rotate: y 180deg;
		}

		& .match {
			transition: opacity 0.3s ease-out;
			opacity: 0.4;
		}
	}

	.timer {
		transition: color 0.3s ease;
	}

	.pulse {
		color: rgb(200, 0, 0);
		animation: pulse 1s infinite ease;
	}

	@keyframes pulse {
		to {
			scale: 1.4;
		}
	}
</style>
