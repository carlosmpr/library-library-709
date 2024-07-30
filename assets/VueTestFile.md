---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 30, 2024'
  author: 'Carlos Polanco'
  ---
  
  
  
  # VueTestFile.vue
  ## Code Explanation

This code snippet is a Vue.js component that allows users to drag and drop files or click to select files. Let's break down the key parts of the code:

### Template Section
- The template section defines the structure of the component using HTML-like syntax with Vue.js directives.
- The component has a `div` element that serves as a drop zone for files. It has event listeners for dragover and drop events that call the `handleDragOver` and `handleDrop` methods respectively.
- Inside the `div`, there is an `input` element of type file that is hidden and triggered by clicking on a label element.
- The label element displays the selected files or a message prompting the user to drag and drop files or click to select files.

### Script Section
- The script section contains the JavaScript logic for the Vue component.
- It imports two icons (`DocumentTextIcon` and `ArrowUpTrayIcon`) from the `@heroicons/vue` library.
- The component's data object contains properties like `allowedExtensions` (an array of allowed file extensions), `selectedFiles` (an array to store selected files), and `maxFiles` (maximum number of files that can be uploaded).
- The component defines several methods:
  - `handleDragOver`: Prevents the default behavior during a dragover event.
  - `handleDrop`: Handles the drop event, extracts files, and validates them.
  - `extractFiles`: Extracts files from the dropped items and validates them based on their type.
  - `traverseFileTree`: Recursively traverses a file tree to extract files.
  - `isValidFile`: Checks if a file has a valid extension.
  - `handleFileSelect`: Handles the file selection event.
  - `validateAndSetFiles`: Validates selected files and sets them in the `selectedFiles` array.

### Style Section
- The style section contains component-specific styles that are scoped to this component only.

### Example Usage
```html
<template>
  <!-- Include the file upload component -->
  <FileUploadComponent @setError="handleError" />
</template>

<script>
import FileUploadComponent from './FileUploadComponent.vue';

export default {
  components: {
    FileUploadComponent
  },
  methods: {
    handleError(errorMessage) {
      // Handle error messages from the file upload component
      console.error(errorMessage);
    }
  }
};
</script>
```

In this example, the `FileUploadComponent` is included in another Vue component, and the parent component listens for error events emitted by the `FileUploadComponent` to handle any errors that occur during file selection or upload.
  
  ## Component Code
  ```jsx
  <template>
    <div
      @dragover.prevent="handleDragOver"
      @drop.prevent="handleDrop"
      class="p-4 border-2 border-dashed border-gray-300 rounded-md text-center cursor-pointer mb-4 h-96 w-96 flex overflow-y-scroll items-center justify-center"
    >
      <input
        type="file"
        @change="handleFileSelect"
        style="display: none;"
        :accept="allowedExtensions.join(',')"
        id="fileUpload"
        multiple
      />
      <label for="fileUpload" class="cursor-pointer">
        <div v-if="selectedFiles.length">
          <div class="flex flex-wrap gap-10">
            <div v-for="file in selectedFiles" :key="file.name">
              <DocumentTextIcon class="mx-auto w-8" />
              <p>{{ file.name }}</p>
            </div>
          </div>
        </div>
        <div v-else>
          <ArrowUpTrayIcon class="mx-auto w-8" />
          <p>Drag & drop files or folders here, or click to select files</p>
        </div>
      </label>
    </div>
  </template>
  
  <script>
  import { DocumentTextIcon, ArrowUpTrayIcon } from "@heroicons/vue/24/outline";
  
  export default {
    components: {
      DocumentTextIcon,
      ArrowUpTrayIcon
    },
    data() {
      return {
        allowedExtensions: [
          ".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
          ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt",
          ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml",
          ".vue", ".svelte", ".qwik"
        ],
        selectedFiles: [],
        maxFiles: 4
      };
    },
    methods: {
      handleDragOver(event) {
        event.preventDefault();
      },
      handleDrop(event) {
        const items = event.dataTransfer.items;
        this.extractFiles(items).then(files => this.validateAndSetFiles(files));
      },
      async extractFiles(items) {
        const filesArray = [];
        for (let i = 0; i < items.length; i++) {
          const item = items[i].webkitGetAsEntry();
          if (item.isFile) {
            const file = await new Promise(resolve => item.file(resolve));
            if (this.isValidFile(file)) {
              filesArray.push(file);
            } else {
              this.$emit("setError", `Invalid file type. Only ${this.allowedExtensions.join(", ")} files are allowed.`);
            }
          } else if (item.isDirectory) {
            await this.traverseFileTree(item, filesArray);
          }
        }
        return filesArray;
      },
      traverseFileTree(item, filesArray) {
        return new Promise(resolve => {
          if (item.isFile) {
            item.file(file => {
              if (this.isValidFile(file)) {
                filesArray.push(file);
              }
              resolve();
            });
          } else if (item.isDirectory) {
            const dirReader = item.createReader();
            dirReader.readEntries(async entries => {
              for (const entry of entries) {
                await this.traverseFileTree(entry, filesArray);
              }
              resolve();
            });
          }
        });
      },
      isValidFile(file) {
        return this.allowedExtensions.some(ext => file.name.endsWith(ext));
      },
      handleFileSelect(event) {
        const files = Array.from(event.target.files);
        this.validateAndSetFiles(files);
      },
      validateAndSetFiles(files) {
        const filteredFiles = files.filter(file => this.isValidFile(file));
        if (filteredFiles.length > this.maxFiles) {
          this.$emit("setError", `You can only upload a maximum of ${this.maxFiles} files.`);
        } else {
          this.selectedFiles = filteredFiles.slice(0, this.maxFiles);
          this.$emit("setError", "");
        }
      }
    }
  };
  </script>
  
  <style scoped>
  /* Add any component-specific styles here */
  </style>
  ```
  