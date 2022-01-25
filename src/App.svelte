<script lang="ts">
  import { onMount } from "svelte";
  import * as d3 from "d3";
  let _div: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  let div = _div as unknown | SVGElement;
  let file: File;

  onMount(() => {
    _div = d3.select("div");
    let input = _div.append("input");
    input
      .attr("type", "file")
      .attr("id", "file4")
      .attr("name", "file4")
      .on("input", function (event) {
        handleFileSelect(event);
      });

    let span = _div.append("span");
    let button0 = span.append("button");
    button0
      .attr("data-startbyte", 0)
      .attr("data-endbyte", 4)
      .text("1-5")
      .on("click", function (event) {
        handleclick(event);
      });
    let button1 = span.append("button");
    button1
      .attr("data-startbyte", 5)
      .attr("data-endbyte", 14)
      .text("6-15")
      .on("click", function (event) {
        handleclick(event);
      });
    let button2 = span.append("button");
    button2
      .attr("data-startbyte", 6)
      .attr("data-endbyte", 7)
      .text("7-8")
      .on("click", function (event) {
        handleclick(event);
      });
    let button3 = span.append("button");
    button3.text("ファイル全体").on("click", function (event) {
      handleclick(event);
    });

    let div0 = _div.append("div");
    div0.attr("id", "byte_range");

    let div1 = _div.append("div");
    div1.attr("id", "byte_content");
  });

  function handleFileSelect(event: Event) {
    file = Array.from((event.target as HTMLInputElement).files)[0];
  }

  function handleclick(event: PointerEvent) {
    if (file == undefined) {
      alert("Please select a file!");
      return;
    }

    let atrrs = Array.from((event.target as HTMLButtonElement).attributes);
    let start = atrrs.length != 0 ? parseInt(atrrs[0].value) : 0;
    let stop = atrrs.length != 0 ? parseInt(atrrs[1].value) : file.size - 1;
    let reader = new FileReader();

    reader.onloadend = function (evt) {
      if (evt.target.readyState == FileReader.DONE) {
        _div
          .selectChild("#byte_range")
          .text(
            "[Read bytes: " +
              (start + 1) +
              " - " +
              (stop + 1) +
              " of " +
              file.size +
              " byte file]"
          );

        _div.selectChild("#byte_content").text(String(evt.target.result));
      }
    };

    let blob = file.slice(start, stop + 1);
    reader.readAsBinaryString(blob);
  }
</script>

<div bind:this={div} class="example" />

<style lang="scss">
  .example {
    padding: 10px;
    border: 1px solid #ccc;

    :global(#byte_content) {
      margin: 5px 0;
      max-height: 100px;
      overflow-y: auto;
      overflow-x: hidden;
    }

    :global(#byte_range) {
      margin-top: 5px;
    }

    :global(span button) {
      font-family: sans-serif;
      background: #333;
      color: #fff;
      border-radius: 4px;
      border: 0;
      padding: 10px 14px;
    }
  }
</style>
