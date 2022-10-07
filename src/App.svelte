<script>
  import * as HtmlToImg from "html-to-image";
  let html = `
<!-- root id is what gets rendered -->
<div id="root">

</div>`;
  let css = `
/* base styles to avoid weirdness */
#root {
  width: fit-content;
  padding: 1rem;
}
`;
  let dataUrl = "";
  let inPreviewTab = true;
  let width = "";
  let height = "";

  async function createImage() {
    let options = {};
    if (width != "") options.width = width;
    if (height != "") options.height = height;

    inPreviewTab = true;

    setTimeout(async () => {
      dataUrl = await HtmlToImg.toPng(document.getElementById("root"), options);

      inPreviewTab = false;
    }, 100);
  }
</script>

<main>
  <div class="code-input">
    <textarea class="code-area" spellcheck="false" bind:value={html} />
    <textarea class="code-area" spellcheck="false" bind:value={css} />
  </div>
  <div class="inputs">
    <div>
      <span>Width</span>
      <input bind:value={width} type="text" name="width" />
    </div>
    <div>
      <span>Height</span>
      <input bind:value={height} type="text" name="height" />
    </div>
  </div>
  <div class="panes">
    <div on:click={() => (inPreviewTab = true)} class="pane">Preview</div>
    <div
      on:click={() => {
        createImage();
      }}
      class="pane"
    >
      {#if !inPreviewTab}
        Re-render Image
      {:else}
        Image
      {/if}
    </div>
  </div>
  <div class:hidden={inPreviewTab === false}>
    {@html html}
    {@html `<style>${css}</style>`}
  </div>
  <div class:hidden={inPreviewTab === true}>
    <img class="image-display" alt="rendered result" src={dataUrl} />
  </div>
</main>

<style>
  .image-display {
    border: 5px solid black;
  }

  .code-input {
    display: flex;
    gap: 1rem;
  }

  .hidden {
    display: none;
  }

  .code-input > * {
    flex-basis: 50%;
    min-height: 40vh;
  }

  .panes {
    display: flex;
    gap: 1rem;
  }

  .pane {
    flex-basis: 50%;
    cursor: pointer;
    border-bottom: #0f0f0f 2px solid;
    font-weight: bold;
    font-size: 1.5rem;
    padding: 1rem;
    text-align: center;
  }

  .inputs {
    display: flex;
    margin: 0 auto;
    width: fit-content;
    margin-bottom: 1rem;
    margin-top: 1rem;
    gap: 1rem;
  }

  .inputs > div > span {
    font-size: 1.5rem;
  }
  .inputs > div > input {
    display: block;
    margin-top: 0.5rem;
    padding: 0.5rem;
    border-radius: 8px;
  }
</style>
