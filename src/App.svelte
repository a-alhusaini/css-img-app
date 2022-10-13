<script>
  import * as HtmlToImg from "html-to-image";
  import ace from "ace-builds/src/ace.js";
  import "ace-builds/src/ext-language_tools";
  import "ace-builds/src/ext-error_marker";
  import { onMount } from "svelte";

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

  async function createImage() {
    let options = {};

    inPreviewTab = true;

    setTimeout(async () => {
      dataUrl = await HtmlToImg.toPng(document.getElementById("root"), options);

      inPreviewTab = false;
    }, 100);
  }

  onMount(() => {
    ace.config.set("basePath", "/ace/");
    ace.config.set("workerPath", "/ace/");

    ace.require("ace/ext/language_tools");
    ace.require("ace/ext/error_marker");
    let config = {
      enableBasicAutocompletion: true,
      enableLiveAutocompletion: true,
    };

    let htmlEditor = ace.edit("html");
    htmlEditor.setTheme("ace/theme/chrome");
    htmlEditor.session.setMode("ace/mode/html");
    htmlEditor.session.on("change", () => {
      html = htmlEditor.getValue();
    });
    htmlEditor.setOptions(config);

    let cssEditor = ace.edit("css");
    cssEditor.setTheme("ace/theme/chrome");
    cssEditor.session.setMode("ace/mode/css");
    cssEditor.session.on("change", () => {
      css = cssEditor.getValue();
    });
    cssEditor.setOptions(config);
  });
</script>

<main>
  <div class="code-input">
    <div on:input={(val) => console.log(val)} id="html" class="code-area">
      {html}
    </div>
    <div id="css" class="code-area">{css}</div>
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
</style>
