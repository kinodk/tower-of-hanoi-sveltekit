<script lang="ts">
	import StatsContainer from './StatsContainer.svelte';

	type Index = number | undefined;
	type GameType = {
		poles: number;
		disks: number;
		board: number[][];
		targetPole: number | undefined;

		fromTowerIndex: Index;
		toTowerIndex: Index;
		moves: number;

		initializeBoard: Function;
		gameFinished: Function;
	};

	
	let game: GameType = {
		poles: 3,
		disks: 3,
		board: [],
		targetPole: undefined,
		
		fromTowerIndex: undefined,
		toTowerIndex: undefined,
		moves: 0,
		
		initializeBoard: function () {
			for (let i = 1; i <= this.poles; i++) {
				this.board.push([]);
			}
			
			for (let i = 1; i <= this.disks; i++) {
				this.board[0].push(i);
			}
		},
		
		gameFinished: function () {
			let tower: number[] = [];
			
			for (let i = 1; i <= this.disks; i++) {
				tower.push(i);
			}
			
			if (this.targetPole !== undefined) {
				return JSON.stringify(this.board[this.targetPole]) === JSON.stringify(tower)
			} else {
				return this.board.slice(1).some((value) => JSON.stringify(value) === JSON.stringify(tower))
			}
		}
	};
	
	let done = false;
	game.initializeBoard();

	const moveDisk = (): boolean | null => {
		let towers = game.board;
		let fromTowerIndex = game.fromTowerIndex;
		let toTowerIndex = game.toTowerIndex;

		if (typeof fromTowerIndex !== 'number' || typeof toTowerIndex !== 'number') {
			console.log('not an index');
			return null;
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
		if (typeof game.fromTowerIndex === 'number' && typeof game.toTowerIndex === 'number') {
			let success = moveDisk();

			done = game.gameFinished();

			if (success === true) {
				game.fromTowerIndex = undefined;
				game.toTowerIndex = undefined;
				game.moves += 1;
			}

			if (success === false) {
				game.fromTowerIndex = undefined;
				game.toTowerIndex = undefined;
			}
		}
	}}
/>

<div class="board">
	{#each game.board as pole}
		<div class="pole">
			{#each pole as disk}
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

	.pole {
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
