---
  title: 'TreeView.tsx'
  description: 'component description'
  pubDate: 'July 28, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # TreeView.tsx
  # TreeView Component

The `TreeView` component is a React functional component that displays a list of files in a tree-like structure. It allows the user to select a file by clicking on it and propagates the selected file upwards through a callback function provided as a prop.

## Dependencies
- React: A JavaScript library for building user interfaces.

## Props
- `files`: An array of strings representing the list of files to be displayed in the tree view.
- `onSelectFile`: A function that takes a string parameter (file) and is called when a file is selected. It is used to propagate the selected file upwards to the parent component.

## State
- `selectedFile`: A state variable managed by `useState` hook to keep track of the currently selected file.

## Functions
- `handleFileSelect`: A function that updates the `selectedFile` state with the selected file and calls the `onSelectFile` callback function with the selected file as an argument.

## Usage
```jsx
import React from 'react';
import TreeView from './TreeView';

const files = ['folder1/file1.txt', 'folder2/file2.txt', 'folder3/file3.txt'];

const App = () => {
  const handleFileSelection = (file) => {
    console.log(`Selected file: ${file}`);
    // Perform actions with the selected file
  };

  return (
    <div>
      <h1>File Tree View</h1>
      <TreeView files={files} onSelectFile={handleFileSelection} />
    </div>
  );
};

export default App;
```

In the example above, the `TreeView` component is imported and used within the `App` component. The `files` array contains a list of file paths that are passed as a prop to the `TreeView` component. The `handleFileSelection` function logs the selected file to the console, but in a real application, it would perform actions based on the selected file.

When a file is clicked in the tree view, the `handleFileSelect` function is called, updating the selected file state and propagating the selected file upwards through the `onSelectFile` callback function.
  
  ## Component Code
  ```jsx
  import React, { useState } from 'react';

interface TreeViewProps {
  files: string[];
  onSelectFile: (file: string) => void;
}

const TreeView: React.FC<TreeViewProps> = ({ files, onSelectFile }) => {
  const [selectedFile, setSelectedFile] = useState<string>('');

  const handleFileSelect = (file: string) => {
    setSelectedFile(file);  // Update the selected file state
    onSelectFile(file);  // Propagate the selection upwards
  };

  return (
    <div className="flex flex-wrap-reverse flex-row">
      {files.map((file, index) => {
        const lastPart = file.split('/').pop();  // Extract the last part of the path after the last '/'
        const isSelected = file === selectedFile;  // Check if this file is the selected file

        return (
          <div 
            className={`flex flex-col cursor-pointer gap-0 justify-center p-2.5 font-bold rounded-t-lg border-t border-r border-l border-solid border-neutral-200 
                        ${isSelected ? 'bg-white' : 'bg-zinc-100'}`}  // Conditional class for background color
            key={index} 
            onClick={() => handleFileSelect(file)}
          >
            <p>{lastPart}</p>  
          </div>
        );
      })}
    </div>
  );
};

export default TreeView;
  ```
  