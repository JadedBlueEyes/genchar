<script>
	import Tixy from "$lib/Tixy.svelte";
	
	let transform;
	try { // First, try and get the transform from the URL
		let substr = decodeURIComponent(window.location.hash.substring(1))
		if (substr) {transform = substr}
	} catch(e){}
	if (!transform) { // otherwise, try load from LocalStorage
		try {
			transform = window.localStorage.getItem("transform")
		} catch(e){}
	}

	// If transform is not set by this point, the user is either new (and has no data stored) or they saved an empty input field
	// In either case, give them a default value so they don't get a blank screen
	transform = transform || "cos(t + i + x * y)"
	
	$: {
		try {localStorage.setItem('transform', transform);} catch(e){}
		try {window.location.replace("#"+encodeURIComponent(transform))} catch(e){}
	}
</script>

<style>
	:global(body) {
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	
	@media (prefers-color-scheme: dark) {
		:global(body){
			background-color:#333;
		}
		input {
			background-color:#333;
			border-color: #555;
			color: #eee;
		}
		p {
			color: #eee;
		}
	}

	div.tixy {
		background: black;
		line-height: 10px;
		padding: 1em;
		margin: auto;
		width: max-content;
		border-radius: 8px;
		box-shadow: 0 0 1em rgba(0,0,0,0.4);
		position: relative;
	}
	
	div.container {
		text-align: center
	}
	
	input {
		margin: auto;
		margin-top: 2em;
		border-radius: 4px;
		width: 80%
	}
</style>

<div class=container>
	<div class=tixy>
		<Tixy transform={transform}/>
	</div>
    <label for="transform">Code transform</label>
	<input id=transform type=text bind:value={transform} />
	{#if transform.length > 32}<p>Function should be shorter!</p>{/if}
</div>

<svelte:head>
    <title>Tixy live editor{ (transform && transform !== "cos(t + i + x * y)") ? " (" + transform + ")" : ""}</title>
    <meta name="description" content="Tixy live editor lets you edit and run Tixy style code in real time. Currently editing the code {transform}">
</svelte:head>