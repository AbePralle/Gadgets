<script>
  import Dropzone from "svelte-file-dropzone";

	let image_data = [];

  function handleFilesSelect(e) {
    const files = e.detail.acceptedFiles;
    for (let i = 0; i < files.length; i++) {
      const reader = new FileReader();
      reader.onload = () => {
        const data = reader.result;
				//const image = document.createElement('img');
				//image.src = data;
        //images.push( image );
				image_data.push( data );
				image_data = image_data;
      };
      reader.readAsDataURL(files[i]);
    }
  }
</script>

<Dropzone on:drop={handleFilesSelect} />
<ol>
  {#each image_data as data}
    <li><img src={data} width=100 alt="YK"></li>
  {/each}
</ol>
