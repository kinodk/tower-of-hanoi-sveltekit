<script lang="ts">
	import StatsContainer from './StatsContainer.svelte';

	let startTime: number;
	let elapsedTime: number;
	let intervalId: number | undefined;
	export let gameFinished: boolean = false;

	$: hours = String(Math.floor(elapsedTime / 1000 / 60 / 60)).padStart(2, '0') + ':';
	$: minutes = String(Math.floor(elapsedTime / 1000 / 60) % 60).padStart(2, '0') + ':';
	$: seconds = String(Math.floor((elapsedTime / 1000) % 60)).padStart(2, '0') + '.';
	$: millis = String(Math.floor((elapsedTime % 1000) / 10)).padStart(2, '0');
	$: formattedElapsedTime = `${hours == 'NaN:' || hours == '00:' ? '' : hours}${minutes == 'NaN:' || minutes == '00:' ? '00:' : minutes}${seconds == 'NaN.' || seconds == '00.' ? '00.' : seconds}${millis == 'NaN' ? '00' : millis}`;

	const startStopwatch = () => {
		startTime = Date.now();
		intervalId = setInterval(() => {
			let endTime = Date.now();
			elapsedTime = endTime - startTime;
		});
	};

	$: {
		if (gameFinished) {
			clearInterval(intervalId);
			intervalId = undefined;
		}
	}
</script>

<svelte:document
	on:keydown={() => {
		if (intervalId === undefined) {
			startStopwatch();
		}
	}}
/>

<StatsContainer header="Time Elapsed" value={formattedElapsedTime} />
