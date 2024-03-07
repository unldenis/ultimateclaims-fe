<script>
    import { onMount } from 'svelte';
  
    let itemsArray;
  
    let flags = [];

     // Funzione per leggere il contenuto del file
    function readFile(file) {
        const reader = new FileReader();
            reader.onload = function(event) {
                const content = event.target.result.toString().split(/\r?\n/);
                content.shift()
                content.forEach(element => {
                    if(element.toString().length > 0){
                       //console.log(element.toString())
                        flags = [...flags, element.split(",")]
                    }
 
                });
        };
        return reader.readAsText(file);
    }

    const fetchFileContent = async filename => {
      try {
        const response = await fetch(filename);
        const data = await response.text();
        itemsArray = data.split(/\r?\n/);
        itemsArray.shift();
      } catch (error) {
        console.error('Error loading items.csv:', error);
      }
    };

    onMount(() => {
      fetchFileContent('items.csv');
    });

    let files;
	$: if (files) {
		// Note that `files` is of type `FileList`, not an Array:
		// https://developer.mozilla.org/en-US/docs/Web/API/FileList
        readFile(files[0])
	}

        // Definisci la funzione per creare un nuovo file e fornire un URL per il download
    const downloadFile = () => {
      //alert(JSON.stringify(flags))
      let content = "flag,item,name"
      flags.forEach(flag => content += `\n${flag[0]},${flag[1]},${flag[2]}`)
      const blob = new Blob([content], { type: 'text/csv' }); // Crea un oggetto Blob
      const url = URL.createObjectURL(blob); // Ottieni un URL per il Blob
      const a = document.createElement('a'); // Crea un nuovo elemento <a>
      a.href = url; // Imposta l'URL come href
      a.download = 'flags.csv'; // Imposta il nome del file per il download
      a.click(); // Simula un clic sull'elemento <a> per avviare il download
      URL.revokeObjectURL(url); // Rilascia l'URL creato
    };
  </script>
  
<main>
    <h1>UltimateClaims Setup</h1>
    <button on:click={downloadFile}>Save</button>
    <br>
    <br>
    <div>
        <label for="avatar">Upload plugin flags.csv:</label>
        <input accept="text/csv" bind:files id="avatar" name="avatar" type="file" />
    </div>

</main>

  
  
{#if flags.length > 0}
    <h2>Flags</h2>

    {#each flags as item}
    <div style="border-radius: 8px 25px 0px 0px; display: inline-block; border: 1px solid yellow; margin: 15px;">
        <h4>{item[0]}</h4>
        <div>Name: <input bind:value={item[2]} type="text" class="text"></div>

        <div style="padding: 15px">
            {#if itemsArray}
                <label for="material">Choose an item:</label>
                <select bind:value={item[1]} name="material" id="material">
                {#each itemsArray as item}
                    <option value={item}>{item}</option>
                {/each}
                </select> 
            {/if}
        </div>
    </div>

    {/each}

{/if}