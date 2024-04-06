<script lang="ts">
	import StatsContainer from './StatsContainer.svelte';

	type Tower = Array<number>;
	type Index = number | undefined;
	type Towers = Array<Tower>;

	let game: { towers: Towers; fromTowerIndex: Index; toTowerIndex: Index; moves: number } = {
		towers: [[1, 2, 3, 4, 5], [], []],
		fromTowerIndex: undefined,
		toTowerIndex: undefined,
		moves: 0
	};

	const moveDisk = (gameObject: typeof game): boolean => {
		let towers = gameObject.towers
		let fromTowerIndex = gameObject.fromTowerIndex
		let toTowerIndex = gameObject.toTowerIndex

		if (typeof fromTowerIndex !== 'number' || typeof toTowerIndex !== 'number') {
			console.log('not an index');
			return false;
		}

		const fromTower = towers[fromTowerIndex];
		const toTower = towers[toTowerIndex];

		if (fromTower.length === 0) {
			console.log('error: empty source tower');
			game.fromTowerIndex = undefined
			game.toTowerIndex = undefined
			return false;
		}

		if (toTower.length > 0 && fromTower[0] >= toTower[0]) {
			console.log('error: cannot move larger disk onto smaller disk');
			game.fromTowerIndex = undefined
			game.toTowerIndex = undefined
			return false;
		}

		const movedDisk = fromTower.shift();

		if (typeof movedDisk !== 'number') {
			console.log('error: failed to move disk');
			game.fromTowerIndex = undefined
			game.toTowerIndex = undefined
			return false;
		}

		toTower.unshift(movedDisk);
		game.fromTowerIndex = undefined
		game.toTowerIndex = undefined
		game.moves += 1
		return true;
	};

	const keyAssignTowerIndex = (e: KeyboardEvent) => {
		if ([1, 2, 3].includes(+e.key)) {
			if (game.fromTowerIndex !== undefined) {
				game.toTowerIndex = +e.key - 1;
			}
			if (game.fromTowerIndex === undefined) {
				game.fromTowerIndex = +e.key - 1;
			}
		}
	};
</script>

<svelte:window
	on:keydown|preventDefault={(e) => {
		keyAssignTowerIndex(e);
		let success = moveDisk(game)
	}}
/>

<div class="board">
	{#each game.towers as tower}
		<div class="tower">
			{#each tower as disk}
				<div class="disk" style="width:{disk * 50}px">
					{disk}
				</div>
			{/each}
		</div>
	{/each}
</div>

<div class="controlCenterContainer">
	<div class="settingsContainer">
		<StatsContainer header="fromTowerIndex" value={game.fromTowerIndex} />
		<StatsContainer header="toTowerIndex" value={game.toTowerIndex} />
		<StatsContainer header="Moves" value={game.moves} />
	</div>
</div>

<style>
	.board {
		display: flex;
		margin-top: 0px;
		width: 100vw;
		justify-content: space-between;
		border: 2px solid black;
		height: 50vh;
	}

	.tower {
		display: flex;
		flex-direction: column;
		align-items: center;
		align-self: flex-end;
		border: 2px black;
		padding: 20px;
		width: 33%;
	}

	.disk {
		height: 50px;
		background-color: black;
		margin-bottom: 2px;
	}

	.controlCenterContainer {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;
		width: 100vw;
		border: 1px solid black;
		height: 20vh;
	}

	.settingsContainer {
		display: flex;
		height: 100%;
		gap: 5px;
	}
</style>
