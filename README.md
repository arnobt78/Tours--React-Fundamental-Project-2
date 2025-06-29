# Tours Project - React Fundamental Project 2

<img width="1100" alt="Screenshot 2025-02-09 at 01 48 04" src="https://github.com/user-attachments/assets/6a741f3f-b8c8-45f1-9a38-97f30cb9b8f9" /> <img width="1100" alt="Screenshot 2025-02-09 at 01 48 15" src="https://github.com/user-attachments/assets/6a741f3f-b8c8-45f1-9a38-97f30cb9b8f9" />

---

## Project Summary

The **Tours Project** is a React-based web application designed to demonstrate key React fundamentals, including component structure, state management, API integration, and user interactions. The core functionality revolves around fetching and displaying a list of tours from an external API, allowing users to explore, learn more about, and manage their tour list. This project is ideal for those who are learning React and want a hands-on, practical example of building an interactive SPA (Single Page Application).

- **Live-Demo:** [https://tour-arnob.netlify.app/](https://tour-arnob.netlify.app/)

---

## Table of Contents

- [Project Summary](#project-summary)
- [Features](#features)
- [Project Structure](#project-structure)
- [Technology Stack](#technology-stack)
- [Installation & Running Locally](#installation--running-locally)
- [Detailed Walkthrough](#detailed-walkthrough)
  - [Component Overview](#component-overview)
  - [Data Flow](#data-flow)
  - [Key Functionalities](#key-functionalities)
  - [API Usage](#api-usage)
- [Code Examples](#code-examples)
- [Learning Outcomes & Keywords](#learning-outcomes--keywords)
- [Conclusion](#conclusion)

---

## Features

- Fetches tour data from a public API
- Displays tours with images, price, and description
- "Read more" functionality for expanded tour descriptions
- Ability to remove ("not interested") tours from the list
- Option to re-fetch all tours if the list is empty
- Loading indicator during data fetching
- Built with modular, reusable React components

---

## Project Structure

```
Tours--React-Fundamental-Project-2/
├── public/
│   └── ... (static files)
├── src/
│   ├── App.jsx         # Main application component
│   ├── Loading.jsx     # Loading spinner component
│   ├── Tour.jsx        # Single tour card component
│   ├── Tours.jsx       # List of tours component
│   ├── main.jsx        # Entry point, renders App
│   └── index.css       # Global styles
├── package.json
├── vite.config.js
└── README.md
```

---

## Technology Stack

- **React** (Functional components & Hooks)
- **Vite** (Development server & build tool)
- **JavaScript (ES6+)**
- **CSS** (Styling components)
- **Fetch API** (for data requests)
- **Netlify** (Optional: for deployment)

---

## Installation & Running Locally

### 1. Clone the repository

```sh
git clone https://github.com/arnobt78/Tours--React-Fundamental-Project-2.git
cd Tours--React-Fundamental-Project-2
```

### 2. Install dependencies

```sh
npm install
```

### 3. Start the development server

```sh
npm run dev
```

The app should now be running at [http://localhost:5173](http://localhost:5173) (default Vite port).

---

## Detailed Walkthrough

### Component Overview

- **App.jsx**: The root component. Handles data fetching, loading state, and main application logic.
- **Tours.jsx**: Receives the list of tours and renders a `Tour` component for each.
- **Tour.jsx**: Displays a single tour, with "read more" and "not interested" actions.
- **Loading.jsx**: Simple loading spinner for UX feedback during data fetch.

---

### Data Flow

1. **App.jsx** fetches tour data from an external API (`https://www.course-api.com/react-tours-project`) on mount.
2. While fetching, a loading spinner is displayed.
3. Once data is retrieved, state is updated and `Tours.jsx` is rendered.
4. `Tours.jsx` maps over the tour data array, rendering a `Tour` component for each item.
5. Each `Tour` provides options to:
   - Expand/collapse the description ("read more" toggle)
   - Remove the tour from the list ("not interested")
6. If all tours are removed, a "refresh" button appears to re-fetch the data.

---

### Key Functionalities

#### 1. Fetch & Display Tours

- Uses `useEffect` to fetch data on initial load.
- Stores tours in a state variable using `useState`.

#### 2. Remove Tour

- Each tour has a "not interested" button.
- Clicking removes the tour from the list (`setTours` with filtered array).

#### 3. Read More

- Long descriptions are truncated by default.
- "read more" toggles full description for each tour (local state in `Tour.jsx`).

#### 4. Refresh Tours

- If no tours remain, a "refresh" button calls the fetch function again, restoring the list.

---

### API Usage

- **Endpoint**: `https://www.course-api.com/react-tours-project`
- **Data Structure Example**:
  ```json
  [
    {
      "id": "rec6d6T3q5EBIdCfD",
      "name": "Best of Paris in 7 Days Tour",
      "info": "Paris is synonymous with the finest things that culture can offer ...",
      "image": "https://images.unsplash.com/photo-...",
      "price": "1,995"
    },
    ...
  ]
  ```
- **Fetching** is handled via the Fetch API within an async function.

---

## Code Examples

### App.jsx (Main Logic)

```jsx
import React, { useState, useEffect } from 'react';
import Loading from './Loading';
import Tours from './Tours';

const url = 'https://www.course-api.com/react-tours-project';

function App() {
  const [loading, setLoading] = useState(true);
  const [tours, setTours] = useState([]);

  const removeTour = (id) => {
    const newTours = tours.filter((tour) => tour.id !== id);
    setTours(newTours);
  };

  const fetchTours = async () => {
    setLoading(true);
    try {
      const response = await fetch(url);
      const tours = await response.json();
      setLoading(false);
      setTours(tours);
    } catch (error) {
      setLoading(false);
      console.log(error);
    }
  };

  useEffect(() => {
    fetchTours();
  }, []);

  if (loading) return <main><Loading /></main>;

  if (tours.length === 0) {
    return (
      <main>
        <div className='title'>
          <h2>no tours left</h2>
          <button className='btn' onClick={fetchTours}>refresh</button>
        </div>
      </main>
    );
  }

  return (
    <main>
      <Tours tours={tours} removeTour={removeTour} />
    </main>
  );
}

export default App;
```

---

### Tour.jsx (Tour Card)

```jsx
import React, { useState } from 'react';

const Tour = ({ id, image, info, name, price, removeTour }) => {
  const [readMore, setReadMore] = useState(false);
  return (
    <article className='single-tour'>
      <img src={image} alt={name} className='img' />
      <span className='tour-price'>${price}</span>
      <div className='tour-info'>
        <h5>{name}</h5>
        <p>
          {readMore ? info : `${info.substring(0, 200)}...`}
          <button className='info-btn' onClick={() => setReadMore(!readMore)}>
            {readMore ? 'show less' : '  read more'}
          </button>
        </p>
        <button className='delete-btn btn-block btn' onClick={() => removeTour(id)}>
          not interested
        </button>
      </div>
    </article>
  );
};

export default Tour;
```

---

## Learning Outcomes & Keywords

### What You'll Learn

- **React Fundamentals**: Components, props, state, hooks (`useState`, `useEffect`)
- **Component Reusability**
- **Fetching Data**: Using Fetch API in React
- **Conditional Rendering**
- **List Rendering and Key Usage**
- **Stateful and Stateless Components**
- **Basic UI/UX patterns**: Loading states, empty states, user feedback

### Keywords

`React`, `useState`, `useEffect`, `props`, `component`, `API`, `fetch`, `state`, `list rendering`, `conditional rendering`, `single page application`, `hooks`, `functional components`, `Vite`, `deployment`

---

## Conclusion

This project serves as a hands-on introduction to React's core concepts, emphasizing real-world application structure and user interaction. By working through this project, you will gain practical experience with fetching and displaying data, managing component state, and implementing dynamic UI features. The modular approach encourages reusability and clarity in code organization. Whether you are a beginner aiming to solidify your React knowledge or an instructor seeking a clear teaching example, this project provides an excellent foundation.

---
