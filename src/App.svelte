<script lang="ts">
  import Counter from './lib/Counter.svelte'
  import {onMount} from "svelte";

  let canvas: HTMLCanvasElement;
  let ctx;

  onMount(() => {
    ctx = canvas.getContext("2d");
    ctx.beginPath();
    ctx.arc(250, 250, 250, 0, 2*Math.PI);
    ctx.fillStyle = "lightgray";
    ctx.fill();
  })

  const drawPoint = (p) => {
    ctx.beginPath();
    ctx.arc(p.x, p.y, 2, 0, 2 * Math.PI);
    ctx.fillStyle = "black";
    ctx.fill();
  }

  const pointInCircle = (p) => {
    return Math.sqrt(p.x**2 + p.y**2) < 1;
  }

  let pointCount = 0;
  let inCount = 0;

  $: ratio = inCount / (pointCount || 1);

  const doNTimes = (fn) => (n) => {
    for (let i = 0; i < n; i++) {
      fn();
    }
  }

  const addPoint = () => {
    const x = Math.random();
    const y = Math.random();
    drawPoint({x: x*500, y: y*500})

    pointCount++;
    if (pointInCircle({x: (x*2)-1, y: (y*2)-1})) {
      inCount++;
    }
  }

  let numPointsToAdd = 100;

  const addNPoints = doNTimes(addPoint);

  $: ourPi = ratio * 4;
</script>

<div class="wrapper">
  <div>
    <canvas width="500" height="500" bind:this={canvas}></canvas>

    <button on:click={addPoint}>Add point</button>
    <button on:click={() => {
      addNPoints(10);
    }}>Add 10 points</button>

    <input bind:value={numPointsToAdd} />
    <button on:click={() => addNPoints(numPointsToAdd)}>Add {numPointsToAdd} points</button>
  </div>

  <div>
    <p>Points: {pointCount}</p>
    <p>In Circle: {inCount}</p>
    <p>Percent in circle: {Math.round(ratio * 100)}</p>
    <p>PI Calculation: {ratio * 4}</p>

    <div class="pie-viz">
      {#if ourPi > Math.PI}
        <div class="layer"></div>
      {/if}

      <div class="layer partial" style="--deg: {((ourPi % Math.PI) / Math.PI) * 360}deg" />
    </div>
  </div>
</div>

<style>
  .wrapper {
    display: flex;
  }
  .wrapper div {
    padding: 1rem;
    flex-basis: 0;
  }
  .layer {
    width: 200px;
    height: 200px;
    border-radius: 10000px;
    background: #00000055;
    position: absolute;
    top: 0;
    left: 0;
  }

  .layer.partial {
    background: conic-gradient(#00000055 0deg, #00000055 var(--deg), transparent var(--deg), transparent);
  }

  .pie-viz {
    position: relative;
    width: 200px;
    height: 200px;
  }

  button {
    background-color: lightgray;
    padding: 0.5rem;
  }
</style>
