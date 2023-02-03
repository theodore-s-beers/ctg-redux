
 
 <script>
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
<div style="width: 100px; height: 100px;margin-left:20px">
<svg viewBox="0 0 500 500">
  {#each Array(16).fill(0) as _, i}
    <polygon points="{getTrianglePoints(getRandomPosition(500), getRandomPosition(500), size)}" fill="{colors[i % colors.length]}" />
  {/each}
  
</svg>
</div>

<style>





</style>