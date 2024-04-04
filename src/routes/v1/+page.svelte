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

	const moveDisk = (towers: Towers, fromTowerIndex: Index, toTowerIndex: Index): Towers => {
		if (typeof fromTowerIndex === 'number' && typeof toTowerIndex === 'number') {
			let fromTower = towers[fromTowerIndex];
			let toTower = towers[toTowerIndex];
			if (
				fromTower.length !== 0 &&
				(toTower.length === 0 || (toTower.length > 0 && fromTower[0] < toTower[0]))
			) {
				let movedDisk = fromTower.shift();
				if (typeof movedDisk === 'number') {
					toTower.unshift(movedDisk);
					return towers;
				}
				console.log(toTower);
			} else {
				console.log('error');
				return towers;
			}
		} else {
			console.log('not an index');
			return towers;
		}

		return towers;
	};
</script>

<div class="board">
	{#each game.towers as tower, index}
		<div class="tower">
			<button
				class="boardButton"
				on:click={() => {
					game.fromTowerIndex = index;
				}}>fromTower</button
			>
			<button
				class="boardButton"
				on:click={() => {
					game.toTowerIndex = index;
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
	<button
		class="moveButton"
		on:click={() => {
			if (
				game.fromTowerIndex !== undefined &&
				game.toTowerIndex !== undefined &&
				game.fromTowerIndex !== game.toTowerIndex
			) {
				game.towers = moveDisk(game.towers, game.fromTowerIndex, game.toTowerIndex);

				game.fromTowerIndex = undefined;
				game.toTowerIndex = undefined;
				game.moves += 1;
			}
		}}>MOVE</button
	>
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
		height: 30vh;
	}

	.moveButton {
		height: 48%;
		width: 200px;
		font-size: large;
		font-weight: bolder;
		border-radius: 10%;
	}

	.settingsContainer {
		display: flex;
		height: 48%;
		gap: 5px;
	}
</style>
