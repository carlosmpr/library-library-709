---
  title: 'PopUp.tsx'
  description: 'component description'
  pubDate: 'July 28, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # PopUp.tsx
  # FullScreenPopup Component

The `FullScreenPopup` component is a React functional component that displays a full-screen popup when the `isOpen` prop is true. It takes three props: `isOpen`, `onClose`, and `children`.

- `isOpen`: A boolean prop that determines whether the popup should be displayed or not.
- `onClose`: A function prop that is called when the close button is clicked.
- `children`: Represents the content that will be displayed inside the popup.

## Code Explanation

- The component first checks if the `isOpen` prop is false, and if so, it returns `null`, effectively not rendering anything.
- If `isOpen` is true, the component renders a full-screen popup with a semi-transparent black background and a centered content box.
- The content box has a close button (`&times;`) at the top right corner, which triggers the `onClose` function when clicked.
- The `children` prop is rendered inside the content box, allowing for dynamic content within the popup.

## Dependencies

- The code does not rely on any external libraries or dependencies.

## Example Usage

```jsx
import React, { useState } from 'react';
import FullScreenPopup from './FullScreenPopup';

const App = () => {
  const [isOpen, setIsOpen] = useState(false);

  const handleClose = () => {
    setIsOpen(false);
  };

  return (
    <div>
      <button onClick={() => setIsOpen(true)}>Open Popup</button>
      <FullScreenPopup isOpen={isOpen} onClose={handleClose}>
        <h1>Hello, I am a Full Screen Popup!</h1>
      </FullScreenPopup>
    </div>
  );
};

export default App;
```

In this example, clicking the "Open Popup" button will display the `FullScreenPopup` component with the content "Hello, I am a Full Screen Popup!". The popup can be closed by clicking the close button.
  
  ## Component Code
  ```jsx
  /* eslint-disable @typescript-eslint/no-explicit-any */


const FullScreenPopup = ({ isOpen, onClose, children }:any) => {
  if (!isOpen) return null;

  return (
  
        <div className="w-full fixed inset-0 z-50 flex justify-center items-center bg-black bg-opacity-50 transition-opacity duration-300 ease-out p-2 sm:p-4" style={{zIndex:900}}>
          <div className="w-[90%]  h-full   rounded-lg shadow-lg  transform transition-all duration-300 ease-out scale-95 opacity-0 animate-fadeInScale overflow-y-scroll example">
            <button onClick={onClose} className="fixed text-5xl top-10 right-1/4 m-2 text-2xl  rounded-full text-white flex items-center justify-center ">&times;</button>
            {children}
          </div>
        </div>
   
  );
};

export default FullScreenPopup;
  ```
  