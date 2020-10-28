<script>
  import { onMount } from 'svelte';
  import Dropzone from "svelte-file-dropzone";

  let img_wall_tops = new Image(600,300);
  img_wall_tops.addEventListener('load', redraw_canvas)
  img_wall_tops.src = "AxorWallTops.png";

  onMount(
    () =>
    {
    }
  );

  function redraw_canvas()
  {
    const canvas = document.getElementById("wall_tops");
    const ctx = canvas.getContext('2d');
    ctx.drawImage( img_wall_tops, 0, 0, canvas.width, canvas.height );
  }

  function handle_file_drop(e)
  {
    const files = e.detail.acceptedFiles;
    if (files.length == 0) return;

    const reader = new FileReader();
    reader.onload = () => { img_wall_tops.src = reader.result; };
    reader.readAsDataURL(files[0]);
  }

  function handle_drag_over(e)
  {
      console.log( "DRAG OVER");
  }

  function on_click()
  {
    console.log( "CLICKED!" );
  }
</script>

<style>
  :global(.AxorDropzone){ outline:none; width:300px; }
</style>

<Dropzone on:drop={handle_file_drop} on:dragenter={handle_drag_over} disableDefaultStyles=false noClick=true noKeyboard=true
  containerClasses="AxorDropzone"
>
  <canvas id="wall_tops" width=300 height=150 on:click={e=>on_click(e)}></canvas>
</Dropzone>
