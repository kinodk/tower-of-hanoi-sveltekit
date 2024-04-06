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
			return false;
		}

		if (toTower.length > 0 && fromTower[0] >= toTower[0]) {
			console.log('error: cannot move larger disk onto smaller disk');
			return false;
		}

		const movedDisk = fromTower.shift();

		if (typeof movedDisk !== 'number') {
			console.log('error: failed to move disk');
			return false;
		}

		toTower.unshift(movedDisk);
		return true;
	};
</script>

<div class="board">
	{#each game.towers as tower, index}
		<div class="tower">
			<button
				class="boardButton"
				on:click={() => {
					game.fromTowerIndex = index;

					if (
						game.fromTowerIndex !== undefined &&
						game.toTowerIndex !== undefined &&
						game.toTowerIndex !== game.fromTowerIndex
					) {
						let success = moveDisk(game);

						if (success) {
							game.fromTowerIndex = undefined;
							game.toTowerIndex = undefined;
							game.moves += 1;
						}
					}
				}}>fromTower</button
			>
			<button
				class="boardButton"
				on:click={() => {
					game.toTowerIndex = index;
					if (
						game.fromTowerIndex !== undefined &&
						game.toTowerIndex !== undefined &&
						game.toTowerIndex !== game.fromTowerIndex
					) {
						let success = moveDisk(game);

						if (success) {
							game.fromTowerIndex = undefined;
							game.toTowerIndex = undefined;
							game.moves += 1;
						}
					}
				}}>toTower</button
			>
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
	.boardButton {
		margin-bottom: 5px;
		width: 100px;
		height: 40px;
		border-radius: 10px;
	}

	.board {
		display: flex;
		margin-top: 0px;
		width: 100vw;
		justify-content: space-between;
		border: 2px solid black;
	}

	.tower {
		display: flex;
		flex-direction: column;
		align-items: center;
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
