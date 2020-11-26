<script>
  import { onMount } from "svelte";
  let canvas;
  let hat;
  let image;
  let x = 0,
    y = 0;
  let mouseX = 0,
    mouseY = 0;
  let moving = false;
  let scale = 1;

  const handleUpload = async (event) => {
    const [file] = event.target.files;

    image = await new Promise((resolve, reject) => {
      const image = new Image();
      image.onload = () => resolve(image);
      image.onerror = () => reject();
      image.src = URL.createObjectURL(file);
    });
  };

  function draw() {
    if (canvas) {
      const context = canvas.getContext("2d");

      if (image) {
        context.drawImage(image, 0, 0, 500, 500);
        context.drawImage(hat, x, y, 200 * scale, 200 * scale);
      }
      requestAnimationFrame(draw);
    }
  }

  function handleMove(event) {
    const { clientX, clientY } = event;

    if (moving) {
      const dx = clientX - mouseX;
      const dy = clientY - mouseY;
      x += dx;
      y += dy;
      mouseX = clientX;
      mouseY = clientY;
      console.log(x, y);
    }
  }

  function handleMouseDown(event) {
    const { clientX, clientY } = event;
    mouseX = clientX;
    mouseY = clientY;

    moving = true;
  }

  function handleMouseUp(event) {
    moving = false;
  }

  onMount(() => {
    draw();
    canvas.width = 500;
    canvas.height = 500;
  });
</script>

<style>
  canvas {
    margin: auto;
    width: 500px;
    height: 500px;
  }

  .hats {
    display: none;
  }
</style>

<div class="App" on:mouseup={handleMouseUp}>
  <canvas
    bind:this={canvas}
    on:mousedown={handleMouseDown}
    on:mousemove={handleMove} />
  <input type="range" bind:value={scale} min="0.1" max="3" step="0.01" />
  <input type="file" name="file" on:change={handleUpload} />
  <div class="hats"><img src="assets/gorro_01.png" bind:this={hat} /></div>
</div>
