<script lang="ts">
	import { emoji } from './emoji'

	type State = 'start' | 'playing' | 'paused' | 'won' | 'lost'

	let state: State = 'start'
	let size = 20
	let grid = createGrid()
	let maxMatches = grid.length / 2
	let selected: number[] = []
	let matches: string[] = []

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
	
	function selectedCard(cardIndex: number) {
		selected = selected.concat(cardIndex)
	}
		
	function matchCards() {
		const [first, second] = selected

		if (grid[first] == grid[second]) {
			matches = matches.concat(grid[first])
		}

		setTimeout(() => selected = [], 300)
		selected = []
	}

	function gameWon() {
		state ='won'
	}

	$: selected.length == 2 && matchCards ()

	$: maxMatches == matches.length && gameWon()

	$: console.log({ state, selected, matches})
	
</script>

<h1 class="text-3xl text-center mt-14">Memory Game</h1>
{#if state == 'start'}
	<button class="btn btn-xl variant-outline-primary hover:variant-filled-primary flex justify-center mx-auto my-8 font-bold" on:click={() => (state = 'playing')}>Start</button>
{/if}

{#if state == 'playing'}
<div class="matches flex justify-start gap-4 max-w-md mx-auto text-4xl">
	{#each matches as card}
	<div>{card}</div>
	{/each}
</div>
	<div class="cards max-w-md mx-auto grid grid-cols-5 gap-4 justify-items-center overflow-hidden md:max-w-3xl p-8">
		{#each grid as card, cardIndex}
			{@const isSelected = selected.includes(cardIndex)}
			{@const isSelectedOrMatched = selected.includes(cardIndex) || matches.includes(card)}
			{@const match = matches.includes(card)}

			<button 
			class="card" 
			class:selected={isSelected} 
			disabled={isSelectedOrMatched}
			on:click={() => selectedCard(cardIndex)} >
				<div class:match>{card}</div>
			</button>
		{/each}
	</div>
{/if}

{#if state == 'lost'}
	<h1>Game Over! 🥺 </h1>
	<button on:click={() => state = 'playing'}>Play again</button>
{/if}

{#if state == 'won'}
	<h1>Victory is yours! You've won! 🥳</h1>
	<button on:click={() => state = 'playing'}>Play again</button>
{/if}

<style>
	.card {
		width: 128px;
		height: 128px;
		border-radius: 0.5rem;
		filter: drop-shadow(0 10px 8px rgb(0 0 0 / 0.04)) drop-shadow(0 4px 3px rgb(0 0 0 / 0.1));
		font-size: 60px;
		&.selected {
			border: 4px solid #ff62cd;
		}
		& .match {
			transition: opacity 0.3s ease-out;
			opacity: 0.4;
		}
	}
</style>