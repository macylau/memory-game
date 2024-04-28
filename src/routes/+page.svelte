<script lang="ts">
	import { emoji } from './emoji'

	type State = 'start' | 'playing' | 'paused' | 'won' | 'lost'

	let state: State = 'start'
	let size = 20
	let grid = createGrid()
	let maxMatches = grid.length / 2
	let seleted: number[] = []
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
	console.log(grid)
</script>

<h1 class="text-3xl text-center mt-14">Memory Game</h1>
{#if state == 'start'}
	<button class="btn btn-xl variant-outline-primary hover:variant-filled-primary flex justify-center mx-auto my-8 font-bold" on:click={() => (state = 'playing')}>Start</button>
{/if}

{#if state == 'playing'}
	<div class="max-w-md mx-auto cards grid grid-cols-5 gap-4 justify-items-center m-auto overflow-hidden md:max-w-3xl p-8">
		{#each grid as card, cardIndex}
			<button class="card drop-shadow-lg w-32 h-32 rounded-lg focus:border-4 border-pink-400">
				<div class="text-6xl">{card}</div>
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

