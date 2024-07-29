---
  title: 'SvelteTestFile.svelte'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # SvelteTestFile.svelte
  ### Explanation of the Code

This code snippet is written in Svelte, a JavaScript framework for building user interfaces. It allows users to drag and drop files or select files through a file input field. The code includes functions to handle file validation, extraction, and selection.

#### Functions and Constructs Used:

1. **`createEventDispatcher`**: Imported from the 'svelte' library, this function creates an event dispatcher that can be used to dispatch custom events.

2. **`handleDragOver(event)`**: Prevents the default behavior of drag-over events.

3. **`handleDrop(event)`**: Handles the drop event when files are dropped into the designated area. It extracts the files and validates them.

4. **`extractFiles(items)`**: Asynchronously extracts files from the dropped items. It checks if the item is a file or directory and calls `isValidFile` to validate the file.

5. **`traverseFileTree(item, filesArray)`**: Recursively traverses a file tree to extract files from directories.

6. **`isValidFile(file)`**: Checks if a file has an allowed extension by comparing its name with the list of allowed extensions.

7. **`handleFileSelect(event)`**: Handles the file selection event from the file input field.

8. **`validateAndSetFiles(files)`**: Validates the selected files based on allowed extensions and maximum file count. It updates the `selectedFiles` array accordingly.

#### External Libraries or Dependencies:
- The code imports `createEventDispatcher` from the 'svelte' library.

#### Example Usage:
```jsx
<script>
  // Include the code snippet here
</script>

<div>
  // Include the HTML elements and Svelte directives here
</div>

<style>
  /* Add any component-specific styles here */
</style>
```

This code snippet provides a basic file upload functionality with drag-and-drop support and file validation in a Svelte component.
  
  ## Component Code
  ```jsx
  ///jsx
  <script>
  import { createEventDispatcher } from 'svelte';

  const allowedExtensions = [
    ".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
    ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt",
    ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml",
    ".vue", ".svelte", ".qwik"
  ];
  const maxFiles = 4;

  let selectedFiles = [];
  const dispatch = createEventDispatcher();

  function handleDragOver(event) {
    event.preventDefault();
  }

  async function handleDrop(event) {
    event.preventDefault();
    const items = event.dataTransfer.items;
    const filesArray = await extractFiles(items);
    validateAndSetFiles(filesArray);
  }

  async function extractFiles(items) {
    const filesArray = [];
    for (let i = 0; i < items.length; i++) {
      const item = items[i].webkitGetAsEntry();
      if (item.isFile) {
        const file = await new Promise(resolve => item.file(resolve));
        if (isValidFile(file)) {
          filesArray.push(file);
        } else {
          dispatch('setError', `Invalid file type. Only ${allowedExtensions.join(", ")} files are allowed.`);
        }
      } else if (item.isDirectory) {
        await traverseFileTree(item, filesArray);
      }
    }
    return filesArray;
  }

  function traverseFileTree(item, filesArray) {
    return new Promise(resolve => {
      if (item.isFile) {
        item.file(file => {
          if (isValidFile(file)) {
            filesArray.push(file);
          }
          resolve();
        });
      } else if (item.isDirectory) {
        const dirReader = item.createReader();
        dirReader.readEntries(async entries => {
          for (const entry of entries) {
            await traverseFileTree(entry, filesArray);
          }
          resolve();
        });
      }
    });
  }

  function isValidFile(file) {
    return allowedExtensions.some(ext => file.name.endsWith(ext));
  }

  function handleFileSelect(event) {
    const files = Array.from(event.target.files);
    validateAndSetFiles(files);
  }

  function validateAndSetFiles(files) {
    const filteredFiles = files.filter(file => isValidFile(file));
    if (filteredFiles.length + selectedFiles.length > maxFiles) {
      dispatch('setError', `You can only upload a maximum of ${maxFiles} files.`);
    } else {
      selectedFiles = [...selectedFiles, ...filteredFiles].slice(0, maxFiles);
      dispatch('setError', "");
    }
  }
</script>

<div
  on:dragover|preventDefault={handleDragOver}
  on:drop|preventDefault={handleDrop}
  class="p-4 border-2 border-dashed border-gray-300 rounded-md text-center cursor-pointer mb-4 h-96 w-96 flex overflow-y-scroll items-center justify-center"
>
  <input
    type="file"
    on:change={handleFileSelect}
    style="display: none;"
    accept={allowedExtensions.join(",")}
    id="fileUpload"
    multiple
  />
  <label for="fileUpload" class="cursor-pointer">
    {#if selectedFiles.length > 0}
      <div class="flex flex-wrap gap-10">
        {#each selectedFiles as file}
          <div>
            <DocumentTextIcon class="mx-auto w-8" />
            <p>{file.name}</p>
          </div>
        {/each}
      </div>
    {:else}
      <div>
        <ArrowUpTrayIcon class="mx-auto w-8" />
        <p>Drag & drop files or folders here, or click to select files</p>
      </div>
    {/if}
  </label>
</div>

<style>
/* Add any component-specific styles here */
</style>
  ///
  ```
  