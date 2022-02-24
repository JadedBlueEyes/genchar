<script>
	import { onMount } from 'svelte';
	
	export let size = 16;
	export let transform = "";
	
	function setStyle(node, value) {
		return {
			update(value) {
					node.style.transform="scale("+value+")";
					node.style.backgroundColor=(value<0?"red":"white");
			},
		};
	}
	
	const gridIndexes = (Array(size*size)).fill(1).map((item, index) => index);
	let grid = (Array(size*size)).fill(0);

	const xFromI = i => i%size;
	const yFromI = i => Math.floor(i/size);
	const limit = (v) => Math.max(Math.min(v,1),-1);
	
	let error;
	
	const callTransform = (t,i) => {
		let n = 0;
		try {
			n = transformEval(t/1000, i, xFromI(i), yFromI(i))
		} catch (e) {
			error = e;
		}
		return limit(0 + n)
	};
	
	let transformEval;
	$: try { 
		transformEval = new Function("t", "i", "x", "y", "with (Math) {return "+transform+";}");
		error = "";
		} catch (e) {
			console.log(e)
			error = e;
			transformEval = new Function("t", "i", "x", "y", "return 0;");
		}
	
	onMount(() => {
		let frame;
		
		(function loop() {
			frame = requestAnimationFrame(loop);
			const t = window.performance.now();
			grid = grid.map((item, i) => callTransform(t, i));
		}());
		
		return () => {
			cancelAnimationFrame(frame);
		};
	});
</script>

<style>
	div {
		margin: 0;
		padding: 0;
		background-color: white;
		display: inline-block;
		width: 20px;
		height: 20px;
		border-radius: 50%;
		margin: 1px 1px 0 0;
		transform-origin: center center;
		transform: scale(1);
	}
	
	p {
		color: red;
		position: absolute;
		bottom: 0;
	}
</style>
{#each gridIndexes as n}
	<div use:setStyle={grid[n]}></div>{#if (n+1)%size==0}<br>{/if}
{/each}
{#if error}<p>{error}</p>{/if}