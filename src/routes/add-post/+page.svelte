<script>
    import { invalidate } from '$app/navigation';

    let files = null;
    let image = "";
    let filter = "none";
    let imageFile;
    let username = "";
    let content = "";

    const applyFilter = (selectedFilter) => {
        filter = selectedFilter;
    };

    const handleImageUpload = (event) => {
        const file = event.target.files[0];
        if (file) {
            imageFile = file;
            const reader = new FileReader();
            reader.onload = (e) => {
                image = e.target.result;
            };
            reader.readAsDataURL(file);
        }
    };

    const filters = {
        none: "none",
        grayscale: "grayscale(100%)",
        sepia: "sepia(100%)",
    };

    const handleSubmit = async (event) => {
        event.preventDefault();
        const formData = new FormData();
        formData.append("image", imageFile);
        formData.append("filter", filter);
        formData.append("username", username);
        formData.append("content", content);

        console.log("Form Data:", { imageFile, filter, username, content });

        const response = await fetch('/add-post', {
            method: 'POST',
            body: formData
        });

        if (response.ok) {
            await invalidate();
            window.location.href = '/';
        } else {
            const errorData = await response.json();
            console.error('Failed to submit post:', errorData.error);
            alert('Failed to submit post: ' + errorData.error);
        }
    };
</script>

<header class="bg-white py-4 shadow-md sticky top-0 z-10">
    <div class="container mx-auto px-4 flex justify-between items-center">
        <h1 class="text-2xl font-bold font-['Comic_Sans_MS']">Craftlab</h1>
        <a href="/" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Home</a>
    </div>
</header>

<form class="container mx-auto p-5" method="POST" enctype="multipart/form-data" on:submit={handleSubmit}>
    <label for="dropzone" class="mb-3 flex flex-col items-center justify-center w-full h-64 border-2 border-gray-300 border-dashed rounded-lg cursor-pointer bg-gray-50">
        <div class="flex flex-col items-center justify-center pt-5 pb-6">
            {#if files && files.length}
                <p class="text-sm text-gray-500 font-semibold">{files[0].name}</p>
            {:else}
                <svg class="w-8 h-8 mb-4 text-gray-500" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 16"><path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 13h3a3 3 0 0 0 0-6h-.025A5.56 5.56 0 0 0 16 6.5 5.5 5.5 0 0 0 5.207 5.021C5.137 5.017 5.071 5 5 5a4 4 0 0 0 0 8h2.167M10 15V6m0 0L8 8m2-2 2 2"/></svg>
                <p class="text-sm text-gray-500 font-semibold">Click to upload</p>
            {/if}
        </div>
        <input bind:files name="image" id="dropzone" type="file" accept="image/png, image/jpeg" class="hidden" on:change={handleImageUpload} required/>
    </label>
    <div class="mb-3">
        <label for="username" class="block mb-2 text-sm font-medium text-gray-900">Username</label>
        <input bind:value={username} name="username" id="username" type="text" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5" required/>
    </div>
    <div class="mb-3">
        <label for="content" class="block mb-2 text-sm font-medium text-gray-900">Content</label>
        <textarea bind:value={content} name="content" id="content" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5" required></textarea>
    </div>

    <div class="mb-3">
        <button type="button" class="text-white bg-blue-700 hover:bg-blue-800 font-medium rounded-lg text-sm px-5 py-2.5" on:click={() => applyFilter("none")}>No Filter</button>
        <button type="button" class="text-white bg-blue-700 hover:bg-blue-800 font-medium rounded-lg text-sm px-5 py-2.5" on:click={() => applyFilter("grayscale")}>Grayscale</button>
        <button type="button" class="text-white bg-blue-700 hover:bg-blue-800 font-medium rounded-lg text-sm px-5 py-2.5" on:click={() => applyFilter("sepia")}>Sepia</button>
    </div>
    {#if image}
        <!-- svelte-ignore a11y-img-redundant-alt -->
        <img src={image} alt="Uploaded Image" style="filter: {filters[filter]}" class="mb-3" />
    {/if}
    <button type="submit" class="text-white bg-blue-700 hover:bg-blue-800 font-medium rounded-lg text-sm px-5 py-2.5">Share</button>
</form>
