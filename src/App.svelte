<script>
  import { onMount } from "svelte";
  import { spring } from "svelte/motion";
  import saveAs from "file-saver";

  let canvas;
  let hat;
  let image;
  let mouseX = 0,
    mouseY = 0;
  let moving = false;

  let coords = spring({ x: 310, y: 50 });
  let scale = spring(2);
  let rotation = spring(0);

  const handleUpload = async (event) => {
    const [file] = event.target.files;

    image = await new Promise((resolve, reject) => {
      const image = new Image();
      image.onload = () => resolve(image);
      image.onerror = () => reject();
      image.src = URL.createObjectURL(file);
    });
  };

  function handleMove(event) {
    const { clientX, clientY } = event;

    if (moving) {
      const dx = clientX - mouseX;
      const dy = clientY - mouseY;
      coords.update(({ x, y }) => ({ x: x + dx, y: y + dy }));
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

  $: {
    if (canvas) {
      const context = canvas.getContext("2d");

      context.clearRect(0, 0, canvas.width, canvas.height);

      if (image) {
        context.drawImage(image, 0, 0, 500, 500);

        context.setTransform($scale, 0, 0, $scale, $coords.x, $coords.y); // sets scale and origin
        context.rotate((($rotation + 360) * Math.PI) / 180);

        context.drawImage(hat, -100, -100, 200, 200);

        context.setTransform(1, 0, 0, 1, 0, 0);
        context.restore();
      }
    }
  }

  function save() {
    canvas.toBlob((blob) => saveAs(blob, "entao-eh-natal.png"), "image/png");
  }

  onMount(async () => {
    canvas.width = 500;
    canvas.height = 500;

    image = await new Promise((resolve, reject) => {
      const image = new Image();
      image.onload = () => resolve(image);
      image.onerror = () => reject();
      image.src = "assets/zorron.webp";
    });
  });
</script>

<style>
  :global(body) {
    box-sizing: border-box;
    margin: 0;
    font-family: "Lobster", Apple Color Emoji, Segoe UI Emoji, sans-serif;
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
    padding: 0 16px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
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

  button {
    background: #50613b;
    padding: 8px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    display: grid;
    place-items: center;
    color: #f5ebe3;

    font-family: "Lobster", Apple Color Emoji, Segoe UI Emoji, sans-serif;
  }
</style>

<div class="background" on:mouseup={handleMouseUp}>
  <main>
    <canvas
      bind:this={canvas}
      on:mousedown={handleMouseDown}
      on:mousemove={handleMove} />
    <form>
      <h1>Então é Nataaaal 🎄</h1>
      <label for="upload" class="upload">Escolher imagem</label>
      <label>
        <p>Tamanho</p>
        <input
          type="range"
          bind:value={$scale}
          min="0.1"
          max="3"
          step="0.01"
          class="slider" />
      </label>
      <label>
        <p>Rotação</p>
        <input
          type="range"
          bind:value={$rotation}
          min="-90"
          max="90"
          step="0.1"
          class="slider" />
      </label>
      <input id="upload" type="file" name="file" on:change={handleUpload} />
      <div class="hats"><img src="assets/gorro_01.png" bind:this={hat} /></div>
      <button type="button" on:click={save}>Salvar ⬇</button>
    </form>
  </main>
</div>
