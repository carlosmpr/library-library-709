---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # VueTestFile.vue
  ## Explanation of the Code

### Purpose
The provided code is a Vue.js component that allows users to drag and drop files or select files using a file input. It restricts the file types that can be uploaded to a predefined list of allowed file extensions and limits the number of files that can be selected. The component displays the selected files with their names and icons.

### Code Breakdown
1. **Template Section**:
   - The template section defines the HTML structure of the component.
   - It includes a `div` element that serves as the drop zone for files.
   - Inside the `div`, there is an `input` element of type file that is hidden and triggered by clicking on a label.
   - The label displays different content based on whether files are selected or not.
   - Icons from the Heroicons Vue library are used to represent files and the drop zone.

2. **Script Section**:
   - The script section contains the Vue component logic.
   - It imports two icons, `DocumentTextIcon` and `ArrowUpTrayIcon`, from the Heroicons Vue library.
   - The component's data includes an array of allowed file extensions, an array to store selected files, and a maximum number of files that can be selected.
   - Various methods are defined to handle drag over, drop, file selection, and file validation.
   - The `extractFiles` method is used to extract files from the dropped items or selected files.
   - The `traverseFileTree` method recursively traverses directories to extract files.
   - The `isValidFile` method checks if a file has a valid extension.
   - The `handleFileSelect` method is triggered when files are selected using the file input.
   - The `validateAndSetFiles` method validates selected files against the allowed extensions and maximum file limit.

3. **Style Section**:
   - The style section contains scoped CSS for the component.

### Example Usage
To use this component in a Vue.js application, you would include it in a parent component's template like so:

```html
<template>
  <div>
    <FileUploader @setError="handleError" />
  </div>
</template>

<script>
import FileUploader from './FileUploader.vue';

export default {
  components: {
    FileUploader
  },
  methods: {
    handleError(errorMessage) {
      // Handle error messages from FileUploader component
      console.error(errorMessage);
    }
  }
};
</script>
```

In this example, the `FileUploader` component is imported and used within a parent component. The parent component listens for error events emitted by the `FileUploader` component and handles them accordingly.
  
  ## Component Code
  ```jsx
  ///jsx
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
  ///
  ```
  