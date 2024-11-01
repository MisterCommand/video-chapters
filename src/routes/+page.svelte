<script>
  let videoSrc = '';
  let chapters = [
    { time: 0, name: "Start" },
    { time: 10 }
  ];
  /** @type {HTMLVideoElement} */
  let videoElement;
  let jsonInput = JSON.stringify(chapters, null, 2);

  function handleDrop(event) {
    event.preventDefault();
    const file = event.dataTransfer.files[0];
    if (file && file.type === 'video/mp4') {
      videoSrc = URL.createObjectURL(file);
    }
  }

  function handleDragOver(event) {
    event.preventDefault();
  }

  function handleFileSelect(event) {
    const file = event.target.files[0];
    if (file && file.type === 'video/mp4') {
      videoSrc = URL.createObjectURL(file);
    }
  }

  function triggerFileInput() {
    document.getElementById('fileInput')?.click();
  }

  function seekTo(time) {
    if (videoElement) {
      videoElement.currentTime = time;
      videoElement.play();
    }
  }

  function updateChapters() {
    try {
      chapters = JSON.parse(jsonInput);
    } catch (e) {
      alert('Invalid JSON');
    }
  }

  function resetChapters() {
    jsonInput = '[]';
    chapters = [];
  }
</script>

<section class="flex flex-col md:grid grid-cols-3 gap-2 h-screen">

    <div class="col-span-2 pt-4">
        {#if !videoSrc}
        <div 
        class="border-2 border-dashed border-gray-400 text-gray-200 p-6 text-center mb-4 cursor-pointer" 
        on:drop={handleDrop} 
        on:dragover={handleDragOver}
        on:click={triggerFileInput}
        role="button"
        >
        Drag and drop an MP4 file here or click to select
        </div>

        <input 
        type="file" 
        id="fileInput" 
        accept="video/mp4" 
        class="hidden" 
        on:change={handleFileSelect}
        />
        {/if}

        {#if videoSrc}
        <video controls class="w-full mx-auto mb-4" bind:this={videoElement}>
            <source src={videoSrc} type="video/mp4">
            Your browser does not support the video tag.
        </video>
        {/if}
    </div>

    <div class="flex flex-col gap-2 border-l-4 p-2 border-gray-600 pt-4">
        <textarea 
            class="w-full p-2 border-2 border-gray-400 mb-2 bg-transparent text-gray-200" 
            bind:value={jsonInput} 
            rows="5"
        ></textarea>
        <div class="flex flex-row gap-2">
            <button 
                class="bg-gray-200 text-[#181818] px-4 py-2 rounded mb-4 w-full" 
                on:click={updateChapters}
            >
                Update Chapters
            </button>
            <button 
                class="bg-gray-200 text-[#181818] px-4 py-2 rounded mb-4 w-full" 
                on:click={resetChapters}
            >
                Reset
            </button>
        </div>

        <h2 class="text-gray-200">Chapters</h2>

        {#each chapters as chapter, index}
        <button 
            class="border-gray-400 border-2 text-gray-200 px-4 py-2" 
            on:click={() => seekTo(chapter.time)}
        >
            {chapter.name ? `${chapter.name} (${chapter.time}s)` : `Chapter ${index + 1} (${chapter.time}s)`}
        </button>
        {/each}
    </div>

</section>
