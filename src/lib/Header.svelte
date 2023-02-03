<script lang="ts">
	import { page } from '$app/stores';
	 let size = 100;
	 let colors = ['#003366', '#99CC00', '#880000', 'grey'];

  function getRandomPosition(max) {
    return Math.floor(Math.random() * max);
  }

  function getTrianglePoints(x, y, size) {
    let topPoint = `${x},${y - size / 2}`;
    let leftPoint = `${x - size / 2},${y + size / 2}`;
    let rightPoint = `${x + size / 2},${y + size / 2}`;

    return `${topPoint} ${leftPoint} ${rightPoint}`;
  }
</script>

 <div style="width: 140px; height: 140px; padding:20px " >
<svg viewBox="20 20 500 500">
  {#each Array(16).fill(0) as _, i}
    <polygon points="{getTrianglePoints(getRandomPosition(500), getRandomPosition(500), size)}" fill="{colors[i % colors.length]}" />
  {/each}
  
</svg>
</div>
<div class="mx-auto mb-4 max-w-[76rem] px-4 text-gray-50" style="position:absolute; top:50px; left:120px">
 

	<h1 class="mb-2.5 text-5xl" ><a href="/">Closing the Gap</a></h1>
	<h2 class="text-2xl">in Non-Latin-Script Data</h2>

</div>

<div class="mb-4 bg-[#f29559] py-3">
	<div class="mx-auto flex max-w-[76rem] space-x-4 px-4 text-xl font-normal">
		<a
			href="/"
			class="underline-offset-2 hover:underline"
			class:underline={$page.route.id === '/'}>Projects</a
		>
		<a
			href="/map"
			class="underline-offset-2 hover:underline"
			class:underline={$page.route.id === '/map'}>Map</a
		>
		<a
			href="/timeline"
			class="underline-offset-2 hover:underline"
			class:underline={$page.route.id === '/timeline'}>Timeline</a
		>

		<a
			href="/about"
			class="underline-offset-2 hover:underline"
			class:underline={$page.route.id === '/about'}>About</a
		>
	</div>
</div>
