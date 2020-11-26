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

      context.clearRect(0, 0, canvas.width, canvas.height);

      if (image) {
        context.drawImage(image, 0, 0, 500, 500);
        context.drawImage(
          hat,
          x - 100 * scale,
          y - 100 * scale,
          200 * scale,
          200 * scale
        );
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
  :global(body) {
    box-sizing: border-box;
    margin: 0;
    font-family: "Lobster", sans-serif;
  }

  canvas {
    width: 500px;
    height: 500px;
  }

  .hats {
    display: none;
  }

  .background {
    background-image: url("https://img.uxfree.com/wp-content/uploads/2017/06/christmas-seamless-pattern-k71.png");
    background-size: 300px;

    height: 100vh;

    display: grid;
    place-items: center;
  }

  main {
    padding: 32px;
    background: #f5ebe3;
    border-radius: 16px;

    display: flex;

    color: #50613b;
  }

  form {
    padding: 16px;
    display: flex;
    flex-direction: column;
  }

  .upload {
    background: #50613b;
    padding: 8px;
    border-radius: 4px;
    cursor: pointer;
    display: grid;
    place-items: center;
    color: #f5ebe3;
  }

  input[type="file"] {
    display: none;
  }

  h1 {
  }

  input[type="range"].slider {
    width: 100%;
    margin: 2.5px 0;
    background-color: transparent;
    -webkit-appearance: none;
  }
  input[type="range"].slider:focus {
    outline: none;
  }
  input[type="range"].slider::-webkit-slider-runnable-track {
    background: #c3dbab;
    border: 1px solid rgba(1, 1, 1, 0);
    border-radius: 25px;
    width: 100%;
    height: 3px;
    cursor: pointer;
  }
  input[type="range"].slider::-webkit-slider-thumb {
    margin-top: -3.5px;
    width: 8px;
    height: 8px;
    background: #6b8f3f;
    border: 8px solid #50613b;
    border-radius: 50px;
    cursor: pointer;
    -webkit-appearance: none;
  }
  input[type="range"].slider:focus::-webkit-slider-runnable-track {
    background: #d0e3bd;
  }
  input[type="range"].slider::-moz-range-track {
    background: #c3dbab;
    border: 1px solid rgba(1, 1, 1, 0);
    border-radius: 25px;
    width: 100%;
    height: 3px;
    cursor: pointer;
  }
  input[type="range"].slider::-moz-range-thumb {
    width: 8px;
    height: 8px;
    background: #6b8f3f;
    border: 8px solid #50613b;
    border-radius: 50px;
    cursor: pointer;
  }
  input[type="range"].slider::-ms-track {
    background: transparent;
    border-color: transparent;
    border-width: 3.5px 0;
    color: transparent;
    width: 100%;
    height: 3px;
    cursor: pointer;
  }
  input[type="range"].slider::-ms-fill-lower {
    background: #b6d399;
    border: 1px solid rgba(1, 1, 1, 0);
    border-radius: 50px;
  }
  input[type="range"].slider::-ms-fill-upper {
    background: #c3dbab;
    border: 1px solid rgba(1, 1, 1, 0);
    border-radius: 50px;
  }
  input[type="range"].slider::-ms-thumb {
    width: 8px;
    height: 8px;
    background: #6b8f3f;
    border: 8px solid #50613b;
    border-radius: 50px;
    cursor: pointer;
    margin-top: 0px;
    /*Needed to keep the Edge thumb centred*/
  }
  input[type="range"].slider:focus::-ms-fill-lower {
    background: #c3dbab;
  }
  input[type="range"].slider:focus::-ms-fill-upper {
    background: #d0e3bd;
  }
  /*TODO: Use one of the selectors from https://stackoverflow.com/a/20541859/7077589 and figure out
how to remove the virtical space around the range input in IE*/
  @supports (-ms-ime-align: auto) {
    /* Pre-Chromium Edge only styles, selector taken from hhttps://stackoverflow.com/a/32202953/7077589 */
    input[type="range"].slider {
      margin: 0;
      /*Edge starts the margin from the thumb, not the track as other browsers do*/
    }
  }
</style>

<div class="background" on:mouseup={handleMouseUp}>
  <main>
    <canvas
      bind:this={canvas}
      on:mousedown={handleMouseDown}
      on:mousemove={handleMove} />
    <form>
      <h1>Então é Nataaaaaaaal</h1>
      <label for="upload" class="upload">Escolher imagem</label>
      <label>
        <p>Tamanho</p>
        <input
          type="range"
          bind:value={scale}
          min="0.1"
          max="3"
          step="0.01"
          class="slider" />
      </label>
      <input id="upload" type="file" name="file" on:change={handleUpload} />
      <div class="hats"><img src="assets/gorro_01.png" bind:this={hat} /></div>
    </form>
  </main>
</div>
