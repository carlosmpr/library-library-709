---
  title: 'FileSelector.tsx'
  description: 'component description'
  pubDate: 'July 28, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # FileSelector.tsx
  # FileSelector Component

The `FileSelector` component is a React functional component that allows users to select files or folders for analysis. It provides a drag-and-drop area for file selection and displays the selected files for further processing.

### Dependencies
- React: A JavaScript library for building user interfaces.
- FullScreenPopup: A custom component for displaying a full-screen popup.
- Loader: A custom component for showing a loading spinner.

### Props
- `onAnalyze`: A function that receives the analyzed data as an argument.
- `clearSelection`: A function to clear the file selection.

### State Variables
1. `files`: An array of selected files.
2. `uploading`: A boolean flag to indicate if files are being uploaded.
3. `error`: A string to store any error messages.
4. `showOverlay`: A boolean flag to show a loading overlay.

### Functions
1. `restart`: Resets the component state to its initial values.
2. `handleDragOver`: Prevents the default behavior of drag-over events.
3. `handleDrop`: Handles the dropping of files or folders into the drag-and-drop area.
4. `traverseFileTree`: Recursively traverses a file tree to extract files for upload.
5. `handleFileSelector`: Opens a file selector dialog for manual file selection.
6. `startAnalysis`: Initiates the file upload process, sends files to the server for analysis, and handles the response.

### Usage
```jsx
import React from 'react';
import FileSelector from './FileSelector';

const App = () => {
    const handleAnalyze = (data) => {
        // Process the analyzed data
    };

    const handleClearSelection = () => {
        // Clear the file selection
    };

    return (
        <div>
            <h1>File Analysis App</h1>
            <FileSelector onAnalyze={handleAnalyze} clearSelection={handleClearSelection} />
        </div>
    );
};

export default App;
```

In this example, the `FileSelector` component is used within an `App` component to allow users to select files for analysis. The `onAnalyze` and `clearSelection` functions are passed as props to the `FileSelector` component for handling the analysis results and clearing the file selection, respectively.
  
  ## Component Code
  ```jsx
  import React, { useState } from 'react';
import FullScreenPopup from './PopUp';
import Loader from './Loader/Loader';

interface FileSelectorProps {
    // eslint-disable-next-line @typescript-eslint/no-explicit-any
    onAnalyze: (data: any) => void;
    // eslint-disable-next-line @typescript-eslint/no-explicit-any
    clearSelection: any;
}

const FileSelector: React.FC<FileSelectorProps> = ({ onAnalyze, clearSelection }) => {
    const [files, setFiles] = useState<File[]>([]);
    const [uploading, setUploading] = useState<boolean>(false);
    const [error, setError] = useState<string>('');
    const [showOverlay, setShowOverlay] = useState<boolean>(false);

    const restart = () => {
        clearSelection();
        setFiles([]);
        setError('');
        setShowOverlay(false);
        setUploading(false);
    };

    const handleDragOver = (e: React.DragEvent<HTMLDivElement>) => {
        e.preventDefault();
    };

    const handleDrop = async (e: React.DragEvent<HTMLDivElement>) => {
        e.preventDefault();
        if (e.dataTransfer.items) {
            const items = Array.from(e.dataTransfer.items);
            const filesArray: File[] = [];

            for (const item of items) {
                const entry = item.webkitGetAsEntry();
                if (entry && entry.isDirectory) {
                    await traverseFileTree(entry, filesArray);
                } else if (item.kind === 'file') {
                    const file = item.getAsFile();
                    if (file) filesArray.push(file);
                }
            }

            setFiles(filesArray);
            setError('');
        }
    };

    const traverseFileTree = (item: any, fileArray: File[]) => {
        return new Promise<void>((resolve) => {
            if (item.isFile) {
                item.file((file: File) => {
                    fileArray.push(file);
                    resolve();
                });
            } else if (item.isDirectory) {
                const dirReader = item.createReader();
                dirReader.readEntries((entries: any[]) => {
                    const promises = entries.map((entry) => traverseFileTree(entry, fileArray));
                    Promise.all(promises).then(() => resolve());
                });
            }
        });
    };

    const handleFileSelector = (e: React.MouseEvent<HTMLDivElement, MouseEvent>) => {
        const input = document.createElement('input');
        input.type = 'file';
        input.multiple = true;
        input.webkitdirectory = true; // Allow selecting directories
        input.onchange = (e: Event) => {
            const target = e.target as HTMLInputElement;
            const filesArray = target.files ? Array.from(target.files) : [];
            setFiles(filesArray);
            setError('');
        };
        input.click();
    };

    const startAnalysis = async () => {
        if (files.length === 0) return;

        setUploading(true);
        setShowOverlay(true);
        const formData = new FormData();
        files.forEach((file) => {
            formData.append('files', file);
        });

        try {
            const response = await fetch('http://localhost:3000/api/analyze', {
                method: 'POST',
                body: formData,
            });
            if (!response.ok) {
                throw new Error('Failed to upload files');
            }
            const result = await response.json();
            onAnalyze(result);
            setError('');
        } catch (error) {
            console.error('Upload error:', error);
            setError('Failed to upload files. Please try again.');
        } finally {
            setUploading(false);
            setShowOverlay(false);
        }
    };

    return (
        <div className="p-4">
            {showOverlay && (
                <FullScreenPopup isOpen={showOverlay}>
                    <div className="w-full h-full flex items-center justify-center">
                        <div className="bg-white w-[40%] h-[40%] rounded-3xl flex items-center justify-center">
                            <Loader />
                            <p>Creating the Documents...</p>
                        </div>
                    </div>
                </FullScreenPopup>
            )}

            {files.length > 0 ? (
                <div>
                    <p className="text-2xl mb-2">Selected Files:</p>
                    <div className="my-4 flex gap-4 flex-wrap p-8">
                        {files.map((file) => (
                            <div key={file.name} className="flex items-center">
                                <img src="document.svg" className="w-8" />
                                <p>{file.name}</p>
                            </div>
                        ))}
                    </div>
                    <button
                        className="mt-2 bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
                        onClick={startAnalysis}
                        disabled={uploading}
                    >
                        {uploading ? 'Uploading...' : 'Create Documents'}
                    </button>

                    <button
                        className="mt-2 ml-2 bg-gray-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
                        onClick={restart}
                        disabled={uploading}
                    >
                        Search Again
                    </button>
                </div>
            ) : (
                <div
                    onClick={handleFileSelector}
                    onDragOver={handleDragOver}
                    onDrop={handleDrop}
                    className="border-4 border-dashed border-gray-400 p-10 mb-4 text-center cursor-pointer"
                    style={{ minHeight: '200px' }} // Making the drag and drop zone larger
                >
                    Drag and drop files or folders here or click to select.
                </div>
            )}
            {error && <p className="text-red-500">{error}</p>}
        </div>
    );
};

export default FileSelector;
  ```
  