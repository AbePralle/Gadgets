<script>
  import { onMount } from 'svelte';
  import Dropzone from "svelte-file-dropzone";

  let img_wall_tops = new Image(600,300);
  img_wall_tops.addEventListener('load', redraw_wall_top_tileset)
  img_wall_tops.src = "AxorWallTops.png";

  let img_ground_tiles = new Image(600,200);
  img_ground_tiles.addEventListener('load', redraw_arena);
  img_ground_tiles.src = "GroundTiles.png";

  let is_drawing = false;
  let last_draw_index = -1;
  let selected_tile = 0;

  let arena =
  [
    0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 1, 1, 1, 0, 0, 0, 0, 0,
    0, 1, 0, 1, 0, 0, 0, 0, 0,
    0, 1, 1, 1, 0, 0, 0, 0, 0,
    0, 1, 0, 1, 0, 0, 0, 0, 0,
    0, 1, 0, 1, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0
  ];

  let variations =
  [
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0
  ];

  onMount(
    () =>
    {
    }
  );

  function redraw_wall_top_tileset()
  {
    const canvas = document.getElementById("wall_top_canvas");
    const ctx = canvas.getContext('2d');
    ctx.drawImage( img_wall_tops, 0, 0, canvas.width, canvas.height );

    ctx.strokeStyle = 'rgb(0,0,0,0.25)';
    for (let i=0; i<6; ++i) ctx.strokeRect( 50*i, 0, 50, 150 );
    for (let j=0; j<3; ++j) ctx.strokeRect( 0, 50*j, 300, 50 );

    redraw_arena();
  }

  function redraw_arena()
  {
    {
      const canvas = document.getElementById("ground_tile_canvas");
      const ctx = canvas.getContext('2d');
      ctx.drawImage( img_ground_tiles, 0, 0, canvas.width, canvas.height );
    }

    const canvas = document.getElementById("arena_canvas");
    const ctx = canvas.getContext('2d');

    let index = 0;
    for (let j=0; j<9; ++j)
    {
      for (let i=0; i<9; ++i)
      {
        if (arena[index++])
        {
          draw_tile( 0, i, j, ctx );
          draw_wall( i, j, ctx );
        }
        else
        {
          draw_tile( 0, i, j, ctx );
        }
      }
    }
  }

  function draw_wall( i, j, ctx )
  {
    if (tile_at(i,j+1))
    {
      // Top Left
      if (tile_at(i-1,j) && tile_at(i-1,j+1))
      {
        if (tile_at(i,j-1))
        {
          if (tile_at(i-1,j-1)) draw_random_solid_wall_top(i*2,j*2,ctx);
          else                  draw_quarter_tile( 5, 1, i*2, j*2, ctx );
        }
        else
        {
          draw_quarter_tile( 2, 0, i*2, j*2, ctx );
        }
      }
      else
      {
        if (tile_at(i,j-1))
        {
          draw_quarter_tile( 0, 1, i*2, j*2, ctx );
        }
        else
        {
          draw_quarter_tile( 0, 0, i*2, j*2, ctx );
        }
      }

      // Top Right
      if (tile_at(i+1,j) && tile_at(i+1,j+1))
      {
        if (tile_at(i,j-1))
        {
          if (tile_at(i+1,j-1)) draw_random_solid_wall_top(i*2+1,j*2,ctx);
          else                  draw_quarter_tile( 4, 1, i*2+1, j*2, ctx );
        }
        else
        {
          draw_quarter_tile( 1, 0, i*2+1, j*2, ctx );
        }
      }
      else
      {
        if (tile_at(i,j-1))
        {
          draw_quarter_tile( 3, 1, i*2+1, j*2, ctx );
        }
        else
        {
          draw_quarter_tile( 3, 0, i*2+1, j*2, ctx );
        }
      }

      // Bottom Left
      if (tile_at(i-1,j) && tile_at(i-1,j+1))
      {
        if (tile_at(i,j+1))
        {
          if (tile_at(i-1,j+1)) draw_random_solid_wall_top(i*2,j*2+1,ctx);
          else                  draw_quarter_tile( 5, 0, i*2, j*2+1, ctx );
        }
        else
        {
          draw_quarter_tile( 2, 2, i*2, j*2+1, ctx );
        }
      }
      else
      {
        if (tile_at(i,j+1))
        {
          draw_quarter_tile( 0, 1, i*2, j*2+1, ctx );
        }
        else
        {
          draw_quarter_tile( 0, 2, i*2, j*2+1, ctx );
        }
      }

      // Bottom Right
      if (tile_at(i+1,j) && tile_at(i+1,j+1))
      {
        if (tile_at(i,j+1))
        {
          if (tile_at(i+1,j+1)) draw_random_solid_wall_top(i*2+1,j*2+1,ctx);
          else                  draw_quarter_tile( 4, 0, i*2+1, j*2+1, ctx );
        }
        else
        {
          draw_quarter_tile( 1, 2, i*2+1, j*2+1, ctx );
        }
      }
      else
      {
        if (tile_at(i,j+1))
        {
          draw_quarter_tile( 3, 1, i*2+1, j*2+1, ctx );
        }
        else
        {
          draw_quarter_tile( 3, 2, i*2+1, j*2+1, ctx );
        }
      }
    }
    else
    {
      draw_tile( 1, i, j, ctx );
    }
    if (!tile_at(i,j-1)) draw_tile( 2, i, j-1, ctx );
  }

  function draw_random_solid_wall_top( i, j, ctx )
  {
    switch (variations[j*18+i])
    {
      case 0: draw_quarter_tile( 1, 1, i, j, ctx ); break;
      case 1: draw_quarter_tile( 2, 1, i, j, ctx ); break;
      case 2: draw_quarter_tile( 4, 2, i, j, ctx ); break;
      case 3: draw_quarter_tile( 5, 2, i, j, ctx ); break;
    }
  }

  function draw_tile( tile, i, j, ctx )
  {
    let x = i * 100;
    let y = j * 100;
    ctx.drawImage( img_ground_tiles, tile*200, 0, 200, 200, x, y, 100, 100 );
  }

  function draw_quarter_tile( tile_i, tile_j, i, j, ctx )
  {
    let x = i * 50;
    let y = j * 50;
    ctx.drawImage( img_wall_tops, tile_i*100, tile_j*100, 100, 100, x, y, 50, 50 );
  }

  function handle_wall_top_file_drop(e)
  {
    const files = e.detail.acceptedFiles;
    if (files.length == 0) return;

    const reader = new FileReader();
    reader.onload = () => { img_wall_tops.src = reader.result; };
    reader.readAsDataURL(files[0]);
  }

  function handle_ground_file_drop(e)
  {
    const files = e.detail.acceptedFiles;
    if (files.length == 0) return;

    const reader = new FileReader();
    reader.onload = () => { img_ground_tiles.src = reader.result; };
    reader.readAsDataURL(files[0]);
  }

  function on_draw(e)
  {
    if ( !is_drawing ) return;
    const canvas = document.getElementById("arena_canvas");
    const offset = canvas.getBoundingClientRect();
    let i = ((e.x - offset.left) / 100) | 0;
    let j = ((e.y - offset.top) / 100) | 0;
    let index = j*9 + i;
    if (index == last_draw_index) return;

    if (last_draw_index == -1) selected_tile = arena[index] ^ 1;
    arena[ index ] = selected_tile;
    last_draw_index = index;

    variations[(j*2)*18+i*2] = (Math.random() * 4) | 0;
    variations[(j*2+1)*18+i*2] = (Math.random() * 4) | 0;
    variations[(j*2+1)*18+i*2+1] = (Math.random() * 4) | 0;
    variations[(j*2)*18+i*2+1] = (Math.random() * 4) | 0;

    redraw_arena();
  }

  function tile_at(i,j)
  {
    if (i<0 || j<0 || i>=9 || j>=9) return 0;
    return arena[ j*9 + i ];
  }
</script>

<div style="width:300px">
  Here are wall-top quarter tiles. You can drag a new image with an updated set of tiles here to use those tiles instead.
</div>
<Dropzone on:drop={handle_wall_top_file_drop} disableDefaultStyles=false noClick=true noKeyboard=true
  containerClasses="WallTopDropzone" >
  <canvas id="wall_top_canvas" width=300 height=150></canvas>
</Dropzone>

<p>Ditto with these other tiles.
<Dropzone on:drop={handle_ground_file_drop} disableDefaultStyles=false noClick=true noKeyboard=true
  containerClasses="WallTopDropzone" >
  <canvas id="ground_tile_canvas" width=300 height=100></canvas>
</Dropzone>
</p>

<p>Click and drag below to add or remove walls.<br>
  <canvas id="arena_canvas" width=900 height=900
    on:mousedown={e=>{is_drawing=true;on_draw(e)}}
    on:mouseup={e=>{is_drawing=false;last_draw_index=-1}}
    on:mousemove={e=>on_draw(e)}
  >
  </canvas>
</p>

<style>
  :global(.WallTopDropzone){ outline:none; width:300px; }
</style>

