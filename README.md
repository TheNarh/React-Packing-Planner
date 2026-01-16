## Why This Project?
* This project demonstrates how to:

* Lift and manage shared state in React

* Build reusable, composable components

* Handle derived state without unnecessary re-renders

* Apply real-world UI logic (sorting, toggling, bulk actions)

* Write readable, maintainable React code

## Tech Stack 
* React 19

* JavaScript (ES6+)

* CSS (custom styling)

* Create React App

## Application Architecture
App (state owner)
│
├── Logo (presentational)
├── Form (controlled inputs → emits new items)
├── PackingList
│   ├── Item (stateless, reusable)
│   └── Sorting & list actions
└── Stats (derived state)

## Key React Concepts Demonstrated
* Lifting State Up
  * Shared state (items) is owned by App and passed down via props.

* Controlled Components
  * The form inputs are fully controlled using useState.

* Derived State
  * Packing statistics are calculated on the fly instead of stored.

* Immutability
  * State updates use map, filter, and spread operators.

* Single Responsibility Components
  * Each component does exactly one thing.
 
## Features
* Add items with quantity

* Mark items as packed/unpacked

* Sort items by:

  * Input order

  * Description

  * Packed status

* Clear entire list with confirmation

* Live packing statistics

## Screenshots
<img width="1919" height="930" alt="Packing List" src="https://github.com/user-attachments/assets/ded479d3-2cd0-4b46-920c-bef397bcfe0d" />

## Getting Started
* npm install

* npm start

## Further Improvements
Since this is a learning project, the main aim was to grasp the fundamental concepts of react and use them in practice. However, as time goes on I would add localStorage, add an edit functionality, improve accessibility(ARIA labels) and migrate to Vite for faster builds.

